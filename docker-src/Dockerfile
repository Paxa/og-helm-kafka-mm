FROM azul/zulu-openjdk-debian:8

#MAINTAINER paul@opsguru.io
MAINTAINER pavel.evstigneev@gojek.com

ENV KAFKA_VERSION "2.2.2"
ENV KAFKA_MM_CFG_ROOT "/etc/mirror-maker"
ENV KAFKA_ROOT "/opt/kafka"
ENV SCALA_VERSION "2.12"
ENV KAFKA_HEAP_OPTS "-Xms1024M -Xmx1024M"
ENV KAFKA_MM_WHITELIST ".*"
ENV KAFKA_MM_NUMSTREAMS "1"
ENV JMXPORT "9998"

# Zulu
ENV LANG="en_US.UTF-8"

RUN echo "===> Creating dedicated user/group for MirroMaker" \
    && useradd kafka -m

RUN apt-get -qq update
RUN apt-get -y install wget curl nano

RUN echo "LC_ALL=en_US.UTF-8" >> /etc/environment && \
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && \
    echo "LANG=en_US.UTF-8" > /etc/locale.conf && \
    locale-gen en_US.UTF-8

# Install Kafka binaries & set up MM
RUN mkdir ${KAFKA_ROOT}
RUN mkdir ${KAFKA_MM_CFG_ROOT}
RUN wget http://www-us.apache.org/dist/kafka/${KAFKA_VERSION}/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz -O - \
    | tar xvfz - --strip-components=1 -C ${KAFKA_ROOT}

ADD ./run.sh ${KAFKA_ROOT}/
RUN chmod +x ${KAFKA_ROOT}/run.sh
RUN chown kafka:kafka -R ${KAFKA_ROOT}

ENV CLASSPATH /opt/renametopichandler-1.0-SNAPSHOT.jar
ADD ./mirrormaker-rename-topic-handler/renametopichandler-1.0-SNAPSHOT.jar /opt/

CMD ["su", "-c", "${KAFKA_ROOT}/run.sh", "kafka"]
