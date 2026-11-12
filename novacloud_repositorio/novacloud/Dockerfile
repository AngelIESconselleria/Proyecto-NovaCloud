# Entorno de ejecución de NovaCloud (el mismo que se prepara en ans01)
FROM python:3.12-slim
ENV PIP_NO_CACHE_DIR=1 ANSIBLE_COLLECTIONS_PATH=/opt/collections
RUN apt-get update \
 && apt-get install -y --no-install-recommends git openssh-client ca-certificates \
 && rm -rf /var/lib/apt/lists/*
COPY requirements.txt requirements.yml /tmp/
RUN pip install -r /tmp/requirements.txt \
 && ansible-galaxy collection install -r /tmp/requirements.yml -p /opt/collections
RUN useradd --create-home --uid 1000 ansible
USER ansible
WORKDIR /home/ansible/novacloud
CMD ["bash"]
