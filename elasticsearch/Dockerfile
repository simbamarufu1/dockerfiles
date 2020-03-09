FROM centos:7

ENV ELASTIC_CONTAINER true

COPY elasticsearch-7.2.0-SNAPSHOT-linux-ppc64le.tar.gz /elasticsearch.tar.gz
RUN tar -C /usr/share -xf /elasticsearch.tar.gz
RUN mv /usr/share/elasticsearch-7.2.0-SNAPSHOT /usr/share/elasticsearch

RUN yum update -y && yum install -y nc shadow-utils zip unzip && yum clean all 

RUN groupadd -g 1000 elasticsearch && adduser -u 1000 -g 1000 -G 0 -d /usr/share/elasticsearch elasticsearch && chmod 0775 /usr/share/elasticsearch && chgrp 0 /usr/share/elasticsearch

WORKDIR /usr/share/elasticsearch

# Replace OpenJDK's built-in CA certificate keystore with the one from the OS
# vendor. The latter is superior in several ways.
# REF: https://github.com/elastic/elasticsearch-docker/issues/171

RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /usr/share/elasticsearch/jdk/lib/security/cacerts

ENV PATH /usr/share/elasticsearch/bin:$PATH

COPY --chown=1000:0 bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh

# Openshift overrides USER and uses ones with randomly uid>1024 and gid=0
# Allow ENTRYPOINT (and ES) to run even with a different user
RUN chgrp 0 /usr/local/bin/docker-entrypoint.sh && chmod g=u /etc/passwd && chmod 0775 /usr/local/bin/docker-entrypoint.sh

EXPOSE 9200 9300


ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
# Dummy overridable parameter parsed by entrypoint
CMD ["eswrapper"]

################################################################################
# End of multi-stage Dockerfile
################################################################################
