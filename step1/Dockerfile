FROM registry.access.redhat.com/rhel7.1
MAINTAINER mike

# Config Proxy
#RUN echo proxy=http://web-proxy.houston.hp.com:8080 >> /etc/yum.conf
#RUN echo export ftp_proxy=http://proxy.houston.hp.com:8080  >> /root/.bashrc
#RUN echo export http_proxy=http://proxy.houston.hp.com:8080  >> /root/.bashrc
#RUN echo export https_proxy=http://proxy.houston.hp.com:8080  >> /root/.bashrc
#RUN source /root/.bashrc

# Install oracle12 package
ADD linuxamd64_12102_database_1of2.zip /tmp/install/linuxamd64_12102_database_1of2.zip
ADD linuxamd64_12102_database_2of2.zip /tmp/install/linuxamd64_12102_database_2of2.zip
RUN yum -y install unzip binutils compat-libcap1 compat-libstdc++-33 compat-libstdc++-33.i686 gcc gcc-c++ glibc.i686 glibc glibc-devel glibc-devel.i686 ksh libgcc.i686 libgcc libstdc++ libstdc++.i686 libstdc++-devel libstdc++-devel.i686 libaio libaio.i686 libaio-devel libaio-devel.i686 libXext libXext.i686 libXtst libXtst.i686 libX11 libX11.i686 libXau libXau.i686 libxcb libxcb.i686 libXi libXi.i686 make sysstat vte3 smartmontools unixODBC unixODBC-devel
RUN cd /tmp/install && unzip linuxamd64_12102_database_1of2.zip && unzip linuxamd64_12102_database_2of2.zip && rm *.zip

# Install gosu
#RUN curl -x http://proxy.houston.hp.com:8080 -o /usr/local/bin/gosu -SL 'https://github.com/tianon/gosu/releases/download/1.4/gosu-amd64' \
#    && chmod +x /usr/local/bin/gosu

# Install gosu
RUN curl -o /usr/local/bin/gosu -SL 'https://github.com/tianon/gosu/releases/download/1.4/gosu-amd64' \
    && chmod +x /usr/local/bin/gosu

# Create oracle user
RUN groupadd -g 54321 oinstall && groupadd -g 54322 dba
RUN useradd -m -u 54321 -g oinstall -G dba oracle
RUN echo "oracle:oracle" | chpasswd
COPY sysctl.conf oraInst.loc /etc/
COPY limits.conf /tmp/
RUN cat /tmp/limits.conf >> /etc/security/limits.conf

ENV ORACLE_BASE=/app/oracle
ENV ORACLE_HOME=$ORACLE_BASE/product/12.1.0/dbhome_1
ENV PATH=$ORACLE_HOME/bin:$PATH
ENV NLS_DATE_FORMAT=YYYY-MM-DD\ HH24:MI:SS \
    ORACLE_DATA=/data/oracle \
    ORACLE_SID=ORCL \
    ORACLE_HOME_LISTNER=$ORACLE_HOME

COPY *.rsp install.sh install_rlwrap.sh /tmp/install/

RUN mkdir -p $ORACLE_BASE && chown -R oracle:oinstall $ORACLE_BASE && \
    chmod -R 775 $ORACLE_BASE && \
    mkdir -p /app/oraInventory && \
    chown -R oracle:oinstall /app/oraInventory && \
    chmod -R 775 /app/oraInventory && \
    chmod 664 /etc/oraInst.loc && \
    chmod a+x /tmp/install/install.sh


