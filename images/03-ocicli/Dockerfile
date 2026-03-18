ARG ALPINE_VERSION=3.21
FROM python:3.12.11-alpine${ALPINE_VERSION} AS builder

ARG OCI_CLI_VERSION=3.76.0
RUN apk add --no-cache git unzip groff build-base libffi-dev cmake
RUN git clone --single-branch --depth 1 -b ${OCI_CLI_VERSION} https://github.com/oracle/oci-cli
WORKDIR oci-cli
RUN python -m venv venv
RUN . venv/bin/activate
COPY requirements.txt .
RUN pip install -r requirements.txt
RUN scripts/install/install.py
