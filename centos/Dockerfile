FROM centos:latest
MAINTAINER johnson <johnson@bizfty.com>
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN yum-config-manager --add-repo http://mirrors.aliyun.com/repo/Centos-7.repo
RUN yum-config-manager --add-repo http://mirrors.163.com/.help/CentOS7-Base-163.repo 
RUN yum -y update libselinux
RUN yum -y install net-tools
RUN yum -y install iproute
RUN yum -y install vim-enhanced
RUN yum -y install sudo passwd
RUN yum -y install iptables
RUN yum -y install openssl openssh-server
RUN yum -y install openssh-clients
RUN ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N ''
RUN ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N ''
RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key  -N ''
RUN sed -i "s/#UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config
RUN sed -i "s/UsePAM.*/UsePAM no/g" /etc/ssh/sshd_config
RUN sed -i "s/#Port.*/Port 20022/g" /etc/ssh/sshd_config
RUN echo "root:123" | chpasswd
EXPOSE 20022
VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/init"]