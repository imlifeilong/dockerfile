FROM centos
MAINTAINER imlifeilong@github.com

ENV PYTHON_VERSION 3.7.1
ENV DJANGO_VERSION 2.1.3
ENV PIP "pip install -i https://pypi.douban.com/simple"

# Update the source list
RUN yum -y update \
	&& yum install -y wget gcc make \
	&& yum install zlib-devel openssl-devel libffi-devel -y

# Update the Python
RUN cd /usr/local/src \
	&& wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tar.xz \
	&& tar Jxvf Python-${PYTHON_VERSION}.tar.xz

# Install the sqlite
RUN cd /usr/local/src \
        && wget https://www.sqlite.org/2018/sqlite-autoconf-3250300.tar.gz \
	&& tar zxvf sqlite-autoconf-3250300.tar.gz \
	&& cd sqlite-autoconf-3250300 \
	&& ./configure \
	&& make \
	&& make install

# Complie Python
RUN cd /usr/local/src/Python-${PYTHON_VERSION} \
	&& ./configure --prefix=/usr/local/python${PYTHON_VERSION} \
	&& make \
	&& make install

# Link Python and pip
RUN rm -rf /usr/bin/python \
	&& ln -s /usr/local/python${PYTHON_VERSION}/bin/python3 /usr/bin/python3 \
	&& ln -s /usr/bin/python3 /usr/bin/python \
	&& ln -s /usr/local/python${PYTHON_VERSION}/bin/pip3 /usr/bin/pip

# Fix the yum and all
RUN sed -i "s#/usr/bin/python#/usr/bin/python2#g" `grep /usr/bin/python -rl /usr/bin/*`

# Update pip
#RUN pip install pip --upgrade
RUN ${PIP} pip --upgrade

# Install the Django
#RUN pip install django==${DJANGO_VERSION} \
RUN ${PIP} django==${DJANGO_VERSION} \
	&& ln -s /usr/local/python${PYTHON_VERSION}/bin/django-admin /usr/bin/django-admin

EXPOSE 8000

RUN cd /mnt \
	&& django-admin startproject mysite \
	&& sed -i "s#ALLOWED_HOSTS = \[\]#ALLOWED_HOSTS = \['*'\]#g" /mnt/mysite/mysite/settings.py

CMD ["python", "/mnt/mysite/manage.py", "runserver", "0.0.0.0:8000"]
