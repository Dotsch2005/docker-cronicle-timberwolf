FROM node:18-bullseye AS Builder
ENV CRONICLE_VERSION=0.9.23
RUN apt-get update -y && apt-get upgrade -y && apt install -y curl
WORKDIR /opt/cronicle
RUN curl -L  https://github.com/jhuckaby/Cronicle/archive/refs/tags/v${CRONICLE_VERSION}.tar.gz -o /tmp/Cronicle-${CRONICLE_VERSION}.tar.gz && \
    tar zxvf /tmp/Cronicle-${CRONICLE_VERSION}.tar.gz -C /tmp/ && \
    mv /tmp/Cronicle-${CRONICLE_VERSION}/* . && \
    rm -rf /tmp/* && \
    npm install && \
	npm install mqtt -g && \
    curl -L  https://raw.githubusercontent.com/Dotsch2005/docker-cronicle-timberwolf/main/docker/patches/engine.patch -o /tmp/engine.patch && \
	curl -L  https://raw.githubusercontent.com/Dotsch2005/docker-cronicle-timberwolf/main/docker/docker-entrypoint.js -o /opt/cronicle/bin/docker-entrypoint.js && \
    patch -p3 < /tmp/engine.patch lib/engine.js


FROM node:18-alpine
RUN apk add procps curl
COPY --from=builder /opt/cronicle/ /opt/cronicle/
WORKDIR /opt/cronicle
ENV CRONICLE_foreground=1
ENV CRONICLE_echo=1
ENV CRONICLE_color=1
ENV debug_level=1
ENV HOSTNAME=main
RUN node bin/build.js dist && bin/control.sh setup
CMD ["node", "bin/docker-entrypoint.js"]
