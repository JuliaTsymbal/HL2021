FROM ubuntu:18.04

RUN apt-get update -y && apt-get install vim -y && apt-get install wget -y && apt-get install ssh -y && apt-get install openjdk-11-jdk -y && apt-get install sudo -y
RUN useradd -m hduser && echo "hduser:supergroup" | chpasswd && adduser hduser sudo && echo "hduser     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && cd /usr/bin/ && sudo ln -s python3 python

COPY ssh_config /etc/ssh/ssh_config

WORKDIR /home/hduser

USER hduser
# changed here to java 11
RUN wget -q https://downloads.apache.org/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz && tar zxvf hadoop-3.3.0.tar.gz && rm hadoop-3.3.0.tar.gz
RUN ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa && cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys && chmod 0600 ~/.ssh/authorized_keys

# setup variables for java and hadoop
RUN export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-arm64
RUN export PATH=$PATH:$JAVA_HOME/bin
RUN export HADOOP_CLASSPATH=/usr/lib/jvm/java-11-openjdk-arm64/lib/tools.jar

ENV HDFS_NAMENODE_USER hduser
ENV HDFS_DATANODE_USER hduser
ENV HDFS_SECONDARYNAMENODE_USER hduser

ENV YARN_RESOURCEMANAGER_USER hduser
ENV YARN_NODEMANAGER_USER hduser

# also java 11
ENV HADOOP_HOME /home/hduser/hadoop-3.3.0
RUN echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-arm64" >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
COPY core-site.xml $HADOOP_HOME/etc/hadoop/
COPY hdfs-site.xml $HADOOP_HOME/etc/hadoop/
COPY yarn-site.xml $HADOOP_HOME/etc/hadoop/

COPY docker-entrypoint.sh $HADOOP_HOME/etc/hadoop/

ENV PATH $PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

ADD examples/ examples/ 

COPY --chown=hduser ProcessUnits.java ProcessUnits.java
COPY --chown=hduser input.txt input.txt

EXPOSE 50070 50075 50010 50020 50090 8020 9000 9864 9870 10020 19888 8088 8030 8031 8032 8033 8040 8042 22

ENTRYPOINT ["/home/hduser/hadoop-3.3.0/etc/hadoop/docker-entrypoint.sh"]
