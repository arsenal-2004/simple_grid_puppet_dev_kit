FROM cern/cc7-base
LABEL Maintainer="Mayank Sharma (imptodefeat@gmail.com)"
RUN rpm -ivh https://yum.puppetlabs.com/puppet5/puppet5-release-el-7.noarch.rpm
RUN yum -y --disablerepo=cern,el7,epel install puppetserver puppet-agent 
RUN yum -y install openssh-server openssh-clients

RUN yum -y install gcc-c++ patch readline readline-devel zlib zlib-devel \
   libyaml-devel libffi-devel openssl-devel make \
   bzip2 autoconf automake libtool bison iconv-devel sqlite-devel gnupg2

RUN gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN curl -sSL https://rvm.io/mpapis.asc | gpg --import -; \
curl -L get.rvm.io | bash -s stable

RUN /bin/bash -c "source /etc/profile.d/rvm.sh; rvm reload; rvm requirements run; rvm install 2.4; gem install r10k"

# modules for Production environment (Install stage for Config Master. The module is mounted in init.sh)
RUN /opt/puppetlabs/bin/puppet module install puppetlabs-stdlib --version 5.0.0
RUN /opt/puppetlabs/bin/puppet module install puppetlabs-docker  --version 2.0.0
RUN /opt/puppetlabs/bin/puppet module install puppetlabs-git --version 0.5.0
RUN /opt/puppetlabs/bin/puppet module install puppetlabs-vcsrepo --version 2.4.0
RUN /opt/puppetlabs/bin/puppet module install puppet-python --version 2.1.1
RUN /opt/puppetlabs/bin/puppet module install stahnma-epel --version 1.3.1
RUN /opt/puppetlabs/bin/puppet module install puppet-ssh_keygen --version 3.0.1

# Directories for puppet environments
RUN mkdir /etc/puppetlabs/code/environments/install
RUN mkdir /etc/puppetlabs/code/environments/config
RUN mkdir /etc/puppetlabs/code/environments/pre_deploy
RUN mkdir /etc/puppetlabs/code/environments/deploy
RUN mkdir /etc/puppetlabs/code/environments/test
RUN mkdir /etc/puppetlabs/code/environments/cleanup

# Volumes for puppet environments
VOLUME ["/etc/puppetlabs/code/environments/install"]
VOLUME ["/etc/puppetlabs/code/environments/config"]
VOLUME ["/etc/puppetlabs/code/environments/pre_deploy"]
VOLUME ["/etc/puppetlabs/code/environments/deploy"]
VOLUME ["/etc/puppetlabs/code/environments/test"]
VOLUME ["/etc/puppetlabs/code/environments/cleanup"]

# Volumes for puppet module inside each environment
VOLUME ["/etc/puppetlabs/code/environments/install/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/config/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/pre_deploy/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/deploy/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/test/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/cleanup/site/simple_grid/"]
VOLUME ["/etc/puppetlabs/code/environments/production/modules/simple_grid/"]

# ENC
RUN mkdir /enc
VOLUME [ "/enc" ]

# Puppetserver config
RUN mkdir /data
VOLUME [ "/data" ]

RUN mkdir /simple_grid
VOLUME [ "/simple_grid" ]

#systemd
ENV container docker
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/init"]