GO111MODULES=on
APP?=helloserver
REGISTRY?=cgshome2
#REGISTRY?=gcr.io/images
COMMIT_SHA=$(shell git rev-parse --short HEAD)
GOPATH_BIN=${GOPATH}/bin


.PHONY: build
## build: build the application
build: clean
	@echo "Building..."
	@go build -o ${GOPATH_BIN}/${APP} main.go

.PHONY: run
## run: runs go run main.go
run:
	go run -race main.go

.PHONY: clean
## clean: cleans the binary
clean:
	@echo "Cleaning"
	@go clean

.PHONY: test
## test: runs go test with default values
test:
	go test -v -count=1 -race ./...

.PHONY: build-tokenizer
## build-tokenizer: build the tokenizer application
build-tokenizer:
	${MAKE} -c tokenizer build

.PHONY: setup
## setup: setup go modules
setup:
	@go mod init \
		&& go mod tidy \
		&& go mod vendor
	
.PHONY: docker-build
## docker-build: builds the stringifier docker image to registry
docker-build: build
	docker build -t ${REGISTRY}/${APP}:${COMMIT_SHA} .

.PHONY: docker-build-multistage
## docker-build-multistage: builds the stringifier docker image to registry
docker-build-multistage: build
	docker build -t ${REGISTRY}/${APP}:${COMMIT_SHA} -f Dockerfile.multistage .

.PHONY: docker-push
## docker-push: pushes the stringifier docker image to registry
docker-push: docker-build
	docker push ${REGISTRY}/${APP}:${COMMIT_SHA}

.PHONY: help
## help: Prints this help message
help:
	@echo "Usage: \n"
	@sed -n 's/^##//p' ${MAKEFILE_LIST} | column -t -s ':' |  sed -e 's/^/ /'


## https://danishpraka.sh/2019/12/07/using-makefiles-for-go.html	
