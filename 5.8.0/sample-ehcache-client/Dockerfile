# using the latest OpenJDK 8 update (see https://hub.docker.com/_/openjdk/ for more details)
ARG EHCACHE_VERSION=3.9.3
FROM openjdk:8-jdk-alpine
LABEL maintainers="Anthony Dahanne <anthony.dahanne@softwareag.com>"

# add few utilities, upgrade tar
RUN apk --update add tar openssl ca-certificates bash

# downloading, untarring and removing the archive, link from https://github.com/ehcache/ehcache3/releases
RUN wget -q https://repo1.maven.org/maven2/org/ehcache/ehcache-clustered/${EHCACHE_VERSION}/ehcache-clustered-${EHCACHE_VERSION}-kit.tgz \
  && mkdir /terracotta \
  && tar xvzf ehcache-clustered-${EHCACHE_VERSION}-kit.tgz -C /terracotta --strip-components=1 \
  && rm ehcache-clustered-${EHCACHE_VERSION}-kit.tgz

RUN cp /terracotta/client/logging/impl/logback*.jar /terracotta/client/lib/

COPY ./src/ /terracotta/
COPY logback.xml /terracotta/logback.xml

# adding the user terracotta, to not run the server as root
RUN addgroup -S terracotta && adduser -h /terracotta -s /bin/bash -G terracotta -S -D terracotta
RUN chown -R terracotta:terracotta /terracotta
USER terracotta

# This is the current working directory
WORKDIR /terracotta/

ENV CLASSPATH ".:/terracotta/client/ehcache/*:/terracotta/client/lib/*"
ENV OFFHEAP_RESOURCE1_NAME "offheap-1"
ENV TERRACOTTA_SERVER_URL=terracotta:9410

RUN javac ClientDoingInsertionsAndRetrievals.java

ENTRYPOINT ["java", "-Dlogback.configurationFile=/terracotta/logback.xml", "ClientDoingInsertionsAndRetrievals"]