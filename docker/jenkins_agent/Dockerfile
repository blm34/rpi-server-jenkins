FROM jenkins/ssh-agent:jdk17 AS builder

USER root

# Build dependencies for python
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libffi-dev \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    liblzma-dev \
    && rm -rf /var/lib/apt/lists/*

# Install pyenv
ENV PYENV_ROOT=/opt/pyenv
ENV PATH=$PYENV_ROOT/bin:$PYENV_ROOT/shims:$PATH

RUN git clone --depth=1 https://github.com/pyenv/pyenv.git $PYENV_ROOT \
    && pyenv install 3.14.2


# ---- runtime stage ---- #
FROM jenkins/ssh-agent:jdk17

USER root

COPY --from=builder /opt/pyenv /opt/pyenv

ENV PYENV_ROOT=/opt/pyenv
ENV PATH=$PYENV_ROOT/bin:$PYENV_ROOT/shims:$PATH

RUN ln -s /opt/pyenv/versions/3.14.2/bin/python3.14 /usr/bin/python3 \
    && ln -s /opt/pyenv/versions/3.14.2/bin/python3.14 /usr/bin/python

# Install curl
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Install uv
RUN curl -LsSf https://astral.sh/uv/install.sh | sh \
    && mv /root/.local/bin/uv /usr/local/bin/uv \
    && mv /root/.local/bin/uvx /usr/local/bin/uvx
