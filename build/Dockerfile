FROM golang:1.15-alpine as builder

ARG UPSTREAM_VERSION

RUN apk add --no-cache make gcc musl-dev linux-headers git bash
ADD https://golang.org/dl/go1.15.5.src.tar.gz /tmp/go.tar.gz
RUN (cd /tmp && tar -xf go.tar.gz)
RUN (cd /tmp/go/src && ./make.bash)
ENV PATH="/tmp/go/bin:${PATH}"

RUN git clone -b logContractCode https://github.com/sourcifyeth/go-ethereum.git && \
    cd go-ethereum && make geth

# Pull Geth into a second stage deploy alpine container
FROM alpine:edge

RUN apk add --no-cache ca-certificates
COPY --from=builder /go/go-ethereum/build/bin/geth /usr/local/bin

ENTRYPOINT geth --rinkeby --http --http.addr 0.0.0.0 --http.corsdomain "*" --http.vhosts "*" --ws --ws.origins "*" --ws.addr 0.0.0.0 --syncmode full $EXTRA_OPTS
