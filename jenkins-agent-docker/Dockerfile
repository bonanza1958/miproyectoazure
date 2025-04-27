FROM jenkins/inbound-agent:latest

USER root

RUN apt-get update && \
    apt-get install -y docker.io && \
    apt-get clean

# Opcional: instalar kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl

USER jenkins
