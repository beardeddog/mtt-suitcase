FROM jenkins/jenkins
LABEL maintainer.primary="richard.t.barella@intel.com" \
      maintainer.secondary="william.c.weide@intel.com"

# Dockerfile is built using Anton Fisher's as a template: 
# https://antonfisher.com/posts/2017/01/16/run-jenkins-in-docker-container-with-persistent-configuration/

ARG http_server
ARG http_port
ARG https_server
ARG https_port
ARG master_num_executors

ENV JAVA_OPTS    "-Dhttp.proxyHost=${http_server} -Dhttp.proxyPort=${http_port} \
  -Dhttps.proxyHost=${https_server} -Dhttps.proxyPort=${https_port} \
  -Djenkins.install.runSetupWizard=false"
ENV JENKINS_OPTS "-sessionTimeout=120"
ENV JENKINS_HOME /var/jenkins_home
ENV ftp_proxy    http://$http_server:$http_port/
ENV FTP_PROXY    http://$http_server:$http_port/
ENV http_proxy   http://$http_server:$http_port/
ENV HTTP_PROXY   http://$http_server:$http_port/
ENV https_proxy  http://$https_server:$https_port/
ENV HTTPS_PROXY  http://$https_server:$https_port/
ENV no_proxy     localhost

USER root
RUN apt-get update && apt-get install -y vim tree

# Set timezone
ENV TZ=America/Los_Angeles
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

USER jenkins

# Install jenkins plugins
COPY plugins.sh /usr/share/jenkins/ref/
COPY plugins.txt /usr/share/jenkins/ref/
RUN /usr/share/jenkins/ref/plugins.sh /usr/share/jenkins/ref/plugins.txt
RUN echo 2.0 > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state

# Users configuration step
COPY --chown=jenkins:jenkins users $JENKINS_HOME/users

# config.xml configuration step
COPY --chown=jenkins:jenkins config.xml $JENKINS_HOME/config.xml
RUN perl -pi -e "s/MASTER_NUM_EXECUTORS_VAR/$master_num_executors/g" $JENKINS_HOME/config.xml

# Jenkins workspace for sharing between containers
VOLUME $JENKINS_HOME

############################################
USER jenkins

# Start jenkins
ENTRYPOINT ["/usr/local/bin/jenkins.sh"]
