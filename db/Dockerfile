FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV POSTGRES_USER=postgres
ENV POSTGRES_PASSWORD=postgres
ENV POSTGRES_DB=labdb

RUN apt update && apt install -y \
    postgresql \
    postgresql-contrib \
    openssh-server \
    vim \
    sudo

RUN mkdir /var/run/sshd

ARG TOLMACHEV_HASH

# Создание пользователя tolmachev с sudo и паролем
RUN useradd -m -s /bin/bash tolmachev && \
    echo "tolmachev:${TOLMACHEV_HASH}" | chpasswd -e && \
    usermod -aG sudo tolmachev

# Настройки SSH
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config

# PostgreSQL настройки
USER postgres

# Настройка конфигурации PostgreSQL для внешних подключений
RUN sed -i "s/#listen_addresses = 'localhost'/listen_addresses = '*'/g" /etc/postgresql/*/main/postgresql.conf && \
    sed -i "s/#port = 5432/port = 5432/" /etc/postgresql/*/main/postgresql.conf && \
    /etc/init.d/postgresql start && \
    psql -c "ALTER USER ${POSTGRES_USER} PASSWORD '${POSTGRES_PASSWORD}';" && \
    psql -c "CREATE DATABASE ${POSTGRES_DB} OWNER ${POSTGRES_USER};"

USER root

EXPOSE 22 5432

CMD service postgresql start && /usr/sbin/sshd -D
