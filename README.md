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

## 변수와 가변성

* 러스트에서 변수는 기본적으로 불변(immutable)이다.
  - 불변인 변수는 값이 할당되면 그 값을 변경할 수 없다.
  - ```rust
    let x = 5;
    x = 6;    // 컴파일 오류 발생
    ```
  - ```rust
    let mut x = 5;    // mut 키워드를 붙이면 가변(mutable) 변수가 되므로 컴파일 오류가 발생하지 않는다.
    x = 6;
    ```

* 상수는 `mut` 키워드를 사용할 수 없으며 항상 불변이다.
  - `const` 키워드를 사용해야 하며 반드시 타입을 명시해야 한다.
  - 상수의 스코프는 전역(global)입니다.
  - ```rust
    const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
    ```

* 섀도잉 (shadowing)
  - `let` 키워드로 기존과 동일한 변수를 재선언하면, 기존 변수는 가려집니다(shadowed).
  - ```rust
    fn main() {
        let x = 5;

        let x = x + 1;
        {
            let x = x * 2;
            println!("The value of x in the inner scope is: {x}");
        }
        println!("The value of x is: {x}");
    }
    ```
  - 결과는 다음과 같습니다.
    ```
    The value of x in the inner scope is: 12
    The value of x is: 6
    ```
  - `mut`과 `let`의 차이점: let 키워드는 새로운 변수를 만들기 때문에 다른 타입의 값을 저장할 수 있다.
 
## 데이터 타입

* 스칼라 타입 (scalar type)
  - 정수 (signed, unsigned)
    * signed 정수: i8, i16, i32, i64, i128, isize (여기서 isize 타입은 아키텍처를 따름, 가령 32/64비트)
    * unsigned 정수: u8, u16, u32, u64, u128, usize (여기서 usize 타입은 아키텍처를 따름, 가령 32/64비트)
    * 10, 16, 8, 2진수 표현이 가능하며 _ 문자로 자릿수 구분이 가능함: Decimal (98_222), Hex (0xff), Octal (0o77), Binary (0b1111_0000), Byte (b'A')-u8 전용
  - 부동 소수점 숫자
    * f32, f64: 이것은 마치 C언어의 float, double과 같다.
  - 불리언 (bool)
  - 문자 (char): ''로 감싼다. 4바이트 크기, 유니코드 스칼라 값을 표현한다. (반면 문자열은 ""로 감싼다)

* 복합 타입 (compound type)
  - 튜플: ()로 감싼다. 각 요소는 타입이 다를 수 있다. 한 번 선언되면 크기 변경이 안 된다.
    * 튜플의 각 요소에 접근하려면 .를 써야 한다.
    * ```rust
      let tup: (i32, f64, u8) = (500, 6.4, 1);
      let five_hundred = x.0;
      ```
  - 배열: []로 감싼다. 튜플과 달리 모든 요소는 타입이 같아야 한다. 한 번 선언되면 크기 변경이 안 된다.
    * 배열의 각 요소에 접근하려면 [n]을 써야 한다. 여기서 n은 인덱스 값이며 0부터 시작한다.
    * ```rust
      let a: [i32; 5] = [1, 2, 3, 4, 5];    // 타입은 i32, 개수는 5개
      let b = [3; 5];    // 개수는 5개이며 모두 3으로 채움
      ```

## 함수

* `fn` 키워드로 시작한다.
  - 모든 글자를 소문자로 쓰고, 단어는 _로 구분한다.
  - `fn` 키워드 이후에는 함수 이름과 괄호를 붙여서 함수를 정의한다.
  - 함수 본문은 {}로 감싼다.
  - 파라미터는 다음과 같이 () 안에 넣는다. 파라미터의 타입은 반드시 선언해야 한다.
    ```rust
    fn main() {
        another_function(5);
    }

    fn another_function(x: i32) {
        println!("The value of x is: {x}");
    }
    ```
  - 리턴 값을 갖는 함수는 다음과 같습니다.
    ```rust
    fn five() -> i32 {
        5    // i32 타입 값 5를 리턴한다. 중요한 것은 리턴 구문은 끝에 ;를 붙이지 말아야 한다.
    }

    fn main() {
        let x = five();

        println!("The value of x is: {x}");
    }    
    ```

## 주석

* C/C++ 언어와 비슷하다.
  - 단일 주석: //로 시작한다.
  - 다중 주석: /*와 */로 감싼다.

## 조건문

* if
  - ```rust
    fn main() {
        let number = 6;

        if number % 4 == 0 {
            println!("number is divisible by 4");
        } else if number % 3 == 0 {
            println!("number is divisible by 3");
        } else if number % 2 == 0 {
            println!("number is divisible by 2");
        } else {
            println!("number is not divisible by 4, 3, or 2");
        }
    }
    ```
  - if 이후에 ()가 없습니다. 조건문은 반드시 bool 타입이어야 합니다.

## 반복문

* loop: 무한루프, {}로 감싼다. break로 벗어날 수 있다.
* while: 조건이 true인 동안에는 계속 반복한다.
* for: `for [조건] in [배열]` 형태이며 배열 요소를 순회하며 반복문을 실행한다.
  - ```rust
    fn main() {
        let a = [10, 20, 30, 40, 50];

        for element in a {
            println!("the value is: {element}");
        }
    }
    ```
  - ```rust
    fn main() {
        for number in (1..4).rev() {
            println!("{number}!");
        }
        println!("LIFTOFF!!!");
    }

    // 여기서 number는 3, 2, 1 순서대로 실행한다.
    ```
