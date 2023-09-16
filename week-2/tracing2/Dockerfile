FROM golang:1.19 as builder
WORKDIR /app
COPY . .
RUN go build -o test

FROM ubuntu:latest
WORKDIR /app
COPY --from=builder /app/test ./test
CMD ["app/test"]
