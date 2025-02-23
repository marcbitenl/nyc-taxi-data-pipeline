# 🚀 Pipeline de Dados com Azure Data Factory, Databricks e Delta Lake

## 📌 Visão Geral do Projeto

Este projeto demonstra a construção de um pipeline completo de dados para processar informações de corridas de táxi da cidade de Nova York. As etapas incluem:

- **Azure Data Factory (ADF)** para extração dinâmica de dados diretamente de um site público.
- **Azure Data Lake Storage (ADLS Gen2)** para armazenamento dos dados em camadas (Bronze, Silver e Gold).
- **Azure Databricks** para transformação e otimização dos dados utilizando o formato Delta Lake.
- **Unity Catalog** para gerenciar acessos e configurar locais externos de maneira segura.

O pipeline automatiza a coleta de arquivos Parquet, realiza a transformação dos dados no Databricks e armazena os resultados em Delta Lake seguindo uma arquitetura em camadas. Além disso, o projeto demonstra boas práticas em governança de dados, controle de acesso e automação de fluxos de trabalho.

---

## ⚙️ Tecnologias Utilizadas

- **Azure Data Factory (ADF)**
- **Azure Data Lake Storage Gen2**
- **Azure Databricks** (com Delta Lake e PySpark)
- **Unity Catalog**
- **GitHub** para controle de versão
- **OAuth 2.0** para autenticação
- **PySpark** para transformação de dados em larga escala
- **SQL** para consultas e manipulação de dados estruturados

---

## 🚀 Como o Projeto Foi Desenvolvido

### 🔄 **1. Extração de Dados com Azure Data Factory (ADF)**

O ADF foi configurado para buscar dados dinamicamente de uma URL pública, automatizando a extração de arquivos mensais de corridas de táxi. A configuração incluiu:

1. Criação de um pipeline com um loop **ForEach** para iterar pelos 12 meses do ano.
2. Configuração de uma condição (**IfCondition**) para ajustar o formato do mês:
   - Para meses de 1 a 9, adicionou-se um zero à esquerda na URL.
   - Para meses de 10 a 12, o número do mês foi utilizado diretamente.
3. Configuração de uma atividade de **Copy Data** para transferir os arquivos diretamente para o Azure Data Lake (camada Bronze).
4. Automação do agendamento das execuções para garantir atualizações periódicas dos dados.

### 🔒 **2. Processamento de Dados no Azure Databricks**

Após a extração, os dados foram processados no Databricks, onde ocorreram as seguintes etapas:

1. Leitura dos dados armazenados na camada Silver:

```python
df_zone = spark.read.format('parquet')\
                .option('inferSchema', True)\
                .option('header', True)\
                .load('<caminho_da_camada_silver>')
```

2. Transformação e limpeza dos dados usando PySpark:
   - Conversão de tipos de dados.
   - Remoção de valores nulos e duplicados.
   - Padronização de colunas e normalização de dados.

3. Salvamento dos dados processados em formato Delta na camada Gold:

```python
df_zone.write.format('delta')\
    .mode('append')\
    .option('path', '<caminho_da_camada_gold>')\
    .save()
```

4. Criação de tabelas otimizadas para análises futuras, com particionamento e indexação dos dados.

### 📁 **3. Gerenciamento de Acesso com Unity Catalog**

Para garantir o acesso seguro aos dados:

1. Configuração de uma **Storage Credential**.
2. Criação de uma **External Location** para armazenar os dados no Data Lake.
3. Registro das tabelas no metastore do Databricks para facilitar a análise:

```sql
CREATE TABLE IF NOT EXISTS gold.trip_zone
USING DELTA
LOCATION '<caminho_da_camada_gold>';
```

4. Definição de permissões de leitura e escrita por usuário e grupo, assegurando o controle de acesso granular.

---

## 📂 Estrutura do Projeto

```
├── data_extraction/        # Configurações e pipelines do ADF
├── data_transformation/    # Notebooks de transformação de dados no Databricks
├── scripts/                # Scripts de automação e suporte
├── notebooks/              # Exportação dos notebooks do Databricks
├── tests/                  # Scripts de testes de integridade dos dados
├── documentation/          # Documentação técnica do projeto
├── README.md               # Documentação do projeto
├── .gitignore              # Arquivos a serem ignorados no controle de versão
```

---

## ✅ Principais Funcionalidades

- Extração automatizada de dados dinâmicos diretamente de uma URL pública usando ADF.
- Transformação e validação de dados com PySpark no Databricks.
- Armazenamento otimizado em Delta Lake para análises rápidas e eficientes.
- Controle de acesso seguro utilizando Unity Catalog e locais externos configurados.
- Implementação de testes de integridade para garantir a qualidade dos dados processados.
- Automatização de fluxos de trabalho, com agendamentos periódicos e notificações de status.

---

## 📊 Resultados Esperados

- Tabelas Delta registradas no metastore do Databricks, prontas para consultas analíticas avançadas.
- Dados organizados e disponíveis no Azure Data Lake Storage (Camada Gold), prontos para análises e relatórios.
- Relatórios automatizados gerados diretamente a partir da camada Gold.
- Otimização do tempo de processamento e redução de custos operacionais por meio de particionamento e caching eficiente.

---

