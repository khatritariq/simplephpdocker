FROM centos:centos7

MAINTAINER Tariq <kha3tariq@gmail.com>

ARG uid=1000
ARG gid=1000

# -----------------------------------------------------------------------------
# Import the RPM GPG keys for Repositories
# -----------------------------------------------------------------------------
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	&& rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

# -----------------------------------------------------------------------------
# Utilities
# -----------------------------------------------------------------------------
RUN yum -y install wget
RUN yum -y install vim

# -----------------------------------------------------------------------------
# Apache
# -----------------------------------------------------------------------------
RUN cd /etc/yum.repos.d && wget https://repo.codeit.guru/codeit.el`rpm -q --qf "%{VERSION}" $(rpm -q --whatprovides redhat-release)`.repo
# -----------------------------------------------------------------------------
#  PHP
# -----------------------------------------------------------------------------
RUN	yum -y update \
	&& yum --setopt=tsflags=nodocs -y install \
	gcc \
	gcc-c++ \
	mod_ssl \
	httpd  \
	php72w \
	php72w-cli \
	php72w-devel \
	php72w-mysqlnd \
	php72w-pdo \
	php72w-mbstring \
	php72w-soap \
	php72w-gd \
	php72w-xml \
	php72w-ldap \
	php72w-pecl-apcu \
	unzip \
	libXrender fontconfig libXext urw-fonts \
	ImageMagick ImageMagick-devel \
	&& rm -rf /var/cache/yum/* \
	&& yum clean all
# -----------------------------------------------------------------------------
# UTC Timezone & Networking
# -----------------------------------------------------------------------------
RUN ln -sf /usr/share/zoneinfo/UTC /etc/localtime \
	&& echo "NETWORKING=yes" > /etc/sysconfig/network

# -----------------------------------------------------------------------------
# Install DB2 PDO driver
# -----------------------------------------------------------------------------
#ENV DB2EXPRESSC_URL https://s3-ap-southeast-1.amazonaws.com/naqoda/downloads/ibm_data_server_driver_package_linuxx64_v10.5.tar.gz
#ENV IBM_DB_HOME /opt/ibm/dsdriver
#ENV LD_LIBRARY_PATH /opt/ibm/dsdriver/odbc_cli_driver/linuxamd64/clidriver/lib
#
#RUN mkdir /opt/ibm \
#    && curl -fSLo /opt/ibm/expc.tar.gz $DB2EXPRESSC_URL  \
#    && cd /opt/ibm && tar xf expc.tar.gz \
#    && rm /opt/ibm/expc.tar.gz \
#	&& cp $IBM_DB_HOME/php_driver/linuxamd64/php64/ibm_db2_5.3.6_nts.so /usr/lib64/php/modules/ibm_db2.so \
#	&& cp $IBM_DB_HOME/php_driver/linuxamd64/php64/pdo_ibm_5.3.6_nts.so /usr/lib64/php/modules/pdo_ibm.so \
#	&& cd /opt/ibm/dsdriver/odbc_cli_driver/linuxamd64 \
#    && tar xf ibm_data_server_driver_for_odbc_cli.tar.gz \
#	&& echo 'extension=ibm_db2.so' > /etc/php.d/pdo_db2.ini \
#	&& echo 'extension=pdo_ibm.so' >> /etc/php.d/pdo_db2.ini
#
#COPY modules/php71/* /usr/lib64/php/modules/

# -----------------------------------------------------------------------------
# Build Kafka PHP extension
# -----------------------------------------------------------------------------
#RUN curl -fSLo /tmp/librdkafka-master.zip https://github.com/edenhill/librdkafka/archive/master.zip \
#	&& cd /tmp \
#	&& unzip librdkafka-master.zip  \
#	&& cd librdkafka-master \
#	&& ./configure \
#	&& make \
#	&& make install
#
#RUN pecl install channel://pecl.php.net/rdkafka-2.0.0 \
#	&& echo 'extension=rdkafka.so' > /etc/php.d/kafka.ini

# -----------------------------------------------------------------------------
# Global Apache configuration changes
# Disable Apache directory indexes
# Disable Apache language based content negotiation
# Disable all Apache modules and enable the minimum
# Enable ServerStatus access via /_httpdstatus to local client
# Apache tuning
# -----------------------------------------------------------------------------
RUN sed -i \
	-e 's~^ServerSignature On$~ServerSignature Off~g' \
	-e 's~^ServerTokens OS$~ServerTokens Prod~g' \
	-e 's~^DirectoryIndex \(.*\)$~DirectoryIndex \1 index.php~g' \
	-e 's~^Group apache$~Group app~g' \
	-e 's~^IndexOptions \(.*\)$~#IndexOptions \1~g' \
	-e 's~^IndexIgnore \(.*\)$~#IndexIgnore \1~g' \
	-e 's~^AddIconByEncoding \(.*\)$~#AddIconByEncoding \1~g' \
	-e 's~^AddIconByType \(.*\)$~#AddIconByType \1~g' \
	-e 's~^AddIcon \(.*\)$~#AddIcon \1~g' \
	-e 's~^DefaultIcon \(.*\)$~#DefaultIcon \1~g' \
	-e 's~^ReadmeName \(.*\)$~#ReadmeName \1~g' \
	-e 's~^HeaderName \(.*\)$~#HeaderName \1~g' \
	-e 's~^LanguagePriority \(.*\)$~#LanguagePriority \1~g' \
	-e 's~^ForceLanguagePriority \(.*\)$~#ForceLanguagePriority \1~g' \
	-e 's~^AddLanguage \(.*\)$~#AddLanguage \1~g' \
	-e '/#<Location \/server-status>/,/#<\/Location>/ s~^#~~' \
	-e '/<Location \/server-status>/,/<\/Location>/ s~Allow from .example.com~Allow from localhost 127.0.0.1~' \
	-e 's~^StartServers \(.*\)$~StartServers 3~g' \
	-e 's~^MinSpareServers \(.*\)$~MinSpareServers 3~g' \
	-e 's~^MaxSpareServers \(.*\)$~MaxSpareServers 3~g' \
	-e 's~^ServerLimit \(.*\)$~ServerLimit 10~g' \
	-e 's~^MaxClients \(.*\)$~MaxClients 10~g' \
	-e 's~^MaxRequestsPerChild \(.*\)$~MaxRequestsPerChild 1000~g' \
	/etc/httpd/conf/httpd.conf

RUN sed -i \
	-e 's~^\(LoadModule .*\)$~#\1~g' \
	/etc/httpd/conf.modules.d/00-dav.conf

RUN sed -i \
	-e 's~^\(LoadModule .*\)$~#\1~g' \
	/etc/httpd/conf.modules.d/00-lua.conf

#RUN sed -i \
#	-e 's~^\(LoadModule .*\)$~#\1~g' \
#	/etc/httpd/conf.modules.d/00-proxy.conf

# -----------------------------------------------------------------------------
# Disable the default SSL Virtual Host
# -----------------------------------------------------------------------------
RUN sed -i \
	-e '/<VirtualHost _default_:443>/,/#<\/VirtualHost>/ s~^~#~' \
	/etc/httpd/conf.d/ssl.conf

# -----------------------------------------------------------------------------
# Limit process for the application user
# -----------------------------------------------------------------------------
#RUN { \
#		echo ''; \
#		echo $'apache\tsoft\tnproc\t30'; \
#		echo $'apache\thard\tnproc\t50'; \
#		echo $'app\tsoft\tnproc\t30'; \
#		echo $'app\thard\tnproc\t50'; \
#	} >> /etc/security/limits.conf

# -----------------------------------------------------------------------------
# Global PHP configuration changes
# -----------------------------------------------------------------------------
RUN sed -i \
	-e 's~^;date.timezone =$~date.timezone = UTC~g' \
	-e 's~^;user_ini.filename =$~user_ini.filename =~g' \
	-e 's~^; max_input_vars.*$~max_input_vars = 4000~g' \
	-e 's~^;always_populate_raw_post_data = -1$~always_populate_raw_post_data = -1~g' \
	-e 's~^upload_max_filesize.*$~upload_max_filesize = 8M~g' \
	-e 's~^post_max_size.*$~post_max_size = 12M~g' \
	/etc/php.ini


# -----------------------------------------------------------------------------
# ImageMagick
# -----------------------------------------------------------------------------
#RUN echo '' | pecl install imagick
#RUN echo "extension=imagick.so" > /etc/php.d/imagick.ini

# -----------------------------------------------------------------------------
# https://wkhtmltopdf.org
# with dependencies: libXrender fontconfig libXext urw-fonts
# -----------------------------------------------------------------------------
#RUN cd /usr/local/bin \
#	&& curl -fSLo wkhtmltox.tar.xz https://downloads.wkhtmltopdf.org/0.12/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz \
#	&& tar -xf wkhtmltox.tar.xz \
#	&& rm wkhtmltox.tar.xz \
#	&& ln -s /usr/local/bin/wkhtmltox/bin/wkhtmltopdf /usr/local/bin/wkhtmltopdf

# -----------------------------------------------------------------------------
# Add default service users
# -----------------------------------------------------------------------------
RUN if ! grep -q ":${gid}:" /etc/group;then groupadd -g ${gid} app;fi
RUN useradd -u ${uid} -d /var/www/vhosts -m -g ${gid} app \
	&& usermod -a -G ${gid} apache


# -----------------------------------------------------------------------------
# Add mcrypt library
# -----------------------------------------------------------------------------
RUN yum -y install make
RUN yum -y install libmcrypt-devel
RUN pecl install mcrypt-1.0.1

# ------------------------------------------------------------------------------
RUN pecl install xdebug

RUN echo 'zend_extension="/usr/lib64/php/modules/xdebug.so"' >> /etc/php.ini
RUN echo 'xdebug.remote_enable=1' >> /etc/php.ini
RUN echo 'xdebug.remote_autostart=1' >> /etc/php.ini
RUN echo 'xdebug.remote_log=/var/log/xdebug.log ; Logs xdebug outputs to a file. This is very useful for troubleshooting.' >> /etc/php.ini
RUN echo 'xdebug.remote_host=172.28.5.2; IP address of your development computer.' >> /etc/php.ini
RUN echo 'xdebug.remote_port=9000; Port that development computer listens on.' >> /etc/php.ini
RUN echo 'xdebug.remote_connect_back=1' >> /etc/php.ini

# -----------------------------------------------------------------------------
# Add a symbolic link to the app users home within the home directory &
# Create the initial directory structure
# -----------------------------------------------------------------------------
RUN ln -s /var/www/vhosts /home/app \
	&& mkdir -p /var/www/vhosts && mkdir -p /var/session

# -----------------------------------------------------------------------------
# Virtual hosts configuration
# -----------------------------------------------------------------------------
ADD vhosts.conf /etc/httpd/conf.d

# -----------------------------------------------------------------------------
# Set permissions
# -----------------------------------------------------------------------------
RUN chown -R app:${gid} /var/www/vhosts \
	&& chmod 770 /var/www/vhosts \
	&& chmod -R 777 /var/session

# -----------------------------------------------------------------------------
# Remove packages
# -----------------------------------------------------------------------------
RUN rm -rf /var/cache/yum/* \
	&& yum clean all

# -----------------------------------------------------------------------------
# Set default environment variables used to identify the service container
# -----------------------------------------------------------------------------
ENV SERVICE_UNIT_APP_GROUP app-1
ENV	SERVICE_UNIT_LOCAL_ID 1
ENV	SERVICE_UNIT_INSTANCE 1

# -----------------------------------------------------------------------------
# Set default environment variables used to configure the service container
# -----------------------------------------------------------------------------
ENV APACHE_SERVER_ALIAS ""
ENV	APACHE_SERVER_NAME app-1.local
ENV	APP_HOME_DIR /var/www/app
ENV	DATE_TIMEZONE UTC
ENV	HTTPD /usr/sbin/httpd
ENV	SERVICE_USER app
ENV	SERVICE_USER_GROUP app
ENV	SERVICE_USER_PASSWORD ""
ENV	SUEXECUSERGROUP false
ENV	TERM xterm
ENV DB_MYSQL_PORT_3306_TCP_ADDR ""
ENV DB_MYSQL_PORT_3306_TCP_PORT ""

# -----------------------------------------------------------------------------
# Set locale
# -----------------------------------------------------------------------------
RUN localedef -i en_GB -f UTF-8 en_GB.UTF-8
ENV LANG en_GB.UTF-8

# -----------------------------------------------------------------------------
# Set ports
# -----------------------------------------------------------------------------
EXPOSE 80 443 8071 8072 8073 9001

# -----------------------------------------------------------------------------
# Copy files into place
# -----------------------------------------------------------------------------
#ADD index.php /var/www/app/public_html/index.php
RUN echo 'Listen 8071' >> /etc/httpd/conf/httpd.conf
RUN echo 'Listen 8072' >> /etc/httpd/conf/httpd.conf
RUN echo 'Listen 8073' >> /etc/httpd/conf/httpd.conf
RUN echo 'extension=mcrypt.so;' >> /etc/php.ini
RUN yum -y install composer
RUN > /etc/httpd/conf.modules.d/00-mpm.conf
RUN echo 'LoadModule mpm_prefork_module modules/mod_mpm_prefork.so' >> /etc/httpd/conf.modules.d/00-mpm.conf

CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
