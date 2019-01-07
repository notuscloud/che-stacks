FROM debian:9.6 as hashicorp

ENV ARCH=amd64

# Install Hashicorp required packages
RUN apt update && apt install -y wget unzip

# Hashicorp tools
ENV CONSUL_VERSION=1.4.0
ENV VAULT_VERSION=1.0.1
ENV TERRAFORM_VERSION=0.11.11

ENV HASHICORP_RELEASES=https://releases.hashicorp.com

# Install Hashicorp consul
WORKDIR /var/tmp
RUN set -eux; \
    mkdir consul && \
    cd consul && \
    wget ${HASHICORP_RELEASES}/consul/${CONSUL_VERSION}/consul_${CONSUL_VERSION}_linux_${ARCH}.zip && \
    unzip -d /bin consul_${CONSUL_VERSION}_linux_${ARCH}.zip && \
    cd .. && \
    rm -rf consul && \
    consul version

# Install Hashicorp vault
RUN set -eux; \
    mkdir vault && \
    cd vault && \
    wget ${HASHICORP_RELEASES}/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_linux_${ARCH}.zip && \
    unzip -d /bin vault_${VAULT_VERSION}_linux_${ARCH}.zip && \
    cd .. && \
    rm -rf vault && \
    vault version

# Install Hashicorp terraform
RUN set -eux; \
    mkdir terraform && \
    cd terraform && \
    wget ${HASHICORP_RELEASES}/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip && \
    unzip -d /bin terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip && \
    cd .. && \
    rm -rf terraform && \
    terraform version
    
FROM debian:9.6

MAINTAINER thomas@notuscloud.fr

# Fetch Hashicorp binairies from the previous build stage
COPY --from=hashicorp /bin/terraform /bin/.
COPY --from=hashicorp /bin/consul /bin/.
COPY --from=hashicorp /bin/vault /bin/.

# Add some packages
RUN apt update && \ 
    apt install -y vim procps curl zsh git wget unzip openjdk-8-jre-headless gnupg gnupg2 gnupg1

# Add ansible user
RUN useradd ansible -s /bin/bash -b /home -m

# Install ansible
RUN echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" >> /etc/apt/sources.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367 && \
    apt-get update && apt-get -y install ansible && ansible --version

# CMD
CMD tail -f /dev/null