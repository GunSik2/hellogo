# Env
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
- 프로젝트 구성
```
mkdir -p $GOPATH/src/github.com/GunSik2/hellogo/firstapp
vi src/github.com/GunSik2/hellogo/firstapp/hello.go
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
go install github.com/GunSik2/hellogo/firstapp
```
- 실행
```
$GOPATH/bin/firstapp
```

## GO 라이브러리
- 프로젝트 구성
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
vi src/github.com/GunSik2/hellogo/firstapp/hello.go
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
go install github.com/GunSik2/hellogo/firstapp
```
- 실행
```
$GOPATH/bin/firstapp
```
- 디렉토리 구조
```
bin
├── firstapp
src
└── github.com
    └── GunSik2
        └── hellogo
            ├── firstapp
            │   └── hello.go
            └── stringutil
                └── reverse.go
```


## Reference
- https://www.youtube.com/watch?v=YS4e4q9oBaU
- https://golang.org/doc/gopath_code
