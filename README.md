# Setup 

이 섹션에서는 Rust 프로그램을 WebAssembly로 컴파일하고 이를 JavaScript에 통합하기 위한 도구 체인을 설정하는 방법을 설명한다. 


## The Rust Toolchain
rustup, rustc 그리고 cargo를 포함한 표준 Rust 툴체인이 필요하다. 


## wasm-pack
wasm-pack은 Rust로 생성된 WebAssembly를 구축, 테스트 및 게시하기 위한 원스톱 상점이다. [Install wasm-pack](https://rustwasm.github.io/wasm-pack/installer/) 링크를 클릭하여 설치 방법을 확인하여 설치한다. 

cargo 실행하여 wasm-pack을 설치할 수도 있다. 

```shell
cargo install wasm-pack
```

wasm-pack의 git허브 사이트는 [wasm-pack]을 방문한다. 이 튜터리얼과 비스한 내용의 튜터리얼이 [Welcome to the wasm-pack docs!](https://rustwasm.github.io/docs/wasm-pack/introduction.html)에 있다. 



## cargo-generate

[cargo-generate](https://github.com/cargo-generate/cargo-generate)는 기존 git 저장소를 템플릿으로 활용하여 새로운 Rust 프로젝트를 빠르게 시작하고 실행할 수 있도록 도와준다.

다음 명령으로 cargo-generate를 설치한다. 

```shell
cargo install cargo-generate
```


## npm 
npm은 JavaScript용 패키지 관리자이다. 이를 사용하여 JavaScript 번들러 및 개발 서버를 설치하고 실행한다. 자습서가 끝나면 컴파일된 .wasm을 npm 레지스트리에 게시한다. 


