# DOCKER-VERSION 1.7.0 PUBLISHER
FROM aem-docker-poc_6-2_base:latest
LABEL version="1.0"
LABEL description="AEM publisher docker image"
MAINTAINER smurfsky101

# Extracts AEM
WORKDIR /aem

#Copies required build media
RUN mv /aem/cq-quickstart-6.2.0.jar /aem/cq-publish-4503.jar
ADD resources/postInstallHook.py /aem/postInstallHook.py

# Add customised log file, to print the logging to standard out.
ADD resources/org.apache.sling.commons.log.LogManager.config /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config
ADD resources/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg

# Cleanup windows line just in case
RUN dos2unix /aem/* \
	 /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config \
	 /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg

# Installs AEM
WORKDIR /aem
RUN apt-get update && apt-get install -y \
    	ipython \
    	ipython3 \
    	python-psutil \
    	python-pycurl \
    	&& python aemInstaller.py -i cq-publish-4503.jar -r publish -p 4503 \
    	&& apt-get purge -y \
    	ipython \
    	ipython3 \
    	python-psutil \
    	python-pycurl \
    	&& rm -rf /var/lib/apt/lists/* && \
    	rm /aem/cq-publish-4503.jar

WORKDIR /aem/crx-quickstart/bin
RUN mv quickstart quickstart.original
ADD resources/quickstart /aem/crx-quickstart/bin/quickstart

# Convert from windows line endings, just in case
RUN dos2unix /aem/crx-quickstart/bin/*

# Make quickstart and compaction executable
RUN chmod +x /aem/crx-quickstart/bin/quickstart
RUN chmod +x /aem/compaction.sh

# Set Quickstart Env Variables
ENV CQ_RUNMODE "publish,nosamplecontent,docker"
ENV CQ_JAAS_CONFIG "etc/jaas.config"
ENV CQ_JVM_OPTS "-XX:MaxPermSize=512M -Xmx2G -XX:+UseParallelGC -XX:+UseParallelOldGC"

# Default Publish port 4503
EXPOSE 4503
ENTRYPOINT ["/aem/crx-quickstart/bin/quickstart"]
#ENTRYPOINT ["/aem/compaction.sh"]
