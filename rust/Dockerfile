FROM bitnami/etcd:3.5.9 as etcd

FROM summerwind/actions-runner:v2.304.0-ubuntu-22.04-34fdbf1
ARG RUSTC_VERSION

# To speed up cargo index update
ENV CARGO_NET_GIT_FETCH_WITH_CLI=true
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
ENV MOLD_VERSION v1.11.0
RUN set -e \
    && git clone https://github.com/rui314/mold.git -b ${MOLD_VERSION} \
    && mkdir mold/build \
    && cd mold/build \
    && sudo ../install-build-deps.sh \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER=c++ .. \
    && cmake --build . -j $(nproc) \
    && sudo cmake --install . \
    && cd ../.. \
    && rm -rf mold

# etcd
COPY --from=etcd /opt/bitnami/etcd/bin/etcd /usr/local/bin/etcd
COPY --from=etcd /opt/bitnami/etcd/bin/etcdctl /usr/local/bin/etcdctl

RUN etcd --version
RUN echo "PATH=${PATH}" | sudo tee /etc/environment
