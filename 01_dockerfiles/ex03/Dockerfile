# This Dockerfile will build a gitlab server.
# Building the image will take several minutes, please be patient.
# Before building, create a docker-machine with enough RAM and cores with command:
#
# docker-machine create --virtualbox-memory "4096" --virtualbox-cpu-count "2" default

FROM ubuntu:18.04

# ports needed for SSH, HTTP & HTTPS
EXPOSE 22
EXPOSE 80
EXPOSE 443

# tzinfo needs to know local time
RUN ln -fs /usr/share/zoneinfo/Europe/Helsinki /etc/localtime

ENV DEBIAN_FRONTEND noninteractive

# install dependencies
RUN apt-get update
RUN apt-get install -y \
    curl \
    openssh-server \
    openssl \
    ca-certificates \
    postfix \
    nginx \
    tzdata \
    git

# download and install GitLab
RUN curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN apt-get install -y gitlab-ce

# prepare the RNG for key generation
RUN dd if=/dev/urandom of=/root/.rnd bs=256 count=1
# create a self-signed SSL certificate
RUN mkdir -p /etc/gitlab/ssl && chmod 755 /etc/gitlab/ssl
RUN openssl req \
    -x509 \
    -nodes \
    -days 365 \
    -subj /C=FI/ST=Helsinki/L=Helsinki/O=Hive/OU=C1\CN=192.168.99.100 \
    -newkey rsa:2048 \
    -keyout /etc/gitlab/ssl/192.168.99.100.key \
    -out /etc/gitlab/ssl/192.168.99.100.crt

# if you have your own domain, you can skip creating a self-signed certificate
# and enable letsencrypt's certificate:
#
# RUN echo "letsencrypt['enable'] = true" >> /etc/gitlab/gitlab.rb
# RUN echo "letsencrypt['contact_emails'] = ['rkyttala@student.hive.fi']" >> /etc/gitlab/gitlab.rb
# RUN letsencrypt certonly \
#     --standalone \
#     --agree-tos \
#     --no-eff-email \
#     --agree-tos \
#     --email rkyttala@student.hive.fi \
#     -d git.example.com
#
# RUN mkdir -p /etc/gitlab/ssl/
# RUN openssl dhparam -out /etc/gitlab/ssl/dhparams.pem 2048
# RUN chmod 755 /etc/gitlab/ssl/*

# append to following lines to the GitLab config file
RUN echo "external_url 'https://192.168.99.100'" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['redirect_http_to_https'] = true" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate'] = \"/etc/gitlab/ssl/192.168.99.100.crt\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate_key'] = \"/etc/gitlab/ssl/192.168.99.100.key\"" >> /etc/gitlab/gitlab.rb
RUN echo "gitlab_rails['gitlab_shell_ssh_port'] = \"8022\"" >> /etc/gitlab/gitlab.rb
RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
RUN echo "PasswordAuthentication no" >> /etc/ssh/sshd_config

# start ssh and GitLab server, wait for them get up and running and apply our configurations
ENTRYPOINT (/opt/gitlab/embedded/bin/runsvdir-start &) && \
    sleep 10 && \
    gitlab-ctl reconfigure && \
    service ssh restart && \
    tail -f /dev/null
