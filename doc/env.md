# Go 프로젝트 구성

## GO111MODULE 설정
- off - 빌드 중에 $GOPATH에 있는 패키지를 사용합니다.
- on - 빌드 중에 $GOPATH 대신 모듈에 있는 패키지를 사용합니다.
- auto - 현재 디렉터리가 $GOPATH 외부에 있고 go.mod 파일이 포함된 경우 모듈을 사용하고 그렇지 않으면 $GOPATH의 패키지를 사용합니다.


Go111MODULE 설정 변경
```
go env -w GO111MODULE=auto  
```

## GO 환경 구성
- GOPATH 환경 구성
```
mkdir godev; cd godev
mkdir bin pkg src

export GOPATH=$(pwd)
export PATH=$PATH:$GOPATH/bin
```

## GO 실행 프로그램
- 소스
```
mkdir -p $GOPATH/src/github.com/GunSik2/hellogo/hellocli
vi src/github.com/GunSik2/hellogo/hellocli/main.go
---
package main

import "fmt"

func main() {
	fmt.Println("Hello, world.")
}
---
```
- 빌드 & 인스톨
```
go install github.com/GunSik2/hellogo/hellocli
```
- 실행
```
$GOPATH/bin/hellocli
```

## GO 라이브러리
- 소스
```
mkdir -p $GOPATH/src/github.com/GunSik2/hellogo/stringutil
vi src/github.com/GunSik2/stringutil/reverse.go
---
// Package stringutil contains utility functions for working with strings.
package stringutil

// Reverse returns its argument string reversed rune-wise left to right.
func Reverse(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
---
```
- 빌드
```
go build github.com/GunSik2/hellogo/stringutil
```
- 실행 프로그램 수정 (라이브러리 호출)
```
vi src/github.com/GunSik2/hellogo/hellocli/main.go
---
package main

import (
	"fmt"
	"github.com/GunSik2/hellogo/stringutil"
)

func main() {
	fmt.Println(stringutil.Reverse("!oG ,olleH"))
}
---
```
- 빌드 & 인스톨
```
go install github.com/GunSik2/hellogo/hellocli
```
- 실행
```
$GOPATH/bin/hellocli
```
- 디렉토리 구조
```
bin
├── hellocli
src
└── github.com
    └── GunSik2
        └── hellogo
            ├── hellocli
            │   └── main.go
            └── stringutil
                └── reverse.go
```

## 테스트 프로그램
- 소스 : "_test.go" 로 끝나는 파일을 생성
```
vi src/github.com/GunSik2/hellogo/stringutil/reverse_test.go
--
package stringutil

import "testing"

func TestReverse(t *testing.T) {
	cases := []struct {
		in, want string
	}{
		{"Hello, world", "dlrow ,olleH"},
		{"Hello, 世界", "界世 ,olleH"},
		{"", ""},
	}
	for _, c := range cases {
		got := Reverse(c.in)
		if got != c.want {
			t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
		}
	}
}
---
```
- 테스트 실행
```
go test github.com/GunSik2/hellogo/stringutil
(cd github.com/GunSik2/hellogo/stringutil; go test)
```
## 외부 패키지 사용
- 외부 Git 패키지 다운로드 후 자동으로 빌드 & 인스톨 
  - 패키지 상세: https://pkg.go.dev/github.com/golang/example
```
go get github.com/golang/example/hello
$GOPATH/bin/hello
```

## 패키지 찾기
- https://pkg.go.dev/

## Makefile 사용
- 소스 : [hellogo/helloserver/Makefile](../helloserver/Makefile)
- 실행 
```
$ make help
Usage: 

  build                     build the application
  run                       runs go run main.go
  clean                     cleans the binary
  test                      runs go test with default values
  build-tokenizer           build the tokenizer application
  setup                     setup go modules
  docker-build              builds the application docker image to registry
  docker-build-multistage   builds the application docker image to registry
  docker-push               pushes the application docker image to registry
  help                      Prints this help message
```
- 사용 예시
```
$ make setup
$ make build
$ make run
$ make test
$ make docker-build
$ make docker-push
```

## Docker 이미지 생성
- 소스: [hellogo/helloserver/Dockerfile](../helloserver/Dockerfile)
```
FROM golang:1.16-alpine

WORKDIR /app

COPY go.mod go.sum* .
RUN go mod download

COPY *.go ./

RUN go build -o /helloserver

EXPOSE 8080

CMD [ "/helloserver" ]
```

## Kubernetes 배포
- 소스: [hellogo/helloserver/deployment.yml](../helloserver/deployment.yml)
```
kind: Service
apiVersion: v1
metadata:
  name: helloserver
spec:
  selector:
    app: helloserver
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloserver
  labels:
    app: helloserver
spec:
  replicas: 1
  selector:
    matchLabels:
      app: helloserver
  template:
    metadata:
      labels:
        app: helloserver
    spec:
      containers:
      - name: helloserver
        image: GunSik2/helloserver:latest
        ports:
        - containerPort: 80
```

## Reference
- https://www.youtube.com/watch?v=YS4e4q9oBaU
- https://golang.org/doc/gopath_code
- [Rancher Go Pipleline Example](https://github.com/rancher/pipeline-example-go)
- [Docker's Go Language Guide](https://docs.docker.com/language/golang/build-images/)
- [Makefile Docker Guilde](https://danishpraka.sh/2019/12/07/using-makefiles-for-go.html)
