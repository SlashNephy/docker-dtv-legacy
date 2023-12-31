FROM node:20-alpine@sha256:002b6ee25b63b81dc4e47c9378ffe20915c3fa0e98e834c46584438468b1d0b5

ENV DOCKER="YES"
ARG REPOSITORY="git://github.com/Chinachu/Chinachu.git"
ARG BRANCH="master"

ARG WORK_DIR="/usr/local/chinachu"

#node container default value
ARG USER_NAME="node"

RUN set -x \
	&& apk upgrade --update \
	&& apk add \
		bash \
		coreutils \
		curl \
		procps \
		ca-certificates \
	\
	&& apk add --virtual .build-deps \
		git \
		make \
		gcc \
		g++ \
		autoconf \
		automake \
		wget \
		curl \
		sudo \
		tar \
		xz \
		libc-dev \
		musl-dev \
		eudev-dev \
		libevent-dev \
		perl-utils \
	\
	&& mkdir -p ${WORK_DIR} \
	&& git clone ${REPOSITORY} ${WORK_DIR} \
	&& chown -R ${USER_NAME} ${WORK_DIR} \
	&& cd ${WORK_DIR} \
	&& git checkout ${BRANCH} \
	&& echo 2 | sudo -u ${USER_NAME} ./chinachu installer \
	&& echo 4 | sudo -u ${USER_NAME} ./chinachu installer \
	&& echo 5 | sudo -u ${USER_NAME} ./chinachu installer \
	&& sudo -u ${USER_NAME} ./chinachu service operator initscript | tee /tmp/chinachu-operator \
	&& sudo -u ${USER_NAME} ./chinachu service wui initscript | tee /tmp/chinachu-wui \
	&& sudo -u ${USER_NAME} mkdir log \
	\
	&& chmod u+x /tmp/chinachu-operator /tmp/chinachu-wui \
	&& mv /tmp/chinachu-operator /etc/init.d/ \
	&& mv /tmp/chinachu-wui /etc/init.d/ \
	\
	# cleaning
	&& cd / \
	&& npm cache verify \
	&& apk del --purge .build-deps \
	&& rm -rf /var/cache/apk/*

COPY services.sh /usr/local/bin
COPY config.sample.json ${WORK_DIR}

WORKDIR ${WORK_DIR}
CMD ["/usr/local/bin/services.sh"]
EXPOSE 10772 20772 5353
