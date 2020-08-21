FROM httpd:2.4.35-alpine

EXPOSE 80
EXPOSE 443

COPY ./apache.conf /usr/local/apache2/conf/httpd.conf

RUN apk update -y
RUN apk upgrade -y
