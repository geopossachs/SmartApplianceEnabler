FROM eclipse-temurin:11-jdk-noble

MAINTAINER Axel Mueller <axel.mueller@avanux.de>

ARG PROJECT_VERSION

ENV PATH="/usr/bin:${PATH}"
ENV JAVA_OPTS="-Duser.language=de -Duser.country=DE"
ENV SAE_INSTALL_DIR=/opt/sae
ENV SAE_HOME=/opt/sae/data
ENV SAE_VERSION=$PROJECT_VERSION

EXPOSE 8080

# switch user
USER root

# set timezone to 'Europe/Berlin'
RUN echo "tzdata tzdata/Areas select Europe" > /tmp/preseed.txt; \
    echo "tzdata tzdata/Zones/Europe select Berlin" >> /tmp/preseed.txt; \
    debconf-set-selections /tmp/preseed.txt && \
    rm /etc/timezone && \
    rm /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

# install favored tools 
RUN apt-get update && apt-get install -y --no-install-recommends wget curl nano less htop unzip \
    && rm -rf /var/lib/apt/lists/*

# create a user for the service
RUN adduser --no-create-home --disabled-password --disabled-login amperise \
    && chown -R amperise:amperise /opt \
    && chmod -R a+rw /opt
    
USER amperise

RUN mkdir -p $SAE_INSTALL_DIR && \
    curl -o $SAE_INSTALL_DIR/logback-spring.xml -L -k https://github.com/camueller/SmartApplianceEnabler/raw/master/run/logback-spring.xml

COPY src/target/SmartApplianceEnabler-$SAE_VERSION.war $SAE_INSTALL_DIR/SmartApplianceEnabler-$SAE_VERSION.war

CMD java $JAVA_OPTS -Djava.awt.headless=true -Xmx256m -Dsae.home=$SAE_HOME -Dlogging.config=$SAE_INSTALL_DIR/logback-spring.xml -jar $SAE_INSTALL_DIR/SmartApplianceEnabler-$SAE_VERSION.war