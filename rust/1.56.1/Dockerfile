ARG RUST_VERSION=1.56.1

FROM cimg/rust:${RUST_VERSION}

# Sys deps
RUN set -e \
	&& sudo apt-get update \
	&& sudo apt-get install -y \
		autoconf \
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
	&& sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Rust deps
RUN set -e \
	&& rustup component add llvm-tools-preview rustfmt clippy \
	&& cargo install --git https://github.com/paritytech/cachepot
ENV RUSTC_WRAPPER=cachepot

# Python
ENV PYTHON_VERSION 3.9.2
ENV PYENV_ROOT=/home/circleci/.pyenv \
    PATH=/home/circleci/.pyenv/shims:/home/circleci/.pyenv/bin:/home/circleci/.poetry/bin:$PATH
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
	&& sudo make install \
	&& cd .. \
	&& rm -rf mold

# etcd
ENV ETCD_VER=v3.5.2
ENV DOWNLOAD_URL=https://storage.googleapis.com/etcd
RUN set -e \
    && curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o etcd-${ETCD_VER}-linux-amd64.tar.gz \
    && tar xzvf etcd-${ETCD_VER}-linux-amd64.tar.gz \
    && sudo mv etcd-${ETCD_VER}-linux-amd64/etcd etcd-${ETCD_VER}-linux-amd64/etcdctl /usr/local/bin/ \
    && rm -rf etcd-${ETCD_VER}-linux-amd64.tar.gz etcd-${ETCD_VER}-linux-amd64
