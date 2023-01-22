FROM golang:1.19-alpine3.17 as base

ARG APP_PATH_HOST

WORKDIR /go/src/app

ENV GO111MODULE="on"
ENV GOOS="linux"
ENV CGO_ENABLED=0

RUN apk add --no-cache git make ca-certificates curl

FROM base as livereload

RUN curl -fLo install.sh https://raw.githubusercontent.com/cosmtrek/air/master/install.sh \
    && chmod +x install.sh && sh install.sh && cp ./bin/air /bin/air

EXPOSE 8080

ENTRYPOINT ["air"]

FROM base as debug

ADD ${APP_PATH_HOST} /go/src/app

RUN go install github.com/go-delve/delve/cmd/dlv@latest

RUN go build -o /main.go

EXPOSE 8080 2345

CMD ["dlv", "--listen=:2345", "--headless=true", "--api-version=2", "--accept-multiclient", "exec", "/payment-gateway", "serve"]