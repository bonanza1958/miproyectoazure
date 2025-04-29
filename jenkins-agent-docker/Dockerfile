FROM jenkins/inbound-agent:latest

USER root

# Instalar Docker CLI
RUN apt-get update && \
    apt-get install -y docker.io curl ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Instalar kubectl (última versión estable)
RUN curl -sLO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl


