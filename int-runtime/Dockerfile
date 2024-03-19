FROM staillansag/webmethods-edge-runtime:11.0.0.2-jdbc as builder

ARG GIT_TOKEN

WORKDIR /opt/softwareag/wpm
RUN /opt/softwareag/wpm/bin/wpm.sh install -u staillansag -p $GIT_TOKEN -r https://github.com/staillansag -d /opt/softwareag/IntegrationServer SttDemoiPaaSProject
RUN /opt/softwareag/wpm/bin/wpm.sh install -u staillansag -p $GIT_TOKEN -r https://github.com/staillansag -d /opt/softwareag/IntegrationServer sttUtils
WORKDIR /

USER root
RUN chgrp -R root /opt/softwareag && chmod -R g=u /opt/softwareag


FROM registry.access.redhat.com/ubi8/ubi-minimal:latest

ENV JAVA_HOME=/opt/softwareag/jvm/jvm/ \
    JRE_HOME=/opt/softwareag/jvm/jvm/ \
    JDK_HOME=/opt/softwareag/jvm/jvm/

RUN microdnf -y update ;\
    microdnf -y install \
        procps \
        shadow-utils \
        findutils \
        ;\
    microdnf clean all ;\
    rm -rf /var/cache/yum ;\
    useradd -u 1724 -m -g 0 -d /opt/softwareag sagadmin

RUN chmod 770 /opt/softwareag
COPY --from=builder /opt/softwareag /opt/softwareag

USER sagadmin

EXPOSE 5555
EXPOSE 9999
EXPOSE 5543

ENTRYPOINT "/bin/bash" "-c" "/opt/softwareag/IntegrationServer/bin/startContainer.sh"