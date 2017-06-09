FROM centos:7

RUN yum -y install openssh

RUN mkdir /root/.ssh && chmod 700 /root/.ssh && \
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC5SDguAT9dOSJn5ruZJgYKRw06aS0anuS+9bWhdZ/otllsbqqlKFdsUJcO4rwxxITbpHm+1/8NMyllXcjwkIoBrCLbal2E9nUp8aW/ZDpT7q1JOIQsmQ62XjB8HwqiWhpKJol0EQsOGhjOVkmTKVliid7G6bV6RMWEkoeVIYFpIeDk1NOJi7KGPlhecHUXwFQReey6QWGFj9APaqf8nRuQHHCsiB+fAqHDxJlyx5ipboSTSZrTnDonAMuhoRZm7qwa+LNYDiCyltaT39wx2Bm7V6T7JgjlQi7mBthQmdLXAHq6/Bm3qeftNEmzFOzdha1BBkTSNXZjZpwyp64CzLSO4UgIXq+7hpUA2cSBjROwxg1W9i9VSytfv6ZX37nniLwrFdYhSXqYy+1lFoD0kUzSpHhiw9JAoIahXw4jL9oEbC70lcNvKN72Ug7MNxsS3d8DV8gqRc0eWlAP20aTlc5X3rMWwtXB+89OCRm0vnIcpJRbjdeV2Vmf/0R6yQR+8Yj9k2t1V2v9HfC9/I/T2pliR4kMte1tZw/ZE7XLdjEguRWdb1mvkawqfUJaFGnoMpsvJpuBzH6l590e7A1Ygi5S8yjltc+SmMpzQQ5jc3q33yoxAuXYZShpPIfau3NOx6G2PxV9CQyXEw7qQHv3/BriH1krTLxKqNXMeQFDwzDt7Q== kitcken@test.com" >> /root/.ssh/authorized_keys

COPY kitchen_rsa  /root/.ssh/
COPY kitchen_rsa.pub  /root/.ssh/
RUN chmod 600 /root/.ssh/kitchen_rsa

RUN yum -y install openssh-server sudo && \
    ssh-keygen -t rsa -N '' -f /etc/ssh/ssh_host_rsa_key

RUN sed -ri 's/^session\s+required\s+pam_loginuid.so$/session optional pam_loginuid.so/' /etc/pam.d/sshd && \
    sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -ri 's/^#?PubkeyAuthentication\s+.*/PubkeyAuthentication yes/' /etc/ssh/sshd_config && \
    sed -ri 's/requiretty/!requiretty/' /etc/sudoers && \
    echo 'root:docker.io' | chpasswd

RUN sed -ri 's/langs=en_US.UTF-8/langs=en_US.utf8/' /etc/yum.conf && \
       yum reinstall -y glibc-common

RUN yum -y install nc net-tools iptables

ENV container docker

RUN yum -y update; \
    yum -y install systemd; yum clean all;

RUN cd /lib/systemd/system/sysinit.target.wants/; ls -1 | grep -v systemd-tmpfiles-setup.service | xargs rm; \
      rm -f /etc/systemd/system/*.wants/*;\
      rm -f /lib/systemd/system/local-fs.target.wants/*; \
      rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
      rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
      rm -f /lib/systemd/system/basic.target.wants/*;\
      rm -f /lib/systemd/system/anaconda.target.wants/*;\
      systemctl preset sshd;

EXPOSE 22

VOLUME [ "/sys/fs/cgroup" ]
CMD [ "/usr/sbin/init" ]
