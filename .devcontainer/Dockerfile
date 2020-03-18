#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

# Note: You can use any Debian/Ubuntu based image you want. 
FROM ubuntu:bionic

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Docker Compose version
ARG COMPOSE_VERSION=1.24.0

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Verify git, process tools installed
    && apt-get -y install git iproute2 procps \
    #
    && apt-get install -y apt-transport-https ca-certificates sudo curl gnupg-agent software-properties-common lsb-release \
    #
    # Install Live Share dependencies
    && curl -sSL -o vsls-linux-prereq-script.sh https://aka.ms/vsls-linux-prereq-script \
    && /bin/bash vsls-linux-prereq-script.sh true false false \
    && rm vsls-linux-prereq-script.sh \
    #
    # Install Git LFS
    && curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash \
    && apt-get install -yq git-lfs \
    && git lfs install \
    #
    # Install Docker CE CLI
    && curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | (OUT=$(apt-key add - 2>&1) || echo $OUT) \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install -y docker-ce-cli \
    #
    # Install Docker Compose
    && curl -sSL "https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose \
    #
    # Setup vsonline user
    && useradd --create-home --shell /bin/bash vsonline \
    && mkdir -p /etc/sudoers.d \
    && echo "vsonline ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd \
    && echo "Defaults secure_path=\"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/bin:${EXTRA_PATHS}\"" >> /etc/sudoers.d/securepath \
    && sudo -u vsonline mkdir /home/vsonline/.vsonline \
    #
    # Install nvm (popular Node.js version-management tool)
    # && sudo -u vsonline curl -s -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | sudo -u vsonline bash 2>&1 \
    # && rm -rf /home/vsonline/.nvm/.git \
    #
    # Tactically remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
    # Can leave in image once the CVE is resolved upstream or the node images move to "buster".
    && apt-get purge -y imagemagick imagemagick-6-common \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=dialog

USER vsonline
