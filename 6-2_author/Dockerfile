# DOCKER-VERSION 1.7.0 AUTHOR
FROM aem-docker-poc_6-2_base:latest
LABEL version="1.0"
LABEL description="AEM author docker image"
MAINTAINER smurfsky101

WORKDIR /aem
RUN mv /aem/cq-quickstart-6.2.0.jar /aem/cq-author-4502.jar

# Add customised log file, to print the logging to standard out.
ADD resources/org.apache.sling.commons.log.LogManager.config /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config
ADD resources/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg
ADD resources/postInstallHook.py /aem/postInstallHook.py

# Cleanup windows line just in case
RUN dos2unix /aem/* \
	 /aem/crx-quickstart/install/org.apache.sling.commons.log.LogManager.config \
	 /aem/crx-quickstart/install/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.cfg

# Set Quickstart Env Variables
ENV CQ_RUNMODE "author,nosamplecontent,docker"
ENV CQ_JAAS_CONFIG "etc/jaas.config"
ENV CQ_JVM_OPTS "-XX:MaxPermSize=512M -Xmx2G -XX:+UseParallelGC -XX:+UseParallelOldGC"

WORKDIR /aem
RUN apt-get update && apt-get install -y \
    	ipython \
    	ipython3 \
    	python-psutil \
    	python-pycurl \
    	&& python aemInstaller.py -i cq-author-4502.jar -r author -p 4502 \
    	&& apt-get purge -y \
    	ipython \
    	ipython3 \
    	python-psutil \
    	python-pycurl \
    	&& rm -rf /var/lib/apt/lists/* && \
    	rm /aem/cq-author-4502.jar

WORKDIR /aem/crx-quickstart/bin
RUN mv quickstart quickstart.original
ADD resources/quickstart /aem/crx-quickstart/bin/quickstart

# Convert from windows line endings, just in case
RUN dos2unix /aem/crx-quickstart/bin/*

# Make AEM executable
RUN chmod +x /aem/crx-quickstart/bin/quickstart
RUN chmod +x /aem/compaction.sh

#Default port 4502 for author
EXPOSE 4502
ENTRYPOINT ["/aem/crx-quickstart/bin/quickstart"]
#ENTRYPOINT ["/aem/compaction.sh"]
