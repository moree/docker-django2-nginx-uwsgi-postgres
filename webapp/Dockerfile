# Copyright 2013 Thatcher Peskens
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM alpine:latest

MAINTAINER Dockerfiles

# Install required packages and remove the apt packages cache when done.
#apk update && apk upgrade && \
RUN apk update 
RUN apk add bash \
	python3 \
	python3-dev \
	gcc \
	build-base \
	linux-headers \
	pcre-dev \
	postgresql-dev \
	musl-dev \
	supervisor && \
	python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    rm -r /root/.cache && \
    pip3 install --upgrade pip setuptools && \
    rm -r /root/.cache

# install uwsgi now because it takes a little while
RUN pip3 install uwsgi

# setup all the configfiles
COPY supervisor-app.conf /etc/supervisor/conf.d/

ADD ./sources /var/www/

# COPY requirements.txt and RUN pip install BEFORE adding the rest of your code, this will cause Docker's caching mechanism
# to prevent re-installing (all your) dependencies when you made a change a line or two in your app.
COPY app/requirements.txt /home/docker/code/app/
RUN pip3 install -r /home/docker/code/app/requirements.txt

RUN cd /var/www/djangopj/ && python3 manage.py collectstatic

# add (the rest of) our code
COPY supervisor-app.conf /home/docker/code/
COPY uwsgi.ini /home/docker/code/
COPY wait-for-it.sh /home/docker/code/
#COPY .env /var/

WORKDIR /home/docker/
#EXPOSE 8000
#CMD ["supervisord", "-n", "-c", "/home/docker/code/supervisor-app.conf"]
