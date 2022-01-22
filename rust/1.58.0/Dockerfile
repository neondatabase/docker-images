ARG RUST_VERSION=1.58.0

FROM rust:${RUST_VERSION}-slim-bullseye

# Change default shell for RUN from Dash to Bash
SHELL ["/bin/bash", "-exo", "pipefail", "-c"]

ENV DEBIAN_FRONTEND=noninteractive

# Sys deps
RUN set -e \
	&& apt-get update \
	&& apt-get install -y \
		automake \
		bison \
		build-essential \
		ca-certificates \
		clang \
		cmake \
		curl \
		flex \
		git \
		gnupg \
		gzip \
		jq \
		libbz2-dev \
		libffi-dev \
		liblzma-dev \
		libncurses5-dev \
		libncursesw5-dev \
		libpq-dev \
		libreadline-dev \
		libseccomp-dev \
		libsqlite3-dev \
		libssl-dev \
		libstdc++-10-dev \
		libtool \
		libxml2-dev \
		libxmlsec1-dev \
		libxxhash-dev \
		llvm \
		make \
		netcat \
		net-tools \
		openssh-client \
		parallel \
		pkg-config \
		postgresql-client \
		tk-dev \
		wget \
		xz-utils \
		zlib1g-dev \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Rust deps
RUN set -e \
	&& rustup component add llvm-tools-preview rustfmt clippy \
	&& cargo install cargo-audit

# Python
ENV PYTHON_VERSION 3.7.12
ENV PYENV_ROOT=/root/.pyenv \
    PATH=/root/.pyenv/shims:/root/.pyenv/bin:/root/.poetry/bin:$PATH
RUN set -e \
	&& curl -sSfL https://pyenv.run | bash \
	&& pyenv install ${PYTHON_VERSION} \
	&& pyenv global  ${PYTHON_VERSION} \
	&& python --version \
	&& pip install --upgrade pip \
	&& pip --version \
	&& pip install pipenv wheel

# This installs version poetry at the latest version. poetry is updated about twice a month.
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -

# mold: A Modern Linker
ENV MOLD_VERSION v1.0.1
RUN set -e \
	&& git clone https://github.com/rui314/mold.git \
	&& cd mold \
	&& git checkout ${MOLD_VERSION} \
	&& make -j$(nproc) CXX=clang++ \
	&& make install \
	&& cd .. \
	&& rm -rf mold
