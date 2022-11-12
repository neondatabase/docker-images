FROM summerwind/actions-runner:v2.291.1-ubuntu-20.04
ARG RUSTC_VERSION

ENV PATH="${HOME}/.cargo/bin:${PATH}"

WORKDIR ${HOME}

RUN cd $HOME && \
  curl -O https://static.rust-lang.org/rustup/dist/$(uname -m)-unknown-linux-gnu/rustup-init && \
	chmod +x rustup-init && \
	./rustup-init -y --no-modify-path --default-toolchain ${RUSTC_VERSION} && \
	rm rustup-init

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
        lsof \
        make \
        netcat \
        net-tools \
        openssh-client \
        parallel \
        pkg-config \
        postgresql-client \
        protobuf-compiler \
        wget \
        xz-utils \
        zlib1g-dev \
    && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Rust deps
RUN set -e \
    && rustup component add llvm-tools-preview rustfmt clippy \
    && cargo install --git https://github.com/paritytech/cachepot \
    && cargo install rustfilt

# Python
ENV PYTHON_VERSION 3.9.2
ENV PYENV_ROOT=$HOME/.pyenv \
    PATH=$HOME/.pyenv/shims:$HOME/.pyenv/bin:$HOME/.poetry/bin:$PATH
RUN set -e \
    && curl -sSfL https://pyenv.run | bash \
    && pyenv install ${PYTHON_VERSION} \
    && pyenv global  ${PYTHON_VERSION} \
    && python --version \
    && pip install --upgrade pip \
    && pip --version \
    && pip install pipenv wheel

# This installs version poetry at the latest version. poetry is updated about twice a month.
RUN curl -sSL https://install.python-poetry.org | python3 -

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

RUN echo "PATH=${PATH}" | sudo tee /etc/environment
