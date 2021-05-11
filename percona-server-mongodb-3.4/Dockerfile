FROM debian:jessie
MAINTAINER Percona Development Team <opensource-dev@percona.com>

RUN apt-get update && apt-get install -y --no-install-recommends vim curl strace sudo \
                apt-transport-https ca-certificates \
                pwgen jq \
        && rm -rf /var/lib/apt/lists/*

RUN apt-key adv --keyserver keys.gnupg.net --recv-keys 8507EFA5
RUN echo 'deb https://repo.percona.com/apt jessie main' > /etc/apt/sources.list.d/percona.list

ENV PERCONA_MAJOR 34
ENV PERCONA_VERSION 3.4.24-3.0.jessie

RUN groupadd -r mongodb && useradd -r -g mongodb mongodb

# 添加一个密码可以用来切换root，不然很多事情做不了好烦
RUN echo "mongodb:mongodb" | chpasswd 

RUN \
        apt-get update \
        && apt-get install -y --force-yes telnet \
           jq percona-server-mongodb-$PERCONA_MAJOR=$PERCONA_VERSION \
        && rm -rf /var/lib/apt/lists/* \
        && rm -rf /data/db && mkdir -p /data/db \ 
        && chown -R mongodb:mongodb /data/db

VOLUME ["/data/db"]
RUN adduser mongodb sudo

COPY ps-entry.sh /entrypoint.sh
COPY mongod /usr/bin/mongod
COPY mongos /usr/bin/mongos


RUN mkdir -p /home/mongodb/libs
COPY libs/* /home/mongodb/libs/

#RUN mkdir -p /home/mongodb/proxychains
#COPY proxychains/* /home/mongodb/proxychains/

# 指定运行库路径
ENV LD_LIBRARY_PATH=/home/mongodb/libs
RUN chown -R mongodb:mongodb /home/mongodb
RUN chown -R mongodb:mongodb /usr/bin
RUN chown -R mongodb:mongodb /data

ENTRYPOINT ["/entrypoint.sh"]
EXPOSE 27017
USER mongodb

CMD ["mongod"] 

