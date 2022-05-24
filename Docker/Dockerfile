FROM golang:1.17-alpine AS builder

ENV SOPS_VERSION 3.7.2

RUN apk add --update --no-cache make

ADD https://github.com/mozilla/sops/archive/v${SOPS_VERSION}.tar.gz /go/src/app/
RUN tar xzf /go/src/app/v${SOPS_VERSION}.tar.gz -C /go/src/app/
WORKDIR /go/src/app/sops-${SOPS_VERSION}
RUN make install

FROM dtzar/helm-kubectl:latest

RUN apk add --no-cache \
        python3 \
        py3-pip \
    && apk add terraform --repository=http://dl-cdn.alpinelinux.org/alpine/edge/community \
    && pip3 install --upgrade pip \
    && pip3 install --no-cache-dir \
        awscli gitpython \
    && rm -rf /var/cache/apk/* \
    && curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp \
    && mv /tmp/eksctl /usr/local/bin

RUN helm plugin install https://github.com/jkroepke/helm-secrets --version v3.12.0
COPY --from=builder /go/bin/sops /usr/local/bin/sops
