# DOCKER-VERSION 1.7.0 AUTHOR
FROM aem-docker-poc_6-2_base:latest
LABEL version="1.0"
LABEL description="AEM author docker image"
MAINTAINER smurfsky101

WORKDIR /aem

# Add customised log file, to print the logging to standard out.
ADD resources/org.apache.sling.commons.log.LogManager.config /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config
RUN dos2unix /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config

ADD resources/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg
RUN dos2unix /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg

ADD resources/postInstallHook.py /aem/postInstallHook.py
ADD resources/packagelist.txt /aem/packagelist.txt
RUN dos2unix /aem/postInstallHook.py
RUN dos2unix /aem/packagelist.txt

ADD aemInstaller.py /aem/aemInstaller.py

# Set Quickstart Env Variables
ENV CQ_RUNMODE "publish,nosamplecontent,docker"
ENV CQ_JAAS_CONFIG "etc/jaas.config"
ENV CQ_JVM_OPTS "-XX:MaxPermSize=512M -Xmx2G -XX:+UseParallelGC -XX:+UseParallelOldGC"

WORKDIR /aem

RUN mkdir packages && \
    curl -o packages/aem-service-pkg-6.2.SP1.zip http://mavendc-utils.s3.amazonaws.com/internal/cq6/6.2/aem-service-pkg-6.2.SP1.zip && \
    python aemInstaller.py -i cq-quickstart-6.2.0.jar -r publish -p 4503 && \
    rm -rf packages

WORKDIR /aem/crx-quickstart/bin
ADD resources/quickstart /aem/crx-quickstart/bin/quickstart

# Convert from windows line endings, just in case
RUN dos2unix /aem/crx-quickstart/bin/*

# Make AEM executable
ADD compaction.sh /aem/compaction.sh

RUN chmod +x /aem/crx-quickstart/bin/quickstart
RUN chmod +x /aem/compaction.sh

#Default port 4502 for author
EXPOSE 4503
ENTRYPOINT ["/aem/crx-quickstart/bin/quickstart"]
#ENTRYPOINT ["/aem/compaction.sh"]
