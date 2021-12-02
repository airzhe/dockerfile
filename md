FROM node:latest as builder
ENV APP_VERSION=1.5.6 \
    NODE_OPTIONS=--openssl-legacy-provider
RUN cd /tmp \
    && wget https://github.com/doocs/md/archive/refs/tags/v${APP_VERSION}.tar.gz -O md.tar.gz \
    && mkdir -p md \
    && tar -xf md.tar.gz -C md --strip-components=1 \
    && cd md \
    && npm config set registry https://registry.npm.taobao.org && npm install \
    && npm run build:h5-netlify 

FROM nginx:latest
COPY --from=builder /tmp/md/dist/index.html /usr/share/nginx/html
COPY --from=builder /tmp/md/dist/static /usr/share/nginx/html/static
