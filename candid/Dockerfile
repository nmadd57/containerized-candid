# syntax=docker/dockerfile:1.3.1
FROM ubuntu:24.04 AS build-env
ARG GO_VERSION=1.23.10
ARG ARCH=amd64
ARG CANDID_REF=master
WORKDIR /usr/src/candid
SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates git wget \
    && rm -rf /var/lib/apt/lists/*

RUN wget -q "https://go.dev/dl/go${GO_VERSION}.linux-${ARCH}.tar.gz" \
    && tar -C /usr/local -xzf "go${GO_VERSION}.linux-${ARCH}.tar.gz" \
    && rm "go${GO_VERSION}.linux-${ARCH}.tar.gz"
ENV PATH="${PATH}:/usr/local/go/bin"

RUN git clone --depth 1 --branch "${CANDID_REF}" https://github.com/canonical/candid.git .

RUN go build -o candidsrv -v ./cmd/candidsrv
RUN go build -o candid -v ./cmd/candid
RUN GOBIN=/usr/src/candid go install gopkg.in/macaroon-bakery.v2/cmd/bakery-keygen@latest

# Define a smaller single process image for deployment
FROM ubuntu:24.04 AS deploy-env
RUN apt-get -qq update && apt-get -qq install -y --no-install-recommends ca-certificates curl \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /root/
RUN mkdir www logs
COPY --from=build-env /usr/src/candid/candidsrv .
COPY --from=build-env /usr/src/candid/candid .
COPY --from=build-env /usr/src/candid/bakery-keygen .
COPY --from=build-env /usr/src/candid/static ./www/static/
COPY --from=build-env /usr/src/candid/templates ./www/templates
RUN touch config.yaml

EXPOSE 8081
ENTRYPOINT ["./candidsrv"]
CMD ["config.yaml"]
