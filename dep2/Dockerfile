FROM golang:1.23 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o main cmd/app/main.go

FROM scratch
WORKDIR /app
COPY --from=builder /app/main .
COPY --from=builder /app/migrations ./migrations
CMD ["./main"]