# syntax=docker/dockerfile:1.7

# Actualizado a Airflow 3.2.2
ARG AIRFLOW_IMAGE=apache/airflow:3.2.2-python3.12
FROM ${AIRFLOW_IMAGE}

# ----- Proxies opcionales para el build -----
ARG HTTP_PROXY
ARG HTTPS_PROXY
ENV http_proxy=${HTTP_PROXY} \
    https_proxy=${HTTPS_PROXY}

USER root

# Paquetes de sistema necesarios y utilidades
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      libaio1 \
      libnsl2 \
      unzip \
      wget \
      ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# ----- Oracle Instant Client 23.4 (básico) -----
ENV ORACLE_BASE=/opt/oracle \
    ORACLE_CLIENT_VER=23_4

RUN mkdir -p ${ORACLE_BASE} && \
    cd ${ORACLE_BASE} && \
    wget -O instantclient.zip \
      https://download.oracle.com/otn_software/linux/instantclient/2340000/instantclient-basic-linux.x64-23.4.0.24.05.zip && \
    unzip instantclient.zip && rm -f instantclient.zip && \
    echo ${ORACLE_BASE}/instantclient_${ORACLE_CLIENT_VER} > /etc/ld.so.conf.d/oracle-instantclient.conf && \
    ldconfig

ENV ORACLE_HOME=${ORACLE_BASE}/instantclient_${ORACLE_CLIENT_VER} \
    LD_LIBRARY_PATH=${ORACLE_BASE}/instantclient_${ORACLE_CLIENT_VER} \
    PATH=${ORACLE_BASE}/instantclient_${ORACLE_CLIENT_VER}:$PATH

# Zona horaria
ENV TZ=America/Lima
RUN ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime

# Limpiar proxies para no dejarlos persistentes
ENV http_proxy= \
    https_proxy=

# ----- Dependencias Python -----
USER airflow
WORKDIR /opt/airflow

# Actualizar pip primero
RUN pip install --no-cache-dir --upgrade pip

# Instalar providers compatibles con Airflow 3.2.2
# Providers base que ya tenías
RUN pip install --no-cache-dir \
  apache-airflow-providers-oracle \
  apache-airflow-providers-telegram \
  apache-airflow-providers-openai \
  apache-airflow-providers-airbyte \
  apache-airflow-providers-apache-spark \
  apache-airflow-providers-yandex \
  apache-airflow-providers-git \
  ldap3 \
  oracledb \
  clickhouse-connect \
  xmltodict

# Providers adicionales recomendados para Analytics e Ing. de Datos
# Descomenta los que necesites:

# Data Warehouses & Databases
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-snowflake \
#   apache-airflow-providers-databricks \
#   apache-airflow-providers-postgres \
#   apache-airflow-providers-mysql \
#   apache-airflow-providers-trino \
#   apache-airflow-providers-presto

# Cloud Providers
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-amazon \
#   apache-airflow-providers-google \
#   apache-airflow-providers-microsoft-azure

# Data Processing & Transformation
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-apache-beam \
#   apache-airflow-providers-apache-flink \
#   apache-airflow-providers-dbt-cloud \
#   apache-airflow-providers-apache-hive

# Streaming & Messaging
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-apache-kafka \
#   apache-airflow-providers-redis

# AI/ML & Vector Databases
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-pinecone \
#   apache-airflow-providers-weaviate \
#   apache-airflow-providers-pgvector

# Data Lineage & Quality
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-openlineage \
#   apache-airflow-providers-common-sql

# File Transfer & APIs
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-sftp \
#   apache-airflow-providers-http

# Notebooks
# RUN pip install --no-cache-dir \
#   apache-airflow-providers-papermill

# Usuario final
USER airflow

