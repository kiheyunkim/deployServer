FROM centos

RUN yum update -y

#경로 생성
RUN mkdir -p /root/deploy/

#작업 디렉토리 지정
WORKDIR /root/deploy/

RUN yum install wget -y

#JAVA 설치
RUN wget https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u292-b10/OpenJDK8U-jdk_x64_linux_hotspot_8u292b10.tar.gz
RUN tar xzf OpenJDK8U-jdk_x64_linux_hotspot_8u292b10.tar.gz
RUN ln -s ./jdk8u292-b10 ./jdk
ENV JAVA_HOME /root/deploy/jdk
#https://wiki.jenkins.io/display/JENKINS/Jenkins+got+java.awt.headless+problem
#OpenJdk에 빠진 부분
RUN yum install dejavu-sans-fonts -y
RUN yum install fontconfig -y
ENV PATH $PATH:$JAVA_HOME/bin

#SCP 사용을 위함
RUN yum install openssh-clients -y

#Tomcat 설치
RUN wget https://mirror.navercorp.com/apache/tomcat/tomcat-8/v8.5.65/bin/apache-tomcat-8.5.65.tar.gz
RUN tar -zvxf apache-tomcat-8.5.65.tar.gz
RUN ln -s ./apache-tomcat-8.5.65 ./tomcat
ENV CATALINA_HOME /root/deploy/tomcat
ENV PATH $PATH:$CATALINA_HOME/bin

#Jenkins 설치
RUN wget https://get.jenkins.io/war-stable/2.277.3/jenkins.war -P ${CATALINA_HOME}/webapps
ENTRYPOINT ["catalina.sh", "run"]

#deploy script
#deploy script path
RUN mkdir -p /root/deploy/scripts
#script1 deploy make dir
RUN printf "#!/bin/bash \
\n# paramter newPath\
\nif [ \"\$#\" -ne \"1\" ]\
\nthen\
\n     echo 'it requires 1 argument'\
\n     exit -1\
\nelse\
\n     mkdir -p \$1 \
\nfi" > ./scripts/make_build_path.sh
RUN chmod 744 ./scripts/make_build_path.sh

#script2 file transfer script
RUN printf "#!/bin/bash \
\n# paramter targetFile remoteUser remoteServerAddress remoteTargetPath \
\nif [ \"\$#\" -ne \"4\" ]\
\nthen\
\n    echo 'it requires 4 argument'\
\n    exit -1\
\nfi\
\n\
\nif [ ! -f \$1 ]\
\nthen\
\n     echo 'file Not Exist'\
\n     exit -1\
\nfi\
\n\
\nscp \$2 \$3@\$4:\$5\
\n\
\nif [ \"\$?\" -ne \"0\" ]\
\nthen\
\n    echo 'file transfer fail'\
\n    exit -1\
\nfi" > ./scripts/transfer_to_remote.sh
RUN chmod 744 ./scripts/transfer_to_remote.sh

EXPOSE 8080