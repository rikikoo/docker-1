FROM debian:latest

ADD start.sh /

RUN apt-get update && apt-get install -y wget tar bzip2

RUN wget -O ts3.tar.bz2 https://files.teamspeak-services.com/releases/server/3.12.1/teamspeak3-server_linux_amd64-3.12.1.tar.bz2
RUN tar -xjf ts3.tar.bz2

RUN useradd roger_rabbit && chown -R roger_rabbit teamspeak3-server_linux_amd64 && chmod +x start.sh
RUN su roger_rabbit && touch teamspeak3-server_linux_amd64/.ts3server_license_accepted

CMD ["/bin/sh", "-c", "/start.sh"]

# docker build -t ts3server .
# docker run -it --rm -u roger_rabbit -p 9987:9987/udp -e TS3SERVER_LICENSE=accept ts3server

# test on the teamspeak client:
# cmd + S
# server address: 192.168.99.100:9987
# one-time privilege key: token=...
