# ベースイメージとして CentOS 8 を使用
FROM centos:8

# CentOS Vault リポジトリを設定
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*.repo \
    && sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*.repo

# 必要なパッケージをインストール
RUN yum install -y \
    git \
    curl \
    which \
    sudo \
    gcc \
    openssl-devel \
    readline-devel \
    zlib-devel \
    libffi-devel \
    make \
    bzip2 \
    wget \
    tar \
    zsh \
    emacs

# libyaml をダウンロードしてインストール
RUN wget http://pyyaml.org/download/libyaml/yaml-0.2.5.tar.gz && \
    tar -xzf yaml-0.2.5.tar.gz && \
    cd yaml-0.2.5 && \
    ./configure && \
    make && \
    make install && \
    cd .. && \
    rm -rf yaml-0.2.5 yaml-0.2.5.tar.gz

# rbenv と ruby-build をインストール
RUN git clone https://github.com/rbenv/rbenv.git /root/.rbenv && \
    echo 'export PATH="/root/.rbenv/bin:$PATH"' >> /root/.bashrc && \
    echo 'eval "$(rbenv init -)"' >> /root/.bashrc && \
    git clone https://github.com/rbenv/ruby-build.git /root/.rbenv/plugins/ruby-build && \
    /root/.rbenv/plugins/ruby-build/install.sh

# Ruby のインストール（最新の安定バージョンを指定）
RUN source /root/.bashrc && \
    rbenv install 3.2.1 && \
    rbenv global 3.2.1 && \
    rbenv rehash

# Bundler をインストール
RUN source /root/.bashrc && \
    gem install bundler

# Node.js と Yarn をインストール
RUN curl -sL https://rpm.nodesource.com/setup_14.x | bash - && \
    yum install -y nodejs && \
    npm install -g yarn

# Rails をインストール
RUN source /root/.bashrc && \
    gem install rails

# Zsh をデフォルトのシェルとして設定
RUN echo 'export SHELL=/bin/zsh' >> /root/.bashrc && \
    echo 'exec /bin/zsh -l' >> /root/.bashrc

# dotfiles インストール
RUN git clone https://github.com/norisuke3/dotfiles.git \
    && cd dotfiles \
    && make symlink \
    && make symlink_emacs

# Zsh から Ruby が見えるように .zshrc に設定を書き込む
RUN echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> /root/.zshrc && \
    echo 'eval "$(rbenv init -)"' >> /root/.zshrc

# 作業ディレクトリを作成
WORKDIR /app

# ポートを公開
EXPOSE 3000

# エントリーポイントを zsh に変更
ENTRYPOINT ["/bin/zsh"]

# CMD で Rails サーバを起動するコマンドを指定
CMD ["-c", "source /root/.zshrc && rails server -b 0.0.0.0 -p 3000"]
