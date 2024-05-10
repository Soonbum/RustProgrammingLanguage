# Rust Programming Language

## 러스트 설치

* Linux/macOS 환경
  - 터미널에서 명령어 실행: `$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh`
  - macOS에서 C 컴파일러가 필요할 수도 있다: `$ xcode-select --install`
  - Linux의 경우 GCC, Clang이 보통 설치되어 있으며 `build-essential` 패키지가 필요할 수 있다.
 
* Windows 환경
  - [러스트 설치](https://www.rust-lang.org/tools/install) 안내를 따라서 설치하면 된다.
  - RUSTUP-INIT.EXE 파일을 받아서 설치한다.
  - Visual Studio 2022 또는 Visual Studio Code IDE를 활용한다.
    * Visual Studio 2022 이상의 경우 다음 패키지를 설치한다: C++ 데스크톱 개발, Windows 10 또는 11 SDK, 언어팩
    * Visual Studio Code의 경우 Extensions 설치를 권장한다: rust-analyzer (The Rust Programming Language)
 
* 러스트 업데이트: `$ rustup update`

* 러스트 삭제: `$ rustup self uninstall`

* 러스트 문서 보기: `$ rustup doc` (HTML 문서 보기)

## Hello, World!

* 먼저 projects 디렉토리를 만들고 그 밑에 hello_world 디렉토리를 만든다.

* main.rs 파일을 생성하고 다음 내용을 입력한다.
  - 탭 대신 스페이스 4칸을 사용한다.
  - !가 있으면 매크로 호출 코드, 없으면 함수이다.
  - 표현식 끝에는 ;이 붙는다.

```rust
fn main() {
    println!("Hello, world!");
}
```

* 컴파일한 후 프로그램을 실행해 본다.
  ```
  $ rustc main.rs
  $ ./main
  ```

## 카고 (Cargo)

* 카고: 러스트 빌드 시스템 및 패키지 매니저

* 카고로 프로젝트 생성하기
  - projects 디렉토리에서 다음 명령어를 실행한다. (`cargo new`)
    ```
    $ cargo new hello_cargo
    $ cd hello_cargo
    ```
  - 이렇게 하면 몇 가지 디렉토리/파일들이 생성된다.
    * Cargo.toml: Tom's Obvious, Minimal Language 포맷 파일으로 패키지 이름, 버전, 에디션 및 의존성에 대해 기입되어 있다. 참고로 코드 패키지를 크레이트(crate)라고 한다.
    * .gitignore: git 업로드시 누락해야 할 항목을 기재해야 한다.
    * src 디렉토리

* 카고로 프로젝트 빌드/실행하기
  - `cargo build`: 이렇게 하면 현재 디렉토리가 아닌 target/debug/hello_cargo에 실행 파일을 생성한다. (기본 빌드: 디버그 모드)
  - `cargo build --release`: 릴리즈 모드로 빌드
  - `cargo run`: exe 파일을 직접 실행하는 방법 말고도 이렇게 프로그램을 실행할 수도 있다. (빌드 후 실행)
  - `cargo check`: 실행하지 않고 문제 없이 컴파일되는지 확인만 한다. (빌드에서 실행 파일을 생성하는 과정이 생략됨)

## 예제: 추리 게임

* cargo new로 프로젝트를 생성합니다.

```
$ cargo new guessing_game
$ cd guessing_game
```

* Cargo.toml 파일에서 dependencies 내용을 추가합니다.

```
...

[dependencies]
rand = "0.8.5"
```

* main.rs 파일은 다음과 같습니다.

```rust
use rand::Rng;    // 표준 라이브러리가 아니므로 의존성에 추가해야 함 (library crate)
use std::cmp::Ordering;
use std::io;    // 표준 입출력 라이브러리

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);    // 1~100까지의 랜덤 넘버를 생성해서 secret_number에 저장함

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();    // 사용자 입력값을 저장할 변수를 생성

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");    // 사용자로부터 값을 입력 받음

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };    // 숫자이면 OK, 숫자가 아니면 오류

        println!("You guessed: {guess}");    // 사용자가 입력한 값을 출력함

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }    // 사용자가 입력한 값과 secret_number와 비교해서 결과를 알려줌, 일치하면 루프 종료
    }
}
```

