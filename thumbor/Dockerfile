FROM python:2

LABEL maintainer="Edu Herraiz <ghark@gmail.com>"

VOLUME /data

# Things required for a python/pip environment
COPY system-requirements.txt /usr/src/app/system-requirements.txt
RUN  \
    awk '$1 ~ "^deb" { $3 = $3 "-backports"; print; exit }' /etc/apt/sources.list > /etc/apt/sources.list.d/backports.list && \
    apt-get update && \
    apt-get -y upgrade && \
    apt-get -y autoremove && \
    xargs apt-get -y -q install < /usr/src/app/system-requirements.txt && \
    apt-get clean

ENV HOME /usr/src/app
ENV SHELL bash
ENV WORKON_HOME /usr/src/app
WORKDIR /usr/src/app

ARG DOCKERHOST=172.17.0.1
ENV DOCKERHOST ${DOCKERHOST}
COPY requirements.txt /usr/src/app/requirements.txt
RUN pip install --trusted-host None --no-cache-dir \
   --extra-index-url http://${DOCKERHOST}:9009/simple/ \
   --trusted-host ${DOCKERHOST} \
   -r /usr/src/app/requirements.txt

# It's a temp fix. Without it redis>=3.0.0 is installed which doesn't have correct definitions which are used by redis-py-cluster.
RUN pip uninstall -y redis
RUN pip install redis==2.10.6

COPY conf/thumbor.conf.tpl /usr/src/app/thumbor.conf.tpl

RUN \ 
    ln /usr/lib/python2.7/dist-packages/cv2.x86_64-linux-gnu.so /usr/local/lib/python2.7/cv2.so && \
    ln /usr/lib/python2.7/dist-packages/cv.py /usr/local/lib/python2.7/cv.py

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["thumbor"]

EXPOSE 8000
EXPOSE 8001
