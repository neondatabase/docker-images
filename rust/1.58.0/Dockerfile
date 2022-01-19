FROM rust:1.58.0-slim-buster

# Change default shell for RUN from Dash to Bash
SHELL ["/bin/bash", "-exo", "pipefail", "-c"]

ENV DEBIAN_FRONTEND=noninteractive

RUN set -e \
	&& rustup component add llvm-tools-preview

ENV PYENV_ROOT=/root/.pyenv \
    PATH=/root/.pyenv/shims:/root/.pyenv/bin:/root/.poetry/bin:$PATH

RUN set -e \
	&& apt-get update \
	&& apt-get install -y \
		build-essential \
		ca-certificates \
		gnupg \
		gzip \
		jq \
		curl \
		git \
		libpq-dev \
		libbz2-dev \
		liblzma-dev \
		libncurses5-dev \
		libncursesw5-dev \
		libreadline-dev \
		libffi-dev \
		libsqlite3-dev \
		libssl-dev \
		libxml2-dev \
		libxmlsec1-dev \
		llvm \
		make \
		tk-dev \
		wget \
		xz-utils \
		zlib1g-dev \
		net-tools \
		netcat \
		openssh-client \
		parallel \
		pkg-config \
		postgresql-client \
	&& curl -sSfL https://pyenv.run | bash \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV PYTHON_VERSION 3.7.12
RUN set -e \
	&& pyenv install ${PYTHON_VERSION} \
	&& pyenv global  ${PYTHON_VERSION}

RUN set -e \
	&& python --version \
	&& pip --version \
	&& pip install pipenv wheel

# This installs version poetry at the latest version. poetry is updated about twice a month.
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
