FROM youske/alpine-ruby:latest
MAINTAINER youske miyakoshi <youske@gmail.com>

ENV FLUENTD_VERSION=0.12.21 \
    PACKAGE='wget' \
    BUILDPACKAGE='build-base linux-headers musl-dev ruby-dev' \
    PORT=24224 \
    WORK_DIR=/fluentd

RUN apk update --no-cache
RUN apk add ${PACKAGE} --no-cache && apk add ${BUILDPACKAGE} --no-cache -t buildtool && \
    echo 'gem: --no-document' >> /etc/gemrc

RUN addgroup fluent && adduser -G fluent -S -h /home/fluent -s /bin/bash fluent

RUN mkdir -p ${WORK_DIR}/log ${WORK_DIR}/etc ${WORK_DIR}/plugins
COPY fluent.conf.tmpl ${WORK_DIR}/etc/

#USER fluent

#ENV PATH=/home/fluent/.gem/ruby/2.2.0/bin:$PATH \
#    GEM_PATH=/home/fluent/.gem/ruby/2.2.0:$GEM_PATH

ENV PATH=/root/.gem/ruby/2.2.0/bin:$PATH \
    GEM_PATH=/root/.gem/ruby/2.2.0:$GEM_PATH


RUN echo "gem: --user-install --no-document" >> ~/.gemrc
RUN gem install fluentd -v ${FLUENTD_VERSION}

# USER root
RUN apk del buildtool --no-cache && rm -rf /tmp/* /var/cache/apk/*
ONBUILD COPY fluent.conf.tmpl ${WORK_DIR}/etc/
ONBUILD COPY plugins ${WORK_DIR}/plugins/

#RUN chown -R fluent:fluent ${WORK_DIR}

#USER fluent
EXPOSE ${PORT}
#VOLUME /fluentdlog
WORKDIR ${WORK_DIR}
ENTRYPOINT ["render","/fluentd/etc/fluent.conf", "--", "switch", "shell=/bin/bash", "--", "fluentd", "-c", "/fluentd/etc/fluent.conf", "-p", "/fluentd/plugins"]
