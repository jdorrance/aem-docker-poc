# DOCKER-VERSION 1.0.1
FROM smurfsky101/docker-ubuntu-14-04-java8:1
LABEL version="1.0"
LABEL description="AEM 6.2 base image"
MAINTAINER smurfsky101

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y \
    	dos2unix \
    	software-properties-common \
    	&& rm -rf /var/lib/apt/lists/*	

# Add large binaries (could do this from an HTTP endpoint instead)
# cq-quickstart-6.2.0.jar.jar is assumed to be the AEM quickstart jar
ADD cq-quickstart-6.2.0.jar /aem/cq-quickstart-6.2.0.jar
#ADD https://mavendc-utils.s3.amazonaws.com/internal/cq6/6.2/aem-service-pkg-6.2.SP1.zip /aem/packages/aem-service-pkg-6.2.SP1.zip

# Add the oak-run jar and compaction.sh for compaction
ADD http://central.maven.org/maven2/org/apache/jackrabbit/oak-run/1.5.17/oak-run-1.5.17.jar /aem/oak-run.jar
ADD compaction.sh /aem/compaction.sh
ADD org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg /aem/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg
ADD aemInstaller.py /aem/aemInstaller.py

# Add license properties file from relative path (could do this from an HTTP endpoint instead)
ADD license.properties /aem/license.properties

# Cleanup windows line endings just in case
RUN dos2unix /aem/*

WORKDIR /aem
