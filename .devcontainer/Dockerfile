# hadolint ignore=DL3007
FROM debian:12.11-slim

ARG UID=1000
ARG GID=1000
ARG USER_NAME=dev

LABEL maintainer="69619077+szk302@users.noreply.github.com" \
      description="test developer image" \
      version="1.0.0"

ENV DEBIAN_FRONTEND=noninteractive \
    LC_ALL=ja_JP.UTF-8 \
    LANG=ja_JP.UTF-8 \
    PATH="/usr/local/bin:${PATH}" \
    HISTFILE="/commandhistory/.bash_history"

# -------------------------------------------------------------------
# 基本ツール + ロケール
# -------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        gnupg \
        locales \
        build-essential \
        unzip \
        jq \
        less \
        git \
        gosu \
        sudo && \
    rm -rf /var/lib/apt/lists/* && \
    sed -i -E 's/# (ja_JP.UTF-8)/\1/' /etc/locale.gen && \
    locale-gen

# -------------------------------------------------------------------
# ユーザー作成
# -------------------------------------------------------------------
RUN groupadd --gid "${GID}" "${USER_NAME}" && \
    useradd --uid "${UID}" --gid "${GID}" --shell /bin/bash --create-home "${USER_NAME}" && \
    echo "${USER_NAME}:${USER_NAME}" | /usr/sbin/chpasswd && \
    echo "${USER_NAME} ALL=(ALL:ALL) ALL" | tee "/etc/sudoers.d/${USER_NAME}" && \
    chmod 0440 "/etc/sudoers.d/${USER_NAME}"

WORKDIR /tmp

# -------------------------------------------------------------------
# GitHub CLI（署名済APTリポジトリ）
# -------------------------------------------------------------------
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
      -o /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    chmod a+r /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
      | tee /etc/apt/sources.list.d/github-cli.list && \
    apt-get update && \
    apt-get install -y gh && \
    rm -rf /var/lib/apt/lists/* && \
    gh --version

# -------------------------------------------------------------------
# 開発ツール（ユーザ権限）
# -------------------------------------------------------------------
USER ${USER_NAME}
WORKDIR /home/${USER_NAME}

# -------------------------------------------------------------------
# エントリポイント
# -------------------------------------------------------------------
USER root
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
