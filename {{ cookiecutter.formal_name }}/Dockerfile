# Use Ubuntu 18.04 (bionic) as a base image. This ensures that we're using an
# old version of libc, maximizing the compatibility of the image when it is
# included in an AppImage.
FROM ubuntu:18.04

# Set the working directory
WORKDIR /app

# Set up Docker build arguments
ARG PY_VERSION
ARG SYSTEM_REQUIRES

# Install the deadsnakes PPA so we can get arbitrary Python versions
RUN apt-get update -y && \
    apt-get install -y \
        software-properties-common \
        dirmngr \
        apt-transport-https \
        lsb-release \
        ca-certificates
RUN apt-add-repository ppa:deadsnakes/ppa

# Install git, Python, and any packages required
RUN apt-get update -y && \
    apt-get install -y \
        git \
        python${PY_VERSION}-dev \
        python${PY_VERSION}-venv \
        ${SYSTEM_REQUIRES}

# Install (and update) pip, disabling the global cache
RUN python${PY_VERSION} -m ensurepip
RUN python${PY_VERSION} -m pip config set global.cache-dir false
RUN python${PY_VERSION} -m pip install --upgrade pip
RUN python${PY_VERSION} -m pip install --upgrade setuptools
RUN python${PY_VERSION} -m pip install --upgrade wheel

# Ensure Docker user UID:GID matches host user UID:GID (beeware/briefcase#403)
# Use --non-unique to avoid problems when the UID:GID of the host user
# collides with entries provided by the Docker container.
ARG HOST_UID
ARG HOST_GID
RUN groupadd --non-unique --gid $HOST_GID briefcase
RUN useradd --non-unique --uid $HOST_UID --gid $HOST_GID brutus --home /home/brutus
USER brutus
