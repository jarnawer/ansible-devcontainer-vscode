#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------
FROM ubuntu:bionic
LABEL maintainer="@imjoseangel"
# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    apt-utils \
    software-properties-common \
    build-essential \
    jq \
    git \
    python3 \
    python3-dev \
    python3-pip && \
    rm -rf /var/lib/apt/lists/*

RUN add-apt-repository universe \
    && add-apt-repository multiverse \
    && apt update

RUN pip3 install --upgrade \
    setuptools \
    pip

ADD requirements.txt /requirements.txt
ADD azrequirements.txt /azrequirements.txt

RUN pip3 install --upgrade -r /requirements.txt
RUN pip3 install --upgrade -r /azrequirements.txt

RUN ln -s /usr/bin/python3 /usr/bin/python \
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support for the non-root user
    && apt-get install sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Clean up
    && apt-get autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


