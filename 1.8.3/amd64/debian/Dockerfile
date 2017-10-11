# openhab image
#
# ------------------------------------------------------------------------------
#               NOTE: THIS DOCKERFILE IS GENERATED VIA "update.sh"
#
#                       PLEASE DO NOT EDIT IT DIRECTLY.
# ------------------------------------------------------------------------------
#

FROM multiarch/debian-debootstrap:amd64-jessie

MAINTAINER openHAB <info@openhabfoundation.org>

# Set download urls
ENV JAVA_URL="https://www.azul.com/downloads/zulu/zdk-8-ga-linux_x64.tar.gz"
ENV OPENHAB_URL="https://bintray.com/artifact/download/openhab/bin/distribution-1.8.3-runtime.zip"
ENV OPENHAB_VERSION="1.8.3"

# Set variables
ENV \
    APPDIR="/openhab" \
    EXTRA_JAVA_OPTS="" \
    OPENHAB_HTTP_PORT="8080" \
    OPENHAB_HTTPS_PORT="8443"

# Basic build-time metadata as defined at http://label-schema.org
ARG BUILD_DATE
ARG VCS_REF
LABEL org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.docker.dockerfile="/Dockerfile" \
    org.label-schema.license="EPL" \
    org.label-schema.name="openHAB" \
    org.label-schema.url="http://www.openhab.com/" \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-type="Git" \
    org.label-schema.vcs-url="https://github.com/openhab/openhab-docker.git" \
maintainer="openHAB <info@openhabfoundation.org>"

# Set locales
ENV \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8

# Install basepackages
RUN apt-get update && \
apt-get install --no-install-recommends -y \
ca-certificates \
fontconfig \
locales \
locales-all \
libpcap-dev \
netbase \
unzip \
wget \
&& rm -rf /var/lib/apt/lists/*
ENV DEBIAN_FRONTEND=noninteractive

# Install java
ENV JAVA_HOME='/usr/lib/java-8'
RUN wget -nv -O /tmp/java.tar.gz ${JAVA_URL} &&\
    mkdir ${JAVA_HOME} && \
    tar -xvf /tmp/java.tar.gz --strip-components=1 -C ${JAVA_HOME} && \
    update-alternatives --install /usr/bin/java java ${JAVA_HOME}/bin/java 50 && \
    update-alternatives --install /usr/bin/javac javac ${JAVA_HOME}/bin/javac 50

# Install gosu
ENV GOSU_VERSION 1.10
RUN set -x \
&& dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
&& wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu

# Install openhab
RUN wget -nv -O /tmp/openhab.zip ${OPENHAB_URL} &&\
    unzip -q /tmp/openhab.zip -d ${APPDIR} &&\
    rm /tmp/openhab.zip &&\
    cp -a ${APPDIR}/configurations ${APPDIR}/configurations.dist && \
    echo "export TERM=dumb" | tee -a ~/.bashrc

# Expose volume with configuration and userdata dir
VOLUME ${APPDIR}/configurations ${APPDIR}/addons

# Set working directory, expose and entrypoint
WORKDIR ${APPDIR}
EXPOSE 8080 8443 5555
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
# Execute command

CMD ["gosu", "openhab", "./start.sh"]