FROM golang:1.22.2 AS builder
WORKDIR /sample-app
COPY . .
RUN go mod init go.opentelemetry.io/auto/internal/test/e2e/kafka-go && go mod tidy && CGO_ENABLED=0 go build -o main

FROM bitnami/kafka:latest

USER root
ENV KAFKA_CFG_PROCESS_ROLES=controller,broker
ENV KAFKA_CFG_NODE_ID=0
ENV KAFKA_CFG_LISTENERS=PLAINTEXT://127.0.0.1:9092,CONTROLLER://:9093
ENV KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
ENV KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@127.0.0.1:9093
ENV KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
WORKDIR /sample-app
COPY --from=builder /sample-app/main .
COPY start.sh .
RUN chmod +x start.sh
WORKDIR /
ENTRYPOINT ["/sample-app/start.sh"]