FROM golang:1.19 as builder
WORKDIR /opt/app
COPY . .  
RUN go build -o example

FROM ubuntu:latest
WORKDIR /opt/app
COPY --from=builder /opt/app/example ./example
CMD ["/opt/app/example"]

