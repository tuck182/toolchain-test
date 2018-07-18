FROM debian:8
MAINTAINER Matt Tucker <tuck@whistlingfish.net>

ARG VERSION=3.15
ARG AGENT_WORKDIR=/home/jenkins/agent

RUN echo "deb http://http.debian.net/debian jessie-backports main" | \
        tee --append /etc/apt/sources.list.d/jessie-backports.list > /dev/null

# INSTALL
RUN apt-get update && apt-get install -y  \
        git \
        curl \
        locales \
        python-pip \
        apt-transport-https \
 && apt-get install -y -t jessie-backports \
        openjdk-8-jre-headless \
        ca-certificates-java \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
 && apt-get clean

# Set environment variables
RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# Setup GIT
RUN git config --global user.email "tuck@whistlingfish.net" \
    && git config --global user.name "Jenkins"

# Create Jenkins user
RUN groupadd -g 10000 jenkins \
  && useradd -m -d /home/jenkins  -u 10000 -g 10000 -s /bin/bash jenkins
RUN mkdir /data \
    && chown jenkins:jenkins /data

# Setup JNLP
ENV AGENT_WORKDIR=${AGENT_WORKDIR}

RUN curl --create-dirs -sSLo /usr/share/jenkins/slave.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/slave.jar

USER jenkins
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/jenkins/.jenkins && mkdir -p ${AGENT_WORKDIR}
WORKDIR /home/jenkins
USER root

RUN curl --create-dirs -sSLo /usr/local/bin/jenkins-slave https://raw.githubusercontent.com/jenkinsci/docker-jnlp-slave/master/jenkins-slave \
  && chmod 755 /usr/local/bin/jenkins-slave

ENTRYPOINT ["jenkins-slave"]
