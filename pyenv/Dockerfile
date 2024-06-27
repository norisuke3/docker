FROM norisuke3/centos_with_zsh_emacs:latest

# Install pyenv dependencies
RUN yum install -y \
    patch \
    bzip2-devel \
    sqlite \
    sqlite-devel \
    tk-devel \
    xz-devel

RUN curl https://pyenv.run | bash

COPY ./.zshrc.local.add /root/.zshrc.local.add

RUN cat ~/.zshrc.local.add >> .zshrc.local \
     && rm .zshrc.local.add

RUN ~/.pyenv/bin/pyenv install 3.11.6

RUN ~/.pyenv/bin/pyenv virtualenv 3.11.6 default \
    && ~/.pyenv/bin/pyenv global default

RUN ~/.pyenv/shims/pip install ipython
