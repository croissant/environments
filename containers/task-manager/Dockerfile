FROM debian:latest

ENV GIT_VERSION 2.19.1
ENV PYTHON_VERSION 3.7.0

RUN apt-get update
RUN apt-get -y install aptitude \
    build-essential \
    python \
    openssh-server \
    sudo \
    locales \
    zlib1g-dev \
    libssl-dev \
    libcurl4-openssl-dev \
    gettext \
    libreadline-dev \
    libbz2-dev \
    libsqlite3-dev \
    libpq-dev \
    gcc \
    g++ \
    make \
    xzip \
    autoconf \
    libffi-dev

ARG USER_ID
ARG GROUP_ID

WORKDIR /usr/src
RUN groupadd -g ${GROUP_ID} develop
RUN useradd -m -d /home/docker -g ${GROUP_ID} -u ${USER_ID} -s /bin/bash docker
RUN chown -R docker:develop /home/docker
RUN mkdir /var/run/sshd
RUN echo 'docker ALL=(ALL:ALL) NOPASSWD:ALL' >> /etc/sudoers
RUN echo 'docker:develop' | chpasswd

RUN curl -LO https://www.kernel.org/pub/software/scm/git/git-${GIT_VERSION}.tar.xz
RUN tar xJf git-${GIT_VERSION}.tar.xz && cd git-${GIT_VERSION} && ./configure --without-tcltk && make install
RUN git config --global http.sslVerify false

WORKDIR /usr/local
RUN git clone https://github.com/yyuu/pyenv.git
ADD pyenv/pyenv.sh /etc/profile.d/pyenv.sh
RUN . /etc/profile && pyenv install -s ${PYTHON_VERSION} && pyenv rehash && pyenv global ${PYTHON_VERSION}
RUN chown -R docker:develop /usr/local/pyenv
RUN . /etc/profile && pip install pygments

RUN echo 'en_US.UTF-8 UTF-8' > /etc/locale.gen
RUN locale-gen

EXPOSE 22

RUN apt clean

ADD entrypoint.sh /entrypoint.sh
RUN chmod 0755 /entrypoint.sh

ENTRYPOINT /entrypoint.sh
