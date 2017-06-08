FROM centos:7

# Download tools
RUN yum -y install \
ca-certificates \
curl \
wget

RUN yum -y install \
bzr \
git \
mercurial \
openssh \
subversion

# Build tools
RUN yum -y install \
autoconf \
automake \
bzip2 \
bzip2-devel \
file \
gcc \
gcc-c++ \
glib2-devel \
glibc-devel \
ImageMagick \
ImageMagick-devel \
libcurl-devel \
libevent-devel \
libffi-devel \
libjpeg-devel \
libtool \
libwebp-devel \
libxml2-devel \
libxslt-devel \
libyaml-devel \
make \
mysql-devel \
ncurses-devel \
openssl-devel \
patch \
postgresql-devel \
readline-devel \
sqlite-devel \
xz \
xz-devel \
zlib-devel

# St2 package build debs
RUN yum -y install \
openldap-devel

# Enable remote pubkey access
RUN mkdir /root/.ssh && chmod 700 /root/.ssh && \
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC5SDguAT9dOSJn5ruZJgYKRw06aS0anuS+9bWhdZ/otllsbqqlKFdsUJcO4rwxxITbpHm+1/8NMyllXcjwkIoBrCLbal2E9nUp8aW/ZDpT7q1JOIQsmQ62XjB8HwqiWhpKJol0EQsOGhjOVkmTKVliid7G6bV6RMWEkoeVIYFpIeDk1NOJi7KGPlhecHUXwFQReey6QWGFj9APaqf8nRuQHHCsiB+fAqHDxJlyx5ipboSTSZrTnDonAMuhoRZm7qwa+LNYDiCyltaT39wx2Bm7V6T7JgjlQi7mBthQmdLXAHq6/Bm3qeftNEmzFOzdha1BBkTSNXZjZpwyp64CzLSO4UgIXq+7hpUA2cSBjROwxg1W9i9VSytfv6ZX37nniLwrFdYhSXqYy+1lFoD0kUzSpHhiw9JAoIahXw4jL9oEbC70lcNvKN72Ug7MNxsS3d8DV8gqRc0eWlAP20aTlc5X3rMWwtXB+89OCRm0vnIcpJRbjdeV2Vmf/0R6yQR+8Yj9k2t1V2v9HfC9/I/T2pliR4kMte1tZw/ZE7XLdjEguRWdb1mvkawqfUJaFGnoMpsvJpuBzH6l590e7A1Ygi5S8yjltc+SmMpzQQ5jc3q33yoxAuXYZShpPIfau3NOx6G2PxV9CQyXEw7qQHv3/BriH1krTLxKqNXMeQFDwzDt7Q== kitcken@test.com" >> /root/.ssh/authorized_keys

# Create busybee credentials and make busybee pkey available for root
COPY kitchen*  /root/.ssh/
RUN chmod 600 /root/.ssh/kitchen_rsa

RUN yum -y install openssh-server sudo && \
    ssh-keygen -t rsa -N '' -f /etc/ssh/ssh_host_rsa_key

# 1. small fix for SSH in ubuntu 13.10 (that's harmless everywhere else)
# 2. permit root logins and set simple password password and pubkey
# 3. change requiretty to !requiretty in /etc/sudoers
RUN sed -ri 's/^session\s+required\s+pam_loginuid.so$/session optional pam_loginuid.so/' /etc/pam.d/sshd && \
    sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -ri 's/^#?PubkeyAuthentication\s+.*/PubkeyAuthentication yes/' /etc/ssh/sshd_config && \
    sed -ri 's/requiretty/!requiretty/' /etc/sudoers && \
    echo 'root:docker.io' | chpasswd

# Set default locale to 'UTF-8' for the test execution environment
# Per https://github.com/CentOS/sig-cloud-instance-images/issues/71
RUN sed -ri 's/langs=en_US.UTF-8/langs=en_US.utf8/' /etc/yum.conf && \
       yum reinstall -y glibc-common

RUN yum -y install nc net-tools

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

# we can have ssh
EXPOSE 22

VOLUME [ "/sys/fs/cgroup" ]
CMD [ "/usr/sbin/init" ]
