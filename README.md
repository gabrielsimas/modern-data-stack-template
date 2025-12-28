# 🚀 Modern Data Stack Platform (Agnóstica)

Este repositório contém a infraestrutura base para um Lakehouse moderno. O objetivo é fornecer um "motor" de dados centralizado que pode ser utilizado por múltiplos projetos simultâneos, garantindo isolamento de dados e eficiência de recursos.

---

## 📂 Estrutura de Diretórios Recomendada

Para utilizar esta plataforma, organize seu diretório de trabalho da seguinte forma:

```text
/home/usuario/
  ├── modern-data-stack-template/  <-- (Este repositório)
  │   ├── docker-compose.yaml
  │   └── infra_data/              <-- (Persistência da Infra)
  │
  └── projects/                    <-- (Seus projetos/MVPs)
      └── shopping-list/
          ├── .env                 <-- (Configurações do projeto)
          ├── dags/                <-- (Orquestração)
          ├── keys/                <-- (Service Accounts .json)
          └── plugins/
```
---
## 🛠️ Configuração do Ambiente de Infra

### **1. Permissões nos Volumes**
Execute estes comandos para garantir que os containers tenham permissão de escrita nas pastas de dados:

```console
mkdir -p infra_data/postgres_data infra_data/dremio_data infra_data/portainer_data
sudo chown -R 999:999 infra_data/postgres_data
sudo chown -R root:root infra_data/dremio_data
sudo chown -R root:root infra_data/portainer_data
```

## 🔌 Configuração de Projetos (Clientes)

### **1. Preparação das Pastas do Projeto**
Na pasta do seu projeto, crie os diretórios necessários e ajuste as permissões para o Airflow:

```console
mkdir -p dags plugins keys
sudo chown -R 50000:0 dags plugins keys
sudo chmod -R 775 dags plugins keys
```
## 🚀 Como Iniciar

A partir da pasta da **infraestrutura**, execute o comando apontando para o arquivo de ambiente do seu **projeto**:

```console
docker compose --env-file ../projects/seu-projeto/.env up -d
```
---
## 📋 Endpoints da Plataforma
|Serviço    | Porta   | Descrição                          |
| ---       | ---     | ---                                |
| Airflow   | `8080`  | Orquestração de Pipelines (DAGs)   |
| Dremio    | `9047`  | Motor de Query e Virtualização     |
| Nessie    | `19120` | Catálogo de Dados (Versionamento)  |
| Portainer | `9000`  | Gestão Visual de Containers        |
| Superset  | `8088`  | BI e Dashboards                    |
---
## **Recomendações para o Ambiente**

### **Permissões nas pastas para os Volumes**
```console
mkdir -p infra_data/postgres_data infra_data/dremio_data infra_data/portainer_data
sudo chown -R 999:999 infra_data/postgres_data
sudo chown -R root:root infra_data/dremio_data
sudo chown -R root:root infra_data/portainer_data
```
---
### **Criação dos Bancos de Dados: Nessie e Superset**
### 🦕 **Nessie**
```console
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "CREATE DATABASE nessie;"
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "CREATE USER nessie WITH PASSWORD 'nessie';"
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "GRANT ALL PRIVILEGES ON DATABASE nessie TO nessie;"
```
### 📊 **Superset**
```console
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "CREATE DATABASE superset;"
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "CREATE USER superset WITH PASSWORD 'superset';"
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -c "GRANT ALL PRIVILEGES ON DATABASE superset TO superset;"
docker exec -it mvp-shopping-control-list-postgres-1 psql -U airflow -d superset -c "GRANT ALL ON SCHEMA public TO superset;"
```
---
## **Recomendações para os clientes**
### Na pasta do seu projeto você precisa criar esses 3 diretórios:
#### dags: para as dags do Airflow
#### plugins: para os plugins do Airflow
#### keys: Para as chaves das Services Accounts ou IAMs geradas para permissão
```console
sudo chown -R 50000:0 dags plugins keys
sudo chmod -R 775 dags plugins keys
```
---
### **.env no Projeto local**
#### Seu arquivo `.env` precisa ter as seguintes variáveis:
```console
BUCKET_LANDING
BUCKET_BRONZE
BUCKET_PRATA
BUCKET_OURO
SA_AIRFLOW_FILE=sa-airflow.json
SA_DREMIO_FILE=sa-dremio.json
PROJECT_PATH
```
### **Como subir o projeto**
#### Dentro do diretório do seu projeto, rode esse comando:
```console
docker compose -f ~/data-platform/docker-compose.yml --env-file .env up -d
```