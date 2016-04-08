FROM youske/alpine-std:latest
MAINTAINER youske miyakoshi <youske@gmail.com>

ENV FLUENTD_VERSION=0.12.21 \
    PACKAGE='bash ca-certificates ruby ruby-irb' \
    BUILDPACKAGE='build-base linux-headers musl-dev ruby-dev' \
    PORT=24224 \
    WORK_DIR=/fluentd

RUN apk add ${PACKAGE} --no-cache && apk add ${BUILDPACKAGE} --no-cache -t buildtool && \
    echo 'gem: --no-document' >> /etc/gemrc
RUN gem install fluentd -v ${FLUENTD_VERSION} && \
    echo "gem: --user-install --no-document" >> ~/.gemrc

RUN apk del buildtool --no-cache && rm -rf /tmp/* /var/cache/apk/*

RUN mkdir -p ${WORK_DIR}/log ${WORK_DIR}/etc ${WORK_DIR}/plugins
COPY fluent.conf.tmpl ${WORK_DIR}/etc/

RUN addgroup fluent && adduser -G fluent -S -h /home/fluent -s /bin/bash fluent
RUN chown -R fluent:fluent ${WORK_DIR}

USER fluent
ENV PATH=/home/fluent/.gem/ruby/2.2.0/bin:$PATH \
    GEM_PATH=/home/fluent.gem/ruby/2.2.0:$GEM_PATH

ONBUILD COPY fluent.conf.tmpl ${WORK_DIR}/etc/
ONBUILD COPY plugins ${WORK_DIR}/plugins/

EXPOSE ${PORT}

USER fluent
WORKDIR ${WORK_DIR}
ENTRYPOINT ["render","/fluentd/etc/fluent.conf", "--","switch","shell=/bin/bash","--", "exec fluentd -c /fluentd/etc -p /fluentd/plugins/ OPT"]
#CMD ["/bin/bash"]
