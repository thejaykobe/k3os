ARG ALPINE_VERSION=3.23
FROM python:3.12.13-alpine${ALPINE_VERSION} AS builder

ARG OCI_CLI_VERSION=3.89.2
RUN apk add --no-cache build-base libffi-dev openssl-dev cargo rust

# Install into a self-contained venv at the final runtime path.
# --copies materializes the Python interpreter inside the venv so the
# shebangs in oci-cli scripts resolve at runtime in the k3os image.
RUN python -m venv --copies /usr/bin/oci-cli
ENV PATH=/usr/bin/oci-cli/bin:$PATH
RUN pip install --no-cache-dir --upgrade pip wheel setuptools
RUN pip install --no-cache-dir oci-cli==${OCI_CLI_VERSION}

RUN /usr/bin/oci-cli/bin/oci --version

# Convenience symlink that 03-base will copy into /bin
RUN mkdir -p /oci-cli-bin && ln -sf /usr/bin/oci-cli/bin/oci /oci-cli-bin/oci

# Trim build artifacts to reduce final image size
RUN find /usr/bin/oci-cli -type d -name __pycache__ -prune -exec rm -rf {} + ; \
    find /usr/bin/oci-cli -name '*.pyc' -delete ; \
    find /usr/bin/oci-cli -name '*.pyo' -delete ; true
