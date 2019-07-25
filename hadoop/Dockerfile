FROM bizfty/centos:latest
MAINTAINER johnson <johnson@bizfty.com>

# passwordless ssh
RUN rm -rf /etc/ssh/ssh_host_dsa_key 
RUN rm -rf /etc/ssh/ssh_host_rsa_key
RUN rm -rf /root/.ssh/id_rsa
RUN rm -rf /root/.ssh/authorized_keys
RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key
RUN ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN ssh-keygen -q -N "" -t rsa -f /root/.ssh/id_rsa
RUN cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys

# java
RUN mkdir /usr/java/
RUN curl https://download.java.net/java/GA/jdk12.0.2/e482c34c86bd4bf8b56c0b35558996b9/10/GPL/openjdk-12.0.2_linux-x64_bin.tar.gz | tar -xz -C /usr/java/
RUN ln -s /usr/java/jdk-12.0.2 /usr/java/default
ENV JAVA_HOME /usr/java/default
ENV PATH $PATH:$JAVA_HOME/bin
RUN ln -s $JAVA_HOME/bin/java /usr/bin/java

# hadoop
RUN curl http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz | tar -xz -C /usr/local/
RUN cd /usr/local && ln -s ./hadoop-3.2.0 hadoop

RUN mkdir /usr/local/hadoop/tmp
RUN mkdir /usr/local/hadoop/hdfs
RUN mkdir /usr/local/hadoop/hdfs/data
RUN mkdir /usr/local/hadoop/hdfs/name

ENV HADOOP_HOME /usr/local/hadoop
ENV PATH $PATH:$HADOOP_HOME/bin

ENV HADOOP_COMMON_HOME /usr/local/hadoop
ENV HADOOP_HDFS_HOME /usr/local/hadoop
ENV HADOOP_MAPRED_HOME /usr/local/hadoop
ENV HADOOP_YARN_HOME /usr/local/hadoop
ENV HADOOP_CONF_DIR /usr/local/hadoop/etc/hadoop
ENV YARN_CONF_DIR $HADOOP_HOME/etc/hadoop
ENV HADOOP_SSH_OPTS ="-p 20022"

RUN cd /usr/local/hadoop/etc/hadoop && cp hadoop-env.sh hadoop-env.sh.template
RUN sed -i '/export JAVA_HOME/ s:.*:export JAVA_HOME=/usr/java/default\nexport HADOOP_HOME=/usr/local/hadoop\n:' $HADOOP_HOME/etc/hadoop/hadoop-env.sh
RUN sed -i '/export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:' $HADOOP_HOME/etc/hadoop/hadoop-env.sh
RUN sed -i '/export HADOOP_SSH_OPTS/ s:.*:export HADOOP_SSH_OPTS="-p 20022":' $HADOOP_HOME/etc/hadoop/hadoop-env.sh


ENV HDFS_DATANODE_USER root  
ENV HDFS_DATANODE_SECURE_USER hdfs  
ENV HDFS_NAMENODE_USER root  
ENV HDFS_SECONDARYNAMENODE_USER root
ENV YARN_RESOURCEMANAGER_USER root
ENV HADOOP_SECURE_DN_USER yarn
ENV HADOOP_DATANODE_SECURE_DN_USER hdfs
ENV YARN_NODEMANAGER_USER root

# pseudo distributed
RUN cd /usr/local/hadoop/etc/hadoop && cp core-site.xml core-site.xml.template

#row 19 write content
#sed '19i <property><name>fs.defaultFS</name><value>hdfs://127.0.0.1:9000</value></property>' /usr/local/hadoop/etc/hadoop/core-site.xml
#RUN sed -i '/<\/configuration>/i <property><name>fs.defaultFS</name><value>hdfs://192.168.201.56:9000</value></property>' /usr/local/hadoop/etc/hadoop/core-site.xml
RUN sed -i '/<\/configuration>/i <property><name>fs.defaultFS</name><value>hdfs://127.0.0.1:9000</value></property>' /usr/local/hadoop/etc/hadoop/core-site.xml
RUN sed -i '/<\/configuration>/i <property><name>hadoop.tmp.dir</name><value>/usr/local/hadoop/tmp</value></property>' /usr/local/hadoop/etc/hadoop/core-site.xml

RUN cd /usr/local/hadoop/etc/hadoop && cp hdfs-site.xml hdfs-site.xml.template
RUN sed -i '/<\/configuration>/i <property><name>dfs.replication</name><value>1</value></property>' /usr/local/hadoop/etc/hadoop/hdfs-site.xml

RUN cd /usr/local/hadoop/etc/hadoop && cp mapred-site.xml mapred-site.xml.template
RUN sed -i '/<\/configuration>/i <property><name>map1ereduce.framework.name</name><value>yarn</value></property>' /usr/local/hadoop/etc/hadoop/mapred-site.xml

RUN cd /usr/local/hadoop/etc/hadoop && cp yarn-site.xml yarn-site.xml.template
RUN sed -i '/<\/configuration>/i <property><name>yarn.nodemanager.aux-services</name><value>mapreduce_shuffle</value></property>' /usr/local/hadoop/etc/hadoop/yarn-site.xml

RUN hdfs namenode -format

RUN cd /usr/local/hadoop/sbin



#ADD ssh_config /root/.ssh/config
#RUN chmod 600 /root/.ssh/config
#RUN chown root:root /root/.ssh/config

#ADD bootstrap.sh /etc/bootstrap.sh
#RUN chown root:root /etc/bootstrap.sh
#RUN chmod 700 /etc/bootstrap.sh

#ENV BOOTSTRAP /etc/bootstrap.sh

# workingaround docker.io build error
#RUN ls -la /usr/local/hadoop/etc/hadoop/*-env.sh
#RUN chmod +x /usr/local/hadoop/etc/hadoop/*-env.sh
#RUN ls -la /usr/local/hadoop/etc/hadoop/*-env.sh

#RUN service sshd start && $HADOOP_HOME/etc/hadoop/hadoop-env.sh && $HADOOP_HOME/sbin/start-dfs.sh
#RUN service sshd start && $HADOOP_HOME/etc/hadoop/hadoop-env.sh && $HADOOP_HOME/sbin/start-dfs.sh


# Hdfs ports
# hdfs的web页面默认端口是9870 yarn的web页面端口是8088
#Namenode ports
EXPOSE 9871 9870 9820
#Secondary NN ports: 
EXPOSE 9869 9868
#Yarn ports
EXPOSE 8030 8031 8032 8033 8040 8042 8088
#Datanode ports: 
EXPOSE 9867 9866 9865 9864
#Other ports
EXPOSE 49707 20022
