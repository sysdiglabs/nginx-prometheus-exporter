FROM golang:1.18 as base
ARG VERSION
ARG GIT_COMMIT
ARG DATE
ARG TARGETARCH

WORKDIR /go/src/github.com/nginxinc/nginx-prometheus-exporter

FROM base as builder
COPY . . 
RUN go mod tidy
RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=$TARGETARCH go build -trimpath -a -ldflags "-s -w " -o nginx-prometheus-exporter .
RUN cp nginx-prometheus-exporter /usr/bin/nginx-prometheus-exporter

FROM scratch as scratch
COPY --from=base /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /usr/bin/nginx-prometheus-exporter /usr/bin/nginx-prometheus-exporter
USER 1001:1001
ENTRYPOINT [ "/usr/bin/nginx-prometheus-exporter" ]

FROM quay.io/sysdig/sysdig-mini-ubi:1.2.12 as ubi
COPY --from=base /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /usr/bin/nginx-prometheus-exporter /usr/bin/nginx-prometheus-exporter
USER 1001:1001
ENTRYPOINT [ "/usr/bin/nginx-prometheus-exporter" ]

