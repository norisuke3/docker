# ベースイメージとして CentOS 8 を使用
FROM centos:8

# 最初から有る不要なファイルを削除
RUN cd /root \
    && rm anaconda-ks.cfg  anaconda-post.log  original-ks.cfg .bash_logout .bash_profile .cshrc .tcshrc

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
    gnutls \
    gnutls-devel \
    golang

# emacs の make に必要な、makeinfo を含むパッケージ texinfo をインストール
RUN dnf --enablerepo=powertools install -y texinfo

# emacs をダウンロード/インストール
RUN mkdir ~/tmp \
    && cd ~/tmp \
    && wget https://ftp.gnu.org/pub/gnu/emacs/emacs-27.2.tar.gz \
    && tar -xavf emacs-27.2.tar.gz \
    && cd emacs-27.2 \
    &&  ./configure --without-x \
    && sudo make install \
    && cd \
    && rm -rf tmp

# peco をダウンロード/インストール
RUN mkdir ~/tmp \
    && cd ~/tmp \
    && wget https://github.com/peco/peco/releases/download/v0.5.11/peco_linux_amd64.tar.gz \
    && tar -xzvf peco_linux_amd64.tar.gz \
    && cp peco_linux_amd64/peco /usr/local/bin \
    && cd \
    && rm -rf tmp


# Zsh をデフォルトのシェルとして設定
RUN echo 'export SHELL=/bin/zsh' >> /root/.bashrc && \
    echo 'exec /bin/zsh -l' >> /root/.bashrc

# peco_for_zsh.linux.txt をクローンして、~/zshrc.local としてインストール
RUN cd ~/ \
    && git clone https://gist.github.com/4e3a1ad0a470b13126240d313c02d382.git \
    && mv 4e3a1ad0a470b13126240d313c02d382/.zshrc.local ~/.zshrc.local \
    && rm -rf 4e3a1ad0a470b13126240d313c02d382

# dotfiles インストール
RUN cd ~/ \
    && git clone --branch docker https://github.com/norisuke3/dotfiles.git \
    && cd dotfiles \
    && make symlink \
    && make symlink_emacs

# Emacs 起動時の init-loader が見つからないエラー対策
RUN cd ~/ \
    && git clone https://github.com/emacs-jp/init-loader.git \
    && cp init-loader/init-loader.el dotfiles/.emacs.d/site-lisp \
    && rm -rf init-loader

COPY ./elpa.tar.gz /root/.emacs.d/elpa.tar.gz

RUN cd /root/.emacs.d/ \
    && tar -xzvf ./elpa.tar.gz

# Go 環境設定
RUN cd ~/ \
    && mkdir go \
    && cd go \
    && mkdir bin pkg src

# GHQ インストール
RUN go get github.com/x-motemen/ghq@c875403c25e268945946e2926698fb23097e56f4

# 作業ディレクトリを設定
WORKDIR /root

# エントリーポイントを zsh に変更
ENTRYPOINT ["/bin/zsh"]
