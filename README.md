# wasm-pack을 이용한 Hello, World!

프로젝트 템플릿은 정상적인 기본값으로 미리 구성되어 있으므로 웹용 코드를 빠르게 빌드, 통합 및 패키징할 수 있다. 

다음 명령을 사용하여 프로젝트 템플릿을 복제한다. 
```shell
cargo generate --git https://github.com/rustwasm/wasm-pack-template
```


그러면 새 프로젝트의 이름을 묻는 메시지가 나타난다. 우리는 'wasm-game-of-life'를 사용할 것이다. 

```shell
wasm-game-of-life
```

## what's inside? 
새로운 wasm-game-of-life 프로젝트에 들어간다. 
```shell
cd wasm-game-of-life
```
이 프로젝의 컨텐츠를 살펴보자. 
```shell
wasm-game-of-life/
├── Cargo.toml
├── LICENSE_APACHE
├── LICENSE_MIT
├── README.md
└── src
    ├── lib.rs
    └── utils.rs
```
이 파일들을 자세히 살펴보겠다. 

### wasm-game-of-life/Cargo.toml
Cargo.toml 파일은 Rust의 패키지 관리자 및 빌드 도구인 cargo에 대한 종속성(dependencies) 및 메타데이터를 지정한다. 이것은 wasm-bindgen 종속성, 나중에 살펴볼 몇 가지 선택적 종속성 및 .wasm 라이브러리 생성을 위해 적절하게 초기화된 크레이트 유형으로 미리 구성된 상태로 제공된다. 

## wasm-game-of-life/src/lib.rs
src/lib.rs 파일은 WebAssembly로 컴파일하는 Rust 크레이트의 루트이다. JavaScript와 인터페이스하기 위해 wasm-bindgen을 사용한다.. window.alert JavaScript 함수를 가져오고 인사말 메시지를 알리는 welcome Rust 함수를 내보낸다. 



```rust
mod utils;

use wasm_bindgen::prelude::*;

/ When the `wee_alloc` feature is enabled, use `wee_alloc` as the global
// allocator.
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
extern {
    fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet() {
    alert("Hello, wasm-game-of-life!");
}
```

### wasm-game-of-life/src/utils.rs
src/utils.rs 모듈은 WebAssembly로 컴파일된 Rust로 더 쉽게 작업할 수 있도록 공통 유틸리티를 제공한다. 이 유틸리티 중 일부는 우리가 우리의 wasm 코드를 디버깅할 때와 같이 자습서의 뒷부분에서 더 자세히 살펴보겠지만 지금은 이 파일을 무시할 수 있다. 

## Build the Project
wasm-pack을 사용하여 다음 빌드 단계를 조율한다.
* Rust 1.30 이상이 있고 wasm32-unknown-unknown 대상이 rustup을 통해 설치되어 있는지 확인한다.
* Cargo를 통해 Rust 소스를 WebAssembly .wasm 바이너리로 컴파일한다. 
* Rust로 생성된 WebAssembly를 사용하기 위한 JavaScript API를 생성하려면 wasm-bindgen을 사용하라. 



이 모든 작업을 수행하려면 프로젝트 디렉터리 내에서 다음 명령을 실행한다. 

```shell
wasm-pack build
```

빌드가 완료되면 pkg 디렉토리에서 아티팩트를 찾을 수 있으며 다음 내용이 있어야 한다. 

```shell
pkg/
├── package.json
├── README.md
├── wasm_game_of_life_bg.wasm
├── wasm_game_of_life.d.ts
└── wasm_game_of_life.js
```
README.md 파일은 기본 프로젝트에서 복사되지만 나머지는 완전히 새로운 파일이다. 


## wasm-game-of-life/pkg/wasm_game_of_life_bg.wasm
.wasm 파일은 Rust 소스에서 Rust 컴파일러에 의해 생성되는 WebAssembly 바이너리이다. 여기에는 모든 Rust 기능 및 데이터의 컴파일된 버전인 wasm이 포함되어 있다. 예를 들어, 내보낸 'greet' function이 있다.


### wasm-game-of-life/pkg/wasm_game_of_life.js
.js 파일은 wasm-bindgen에 의해 생성되며 DOM 및 JavaScript 함수를 Rust로 가져오고 멋진 API를 WebAssembly 함수에 JavaScript로 노출하기 위한 JavaScript 글루가 포함되어 있다.예를 들어 WebAssembly 모듈에서 내보낸 greet function을 래핑하는 JavaScript greet function이 있다.

현재 이 접착제는 많은 일을 하지 않지만 wasm과 JavaScript 간에 더 흥미로운 값을 주고받기 시작하면 경계를 넘어 이러한 값을 관리하는 데 도움이 될 것이다.

```jsx
import * as wasm from './wasm_game_of_life_bg';

// ...

export function greet() {
    return wasm.greet();
}
```


### wasm-game-of-life/pkg/wasm_game_of_life.d.ts
.d.ts 파일에는 JavaScript 글루에 대한 TypeScript 유형 선언이 포함되어 있다. TypeScript를 사용하는 경우 WebAssembly 함수 유형에 대한 호출을 확인할 수 있으며 IDE에서 자동 완성 및 제안을 제공할 수 있다! TypeScript를 사용하지 않는 경우 이 파일을 무시해도 된다.

```jsx
export function greet(): void;
```


### wasm-game-of-life/pkg/package.json
package.json 파일에는 생성된 JavaScript 및 WebAssembly 패키지에 대한 메타데이터가 포함되어 있다.이것은 npm 및 JavaScript 번들러에서 패키지, 패키지 이름, 버전 및 기타 여러 항목 간의 종속성을 결정하는 데 사용된다. 도구와 통합하는 데 도움이 되며 패키지를 npm에 게시할 수 있다.


```json
{
  "name": "wasm-game-of-life",
  "collaborators": [
    "Your Name <your.email@example.com>"
  ],
  "description": null,
  "version": "0.1.0",
  "license": null,
  "repository": null,
  "files": [
    "wasm_game_of_life_bg.wasm",
    "wasm_game_of_life.d.ts"
  ],
  "main": "wasm_game_of_life.js",
  "types": "wasm_game_of_life.d.ts"
}
```

## Putting it into a Web Page
wasm-game-of-life 패키지를 웹 페이지에서 사용하기 위해 [create-wasm-app JavaScript 프로젝트 템플릿](https://github.com/rustwasm/create-wasm-app)을 사용한다. 



wasm-game-of-life 디렉토리 내에서 다음 명령을 실행한다.

```shell
npm init wasm-app www
```
새로운 wasm-game-of-life/www 하위 디렉토리에 포함된 내용은 다음과 같다.
```shell
wasm-game-of-life/www/
├── bootstrap.js
├── index.html
├── index.js
├── LICENSE-APACHE
├── LICENSE-MIT
├── package.json
├── README.md
└── webpack.config.js
```
다시 한 번 이러한 파일 중 일부를 자세히 살펴보겠다. 

ㅌ

### wasm-game-of-life/www/package.json
이 package.json은 npm에 게시된 초기 wasm-pack-template 패키지의 버전인 hello-wasm-pack에 대한 종속성뿐만 아니라 webpack 및 webpack-dev-server 종속성으로 사전 구성되어 제공된다.

### wasm-game-of-life/www/webpack.config.js
이 파일은 웹팩과 해당 로컬 개발 서버를 구성한다. 미리 구성된 상태로 제공되며 웹팩과 로컬 개발 서버가 작동하도록 하기 위해 이를 조정할 필요가 전혀 없다. 

### wasm-game-of-life/www/index.html
이것은 웹 페이지의 루트 HTML 파일이다. index.js를 둘러싼 매우 얇은 래퍼인 bootstrap.js를 로드하는 것 외에는 많은 작업을 수행하지 않는다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Hello wasm-pack!</title>
  </head>
  <body>
    <script src="./bootstrap.js"></script>
  </body>
</html>
```


### wasm-game-of-life/www/index.js
index.js는 웹 페이지의 JavaScript에 대한 기본 진입점이다. 기본 wasm-pack-template의 컴파일된 WebAssembly 및 JavaScript 글루가 포함된 hello-wasm-pack npm 패키지를 가져온 다음 hello-wasm-pack의 welcome 함수를 호출한다.


```jsx
import * as wasm from "hello-wasm-pack";
wasm.greet();
```


## Install the dependencies
먼저 wasm-game-of-life/www 하위 디렉토리 내에서 npm install을 실행하여 로컬 개발 서버와 해당 종속성이 설치되었는지 확인한다. 

```shell
npm install
```

이 명령은 한 번만 실행하면 되며 webpack JavaScript 번들러와 해당 개발 서버를 설치한다.

> Rust 및 WebAssembly 작업에 webpack이 필요하지 않다는 점에 유의하세요. 여기에서는 편의를 위해 선택한 번들러 및 개발 서버일 뿐입니다. Parcel 및 Rollup은 WebAssembly를 ECMAScript 모듈로 가져오는 것도 지원해야 합니다. 원하는 경우 번들러 없이 Rust 및 WebAssembly를 사용할 수도 있습니다!



### Using our Local wasm-game-of-life Package in www

npm의 hello-wasm-pack 패키지를 사용하는 대신 로컬 wasm-game-of-life 패키지를 대신 사용하려고 한다. 이를 통해 Game of Life 프로그램을 점진적으로 개발할 수 있다.

wasm-game-of-life/www/package.json을 열고 'devDependencies' 옆에 'wasm-game-of-life': 'file:../pkg' 항목을 포함하여 'dependencies' 필드를 추가한다. :

```json
{
  // ...
  "dependencies": {                     // Add this three lines block!
    "wasm-game-of-life": "file:../pkg"
  },
  "devDependencies": {
    //...
  }
}
```
다음으로, hello-wasm-pack 패키지 대신 wasm-game-of-life를 가져오도록 wasm-game-of-life/www/index.js를 수정한다.
```jsx
import * as wasm from "wasm-game-of-life";

wasm.greet();
```
새 종속성을 선언했으므로 설치해야 한다.
```shell
npm install
```

이제 웹 페이지를 로컬에서 사용할 준비가 되었다.


## Serving Locally
다음으로 개발 서버용 새 터미널을 연다. 새 터미널에서 서버를 실행하면 백그라운드에서 계속 실행될 수 있으며 그동안 다른 명령을 실행하는 것을 차단하지 않는다. 새 터미널에서 wasm-game-of-life/www 디렉터리 내에서 다음 명령을 실행한다.


```shell
npm run start
```

변경 사항을 적용하고 http://localhost:8080/에 반영하고 싶을 때마다 wasm-game-of-life 디렉토리 내에서 wasm-pack 빌드 명령을 다시 실행하기만 하면 된다. 
