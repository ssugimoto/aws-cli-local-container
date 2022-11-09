FROM mcr.microsoft.com/vscode/devcontainers/python:3.8

# see, https://gist.github.com/gsweene2/aa4229c163d500965e5674ee4418bf7a
# see, https://gist.github.com/ssugimoto/132e0785212be58b0c0a6b4b7b42bc91


################################
# Install apt
# aws-cli
################################
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get -y install --no-install-recommends java-common zip \
  && wget https://corretto.aws/downloads/latest/amazon-corretto-8-x64-linux-jdk.deb \
  && dpkg --install amazon-corretto-8-x64-linux-jdk.deb \
  && wget https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip \
  && unzip awscli-exe-linux-x86_64.zip \
  && ./aws/install \
  # Clean up
  && rm -f awscli-exe-linux-x86_64.zip \
  && rm -f amazon-corretto-8-x64-linux-jdk.deb \
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/*
ENV DEBIAN_FRONTEND=dialog

################################
# Install nvm with node and npm
################################
ENV NVM_DIR /usr/local/share/nvm
ENV NODE_VERSION 16.18.1
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash \
  && . $NVM_DIR/nvm.sh \
  && nvm install $NODE_VERSION \
  && nvm alias default $NODE_VERSION \
  && nvm use default

################################
# Install Terraform
################################
# Download terraform for linux
RUN wget https://releases.hashicorp.com/terraform/1.3.4/terraform_1.3.4_linux_amd64.zip
# Unzip
RUN unzip terraform_1.3.4_linux_amd64.zip
# Move to local bin
RUN mv terraform /usr/local/bin/
# Check that it's installed
RUN terraform --version 

WORKDIR /workspaces
