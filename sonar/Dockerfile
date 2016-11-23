#
# Creates a docker container with SonarQube, incl. several plugins
# Since the original Dockerfile does not support plugins, I
# had to extend the Dockerfile
#
# Original: https://hub.docker.com/_/sonarqube/
#

FROM java:openjdk-8u45-jdk

MAINTAINER Marcel Birkner <marcel.birkner@codecentric.de>

ENV SONARQUBE_HOME /opt/sonarqube

# Http port
EXPOSE 9000

# H2 Database port
EXPOSE 9092

# Database configuration
# Defaults to using H2
ENV SONARQUBE_JDBC_USERNAME sonar
ENV SONARQUBE_JDBC_PASSWORD sonar
ENV SONARQUBE_JDBC_URL jdbc:h2:tcp://localhost:9092/sonar

ENV SONAR_VERSION 5.6.3

ENV SONAR_DOWNLOAD_URL https://sonarsource.bintray.com/Distribution

# pub   2048R/D26468DE 2015-05-25
#      Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
# uid       [ unknown] sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
# sub   2048R/06855C1D 2015-05-25
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys F1182E81C792928921DBCAB4CFCA4A29D26468DE

RUN set -x \
	&& cd /opt \
	&& curl -o sonarqube.zip -fSL $SONAR_DOWNLOAD_URL/sonarqube/sonarqube-$SONAR_VERSION.zip \
	&& curl -o sonarqube.zip.asc -fSL $SONAR_DOWNLOAD_URL/sonarqube/sonarqube-$SONAR_VERSION.zip.asc \
	&& gpg --verify sonarqube.zip.asc \
	&& unzip sonarqube.zip \
	&& mv sonarqube-$SONAR_VERSION sonarqube \
  && rm sonarqube.zip* \
	&& rm -rf $SONARQUBE_HOME/bin/*

# Installing Plugins
RUN cd /opt/sonarqube/extensions/plugins/ \
  && curl -o sonar-java-plugin-4.2.1.6971.jar -fSL $SONAR_DOWNLOAD_URL/sonar-java-plugin/sonar-java-plugin-4.2.1.6971.jar \
  && curl -o sonar-csharp-plugin-5.5.0.479.jar -fSL $SONAR_DOWNLOAD_URL/sonar-csharp-plugin/sonar-csharp-plugin-5.5.0.479.jar \
  && curl -o sonar-web-plugin-2.5.0.476.jar -fSL $SONAR_DOWNLOAD_URL/sonar-web-plugin/sonar-web-plugin-2.5.0.476.jar \
  && curl -o sonar-scm-git-plugin-1.2.jar -fSL $SONAR_DOWNLOAD_URL/sonar-scm-git-plugin/sonar-scm-git-plugin-1.2.jar \
  && curl -o sonar-auth-gitlab-plugin-1.0.0.jar -fSL http://nexus.talanlabs.com/content/groups/public_release/com/synaptix/sonar-auth-gitlab-plugin/1.0.0/sonar-auth-gitlab-plugin-1.0.0.jar \
  && curl -o sonar-gitlab-plugin-1.7.0.jar -fSL http://nexus.talanlabs.com/content/groups/public_release/com/synaptix/sonar-gitlab-plugin/1.7.0/sonar-gitlab-plugin-1.7.0.jar

VOLUME ["$SONARQUBE_HOME/data", "$SONARQUBE_HOME/extensions"]

WORKDIR $SONARQUBE_HOME
COPY run.sh $SONARQUBE_HOME/bin/
RUN chmod +x $SONARQUBE_HOME/bin/run.sh

ENTRYPOINT ["./bin/run.sh"]
