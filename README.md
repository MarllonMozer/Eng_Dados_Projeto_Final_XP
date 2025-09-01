# 🚀 Projeto Final: Pipeline ETL com Kafka, PostgreSQL e Amazon S3

## 📋 Descrição do Projeto

Este projeto implementa um pipeline ETL completo para ingerir, processar e armazenar dados públicos do Tesouro Direto (Tesouro Nacional), utilizando Apache Kafka, PostgreSQL, Kafka Connect e Apache Spark. Os dados são processados em camadas (Bronze, Silver e Gold) e armazenados em um data lake no Amazon S3.

---

## 🎯 Objetivos

- Consumir dados brutos de um arquivo CSV via URL.
- Ingerir dados na camada **Bronze** (PostgreSQL).
- Transmitir dados para o Kafka usando conectores JDBC.
- Enviar dados do Kafka para o Amazon S3 usando Kafka Connect S3.
- Processar os dados com Apache Spark nas camadas **Silver** (limpeza) e **Gold** (agregação).
- Armazenar os resultados finalizados no S3.

---

## 🛠️ Tecnologias Utilizadas

- **Docker** e **docker-compose**
- **Apache Kafka** (Zookeeper, Broker, Schema Registry, Kafka Connect)
- **PostgreSQL**
- **Amazon S3**
- **Apache Spark** (com PySpark)
- **Jupyter Notebook**
- **Kafka Connect JDBC** e **S3 Connectors**

---

---

## 🚀 Como Executar

### 1. Pré-requisitos

- Docker e docker-compose instalados
- Conta AWS com acesso ao S3
- Jupyter Notebook (opcional, se preferir executar localmente)

### 2. Configuração do Ambiente

#### 🔑 Variáveis de Ambiente

Crie um arquivo `.env_kafka_connect` na raiz do projeto com as credenciais AWS:

```bash
AWS_ACCESS_KEY_ID=sua_access_key
AWS_SECRET_ACCESS_KEY=sua_secret_access_key

🪣 Criar Buckets S3
Crie dois buckets no S3 (ex: seu-bucket-01 e seu-bucket-02) na região us-east-1.

Ajuste os arquivos em connectors/sink/ com os nomes dos buckets:

cd custom-kafka-connectors-image
docker build -t connect-custom:1.0.0 .

#### subir o docker compose
docker-compose up -d

7. Criar Tópicos no Kafka
Acesse o container do broker:

bash
docker exec -it broker bash

kafka-topics --create \
--bootstrap-server localhost:9092 \
--partitions 1 \
--replication-factor 1 \
--topic postgres-dadostesouroipca

kafka-topics --create \
--bootstrap-server localhost:9092 \
--partitions 1 \
--replication-factor 1 \
--topic postgres-dadostesouropre

8. Registrar Conectores Source (JDBC)
Na raiz do projeto:

curl -X POST -H "Content-Type: application/json" \
--data @connect_jdbc_postgres_ipca.config \
http://localhost:8083/connectors

curl -X POST -H "Content-Type: application/json" \
--data "@connect_jdbc_postgres_pre.config" \
http://localhost:8083/connectors

11. Configurar Permissões do S3
Adicione a seguinte política em cada bucket no console da AWS (substitua pelo seu ARN de usuário):

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowWriteAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/seuusuario"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket-01",
        "arn:aws:s3:::my-bucket-01/*"
      ]
    }
  ]
}