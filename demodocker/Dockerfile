FROM hidephong/centos7

MAINTAINER techmaster.vn

RUN yum -y update

RUN rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm --force --nodeps

RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm --force --nodeps

RUN rpm --import http://yum.postgresql.org/RPM-GPG-KEY-PGDG-94

RUN rpm -Uivh http://yum.postgresql.org/9.4/redhat/rhel-7-x86_64/pgdg-centos94-9.4-1.noarch.rpm --force --nodeps

# Install base stuff.
RUN yum -y install  nginx \
					unzip \
					git   \
					nano  \					
					redis \
					wget  \
					curl  \
					gcc   \
					gcc-c++				

RUN echo "daemon off;" >> /etc/nginx/nginx.conf

#Setup Nginx"

ADD ./src/default.conf /etc/nginx/conf.d/default.conf

ADD ./src/nginx.conf /etc/nginx/nginx.conf

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

RUN mkdir /root/.ssh/

ADD ./src/repo-key /

RUN chmod 600 /repo-key   

RUN touch /root/.ssh/known_hosts

RUN ssh-keyscan github.com >> /root/.ssh/known_hosts

RUN echo "Host github.com\n\tStrictHostKeyChecking no\n" >> /root/.ssh/config

RUN git clone https://github.com/hidephong/thanh-framework.git /opt/

RUN yum -y install postgresql94 postgresql94-server postgresql94-contrib

RUN su - postgres -c /usr/pgsql-9.4/bin/initdb

COPY ./src/start.sh /usr/bin/

USER root

RUN chmod -v +x /usr/bin/start.sh

RUN chmod u+s,g-w,o-w /bin/su

USER postgres

RUN echo "host all  all    0.0.0.0/0  trust" >> /var/lib/pgsql/9.4/data/pg_hba.conf

RUN echo "listen_addresses='*'" >> /var/lib/pgsql/9.4/data/postgresql.conf

USER root

RUN yum clean all

ADD ./src/redis.conf /etc/redis.conf

#Install NodeJS

#RUN yum install -y nodejs

RUN mkdir /opt/iojs

RUN mkdir /opt/iojs/src

WORKDIR /opt/iojs/src

RUN wget https://iojs.org/dist/v1.5.1/iojs-v1.5.1.tar.gz

RUN tar -zxf iojs-v1.5.1.tar.gz

WORKDIR /opt/iojs/src/iojs-v1.5.1

RUN  ./configure
RUN make
RUN make install

# Cleanup
WORKDIR /
RUN rm -rf /opt/iojs/src/iojs-v1.5.1

RUN yum -y install npm

USER root

WORKDIR /opt

RUN unzip -o public.zip

RUN npm install pm2 -g

RUN npm install moment inflection node-uuid validator toposort-class generic-pool dottie moment-timezone passport-twitter passport-local passport-linkedin passport-google-oauth passport-github passport-facebook nodemailer bluebird formidable async nunjucks connect-redis passport helmet cookie-parser method-override compression express-session body-parser glob chalk lodash redis sequelize pg pg-hstore slug express morgan image-size imagemagick

# Mountable directories
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql","/tmp/","/var/lib/pgsql","/usr/pgsql-9.4/"]

VOLUME ["/var/lib/redis"]

# Set the environment variables
#ENV HOME /var/lib/redis

# Expose ports.
EXPOSE 6379 5432 80 3000

#RUN /usr/bin/start.sh

CMD ["/usr/bin/start.sh"]

RUN echo "Success build"

