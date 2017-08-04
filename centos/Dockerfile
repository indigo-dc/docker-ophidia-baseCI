FROM indigodatacloud/packaging:bcentos_latest
MAINTAINER <ophidia-info@cmcc.it>

USER root

RUN yum -y update
RUN yum -y install epel-release
RUN yum -y install http://repo.mysql.com/mysql-community-release-el7-7.noarch.rpm
    
RUN yum -y groupinstall 'development tools'

RUN yum -y install \
    curl \
    flex-devel \
    git \
    compat-guile18 \
    compat-guile18-devel \
    guile-devel \
    graphviz\* \
    gsl-devel \
    gsl \
    gtk2\* \
    jansson\* \
    libcurl-devel \
    libssh2-devel \
    libtool-ltdl\* \
    libxml2\* \
    mpich\* \
    mysql-community-devel \
    openssl \
    openssl-devel \
    policycoreutils-python \
    readline\* \
    sudo \
    wget \
    indent \
    lcov \
	python-pip

RUN pip install gcovr

RUN yum -y install \
	httpd \
	php \
	mysql-community-server \
	munge \
	munge-devel \
	munge-libs

USER jenkins

ENV CC /usr/lib64/mpich/bin/mpicc
ENV CPPFLAGS "-I/usr/local/ophidia/extra/include"
ENV LDFLAGS "-L/usr/local/ophidia/extra/lib"
ENV LIB -ldl

RUN sudo mkdir -p /var/run/munge && \
	sudo /bin/bash -c "dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key" && \
	sudo chown -R munge:munge /var/run/munge && \
	sudo chown -R munge:munge /etc/munge && \
	sudo chmod 0711 /var/log/munge && \ 
	sudo chmod 0755 /var/run/munge && \
	sudo chmod 0400 /etc/munge/munge.key

RUN sudo mkdir -p /usr/local/ophidia/extra/src && \
    sudo mkdir -p /var/www/html/ophidia && \
    sudo chown -R jenkins:jenkins /usr/local/ophidia && \
    sudo chown -R jenkins:jenkins /var/www/html/ophidia

RUN cd /usr/local/ophidia/extra/src && \
    wget http://ftp.gnu.org/gnu/libmatheval/libmatheval-1.1.11.tar.gz && \
    wget http://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz && \
    wget ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-4.4.0.tar.gz && \
    wget http://www.lip.pt/~david/gsoap_2.8.27.zip && \
    wget https://github.com/SchedMD/slurm/archive/master.tar.gz && \
    tar zxvf libmatheval-1.1.11.tar.gz && \
    tar zxvf hdf5-1.8.17.tar.gz && \
    tar zxvf netcdf-4.4.0.tar.gz && \
    unzip gsoap_2.8.27.zip && \
	tar zxvf master.tar.gz

RUN cd /usr/local/ophidia/extra/src/libmatheval-1.1.11 && \
    ./configure --prefix=/usr/local/ophidia/extra  && \
    make -s > /dev/null && \
    make install > /dev/null && \
    cd /usr/local/ophidia/extra/src/hdf5-1.8.17 && \
    ./configure \
        --prefix=/usr/local/ophidia/extra \
        --enable-parallel && \
    make -s > /dev/null && \
    make install > /dev/null && \
    cd /usr/local/ophidia/extra/src/netcdf-4.4.0 && \
    ./configure \
        --prefix=/usr/local/ophidia/extra \
        --enable-parallel-tests && \
    make -s > /dev/null && \
    make install > /dev/null && \
    cd /usr/local/ophidia/extra/src/gsoap-2.8 && \
    ./configure \
        --prefix=/usr/local/ophidia/extra && \
    make -s > /dev/null && \
    make install > /dev/null && \
	cd /usr/local/ophidia/extra/src/slurm-master && \
	./configure \
		--prefix=/usr/local/ophidia/extra/ \
		--sysconfdir=/usr/local/ophidia/extra/etc/ && \
	make -s > /dev/null && \
	make install > /dev/null

RUN mkdir -p /usr/local/ophidia/extra/etc
COPY slurm.conf /usr/local/ophidia/extra/etc/slurm.conf

RUN rm -rf /usr/local/ophidia/extra/src

ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/usr/local/ophidia/extra/bin:$PATH

RUN sudo mysql_install_db --user=mysql
 
USER root

EXPOSE 22 

CMD ["/usr/sbin/sshd", "-D"]
