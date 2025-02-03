FROM golangci/golangci-lint:v1.56-alpine as linter
WORKDIR /build/
COPY . .
RUN golangci-lint run --timeout=360s

FROM golang:1.22-alpine as builder

WORKDIR /build/
COPY --from=linter /build/go.mod /build/go.sum ./
RUN go mod download && go mod verify

COPY --from=linter /build/ .
RUN go build -v -o events-exporter ./cmd/events-exporter/

FROM alpine:3.19

ARG USER=exporter
ARG UID=10005

# See https://stackoverflow.com/a/55757473/12429735RUN
RUN adduser \
    --disabled-password \
    --gecos "" \
    --shell "/sbin/nologin" \
    --uid "${UID}" \
    "${USER}"

COPY --from=builder /build/events-exporter /app/

# Belt and suspenders
RUN chown -R ${USER}:${USER} /app

WORKDIR /app

USER ${USER}

ENTRYPOINT ["/app/events-exporter"]
