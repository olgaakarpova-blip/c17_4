FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install -y \
    postgresql \
    postgresql-contrib \
    openssh-server \
    sudo

RUN mkdir /var/run/sshd

ARG TOLMACHEV_HASH
RUN useradd -m -s /bin/bash tolmachev && \
    echo "tolmachev:${TOLMACHEV_HASH}" | chpasswd -e && \
    usermod -aG sudo tolmachev

# SSH настройки
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config

# PostgreSQL
USER postgres
RUN /etc/init.d/postgresql start && \
    psql -c "ALTER USER postgres PASSWORD 'postgres';"

USER root

EXPOSE 22 5432

CMD service postgresql start && /usr/sbin/sshd -D
