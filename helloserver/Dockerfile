FROM golang:1.16-alpine

WORKDIR /app

COPY go.mod go.sum* .
RUN go mod download

COPY *.go ./

RUN go build -o /helloserver

EXPOSE 8080

CMD [ "/helloserver" ]
