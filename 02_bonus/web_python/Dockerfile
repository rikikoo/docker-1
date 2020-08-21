FROM python:latest

WORKDIR /opt/app
COPY . $WORKDIR

EXPOSE 80

RUN pip install -r requirements.txt
RUN apt-get update
RUN apt-get install -y \
    vim \
    apache2 \
    git
