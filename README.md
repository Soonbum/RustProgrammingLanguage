# Rust Programming Language

## 시작하기

### 러스트 설치

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

### Hello, World!

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

### 카고 (Cargo)

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

## 일반적인 프로그래밍 개념

### 변수와 가변성

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

### 데이터 타입

* 스칼라 타입 (scalar type)
  - 정수 (signed, unsigned)
    * signed 정수: `i8`, `i16`, `i32`, `i64`, `i128`, `isize` (여기서 `isize` 타입은 아키텍처를 따름, 가령 32/64비트)
    * unsigned 정수: `u8`, `u16`, `u32`, `u64`, `u128`, `usize` (여기서 `usize` 타입은 아키텍처를 따름, 가령 32/64비트)
    * 10, 16, 8, 2진수 표현이 가능하며 _ 문자로 자릿수 구분이 가능함: Decimal (`98_222`), Hex (`0xff`), Octal (`0o77`), Binary (`0b1111_0000`), Byte (`b'A'`)-u8 전용
  - 부동 소수점 숫자
    * `f32`, `f64`: 이것은 마치 C언어의 float, double과 같다.
  - 불리언 (`bool`)
  - 문자 (`char`): ''로 감싼다. 4바이트 크기, 유니코드 스칼라 값을 표현한다. (반면 문자열은 ""로 감싼다)

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

### 함수

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

### 주석

* C/C++ 언어와 비슷하다.
  - 단일 주석: //로 시작한다.
  - 다중 주석: /*와 */로 감싼다.

### 조건문

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

### 반복문

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

## 소유권 이해하기

### 소유권

* 러스트 언어 고유의 특성으로 타 언어와 달리 메모리 할당시 소유권이라는 개념이 있다.
  - 가비지 컬렉터(GC)를 사용하는 Managed 언어와 C/C++과 같은 Unmanaged 언어는 각자 장단점이 있다.
  - 러스트는 가비지 컬렉터에 의한 성능 저하를 일으키지 않으면서도 메모리 누수 및 실패를 일으키지도 않으면서 고성능을 유지할 수 있다.
  - 메모리 영역은 스택과 힙으로 나뉘어져 있는데, 스택은 고정 크기 객체를 저장하는 반면 힙은 동적 할당된 객체를 저장한다.
  - 소유권은 힙에 저장되는 객체를 관리하는 것이 목표이다.
 
* __소유권 규칙__
  - 각각의 값은 소유자가 정해져 있다.
  - 하나의 값은 오직 하나의 소유자만 있을 수 있다.
  - 소유자가 스코프 밖으로 벗어나면 값은 버려진다. (내부적으로 클래스 내 drop 함수 호출)
 
* 예) String 타입은 문자열 리터럴(스택)과 달리 힙에 할당되어 있으므로 내용을 변경할 수 있다.
  - ```rust
    let mut s = String::from("hello");    // 문자열 리터럴을 String으로 변환함
    s.push_str(", world!");    // push_str()이 문자열에 리터럴을 추가함
    println!("{}", s);    // `hello, world!` 출력
    ```

* 변수와 데이터 간 상호작용 방식: 이동
  - 스택 데이터의 경우 (Primitive 타입)
    ```rust
    let x = 5;    // 5가 x에 바인딩되고
    let y = x;    // x의 복사본이 y에 바인딩
    // 결과적으로 x, y가 각각 존재하며 둘 다 스택에 푸시되어 있음
    // 소유권이 이동, 소멸되지 않으며 자동적으로 깊은 복사가 이루어진다. (스택 데이터는 소유권 개념이 적용되지 않음)
    ```
  - 힙 데이터의 경우 (Class 타입)
    ```rust
    let s1 = String::from("hello");
    let s2 = s1;    // String 객체의 소유권이 s1에서 s2로 이동함

    println!("{}, world!", s1);    // 러스트의 경우 "깊은 복사" 또는 "얕은 복사"가 이루어지지 않고 s1이 s2로 이동되었으므로 컴파일 오류가 발생한다.
    // 참고로 러스트는 힙 데이터를 절대 자동으로 "깊은 복사"를 하지 않는다.
    ```

* 변수와 데이터 간 상호작용 방식: 클론
  - "깊은 복사"를 하고 싶을 때는 clone 공용 메서드를 사용하면 된다.
    ```rust
    let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {}, s2 = {}", s1, s2);
    ```

* 만약 별도로 만든 타입에 Copy 트레이트를 구현하면 이것은 힙 데이터라 하더라도 스택 데이터처럼 "깊은 복사"가 이루어지고 대입 연산 이후에도 소유권이 이전되지 않는다.
  - 다만 Drop 트레이트가 구현된 경우에는 Copy 트레이트를 구현할 수 없다.

* 힙 데이터는 다음과 같이 함수 파라미터와 함수의 리턴을 통해 이동됩니다. (중요)
  ```rust
  fn main() {
      let s1 = gives_ownership();         // gives_ownership이 자신의 반환 값을 s1로 이동시킵니다.
      let s2 = String::from("hello");     // s2가 스코프 안으로 들어옵니다.
      let s3 = takes_and_gives_back(s2);  // s2는 takes_and_gives_back로 이동되는데, 이 함수 또한 자신의 반환 값을 s3로 이동시킵니다.
  } // 여기서 s3가 스코프 밖으로 벗어나면서 버려집니다.
    // s2는 이동되어서 아무 일도 일어나지 않습니다. s1은 스코프 밖으로 벗어나고 버려집니다.

  fn gives_ownership() -> String {             // gives_ownership은 자신의 반환 값을 자신의 호출자 함수로 이동시킬 것입니다
      let some_string = String::from("yours"); // some_string이 스코프 안으로 들어옵니다
      some_string                              // some_string이 반환되고 호출자 함수 쪽으로 이동합니다
  }

  // 이 함수는 String을 취하고 같은 것을 반환합니다
  fn takes_and_gives_back(a_string: String) -> String { // a_string이 스코프 안으로 들어옵니다
      a_string  // a_string이 반환되고 호출자 함수 쪽으로 이동합니다
  }
  ```

### 참조와 대여

* 참조자를 이용하면 소유권을 넘겨주지 않고 값만 넘겨줄 수 있다. (대여)
  ```rust
  fn main() {
      let s1 = String::from("hello");
      let len = calculate_length(&s1);    // & 기호를 변수 앞에 붙인다.
    
      println!("The length of '{}' is {}.", s1, len);    // s1은 소유권을 이전하지 않았으므로 정상적으로 컴파일됨
  }

  fn calculate_length(s: &String) -> usize {    // 참조자를 받으므로 타입명 앞에 & 기호를 붙여야 함
      s.len()
  }
  ```

* 대여한 값은 불변성을 가지므로 수정할 수 없다.
  ```rust
  fn main() {
      let s = String::from("hello");

      change(&s);
  }

  fn change(some_string: &String) {
      some_string.push_str(", world");    // 대여한 값을 수정하는 행위는 컴파일 오류를 발생시킴
  }
  ```

* 다만 가변 참조자(mutable reference)를 사용하면 대여한 값을 수정할 수 있다.
  ```rust
  fn main() {
      let mut s = String::from("hello");    // 가변이 가능하도록 mut 키워드를 붙인다.

      change(&mut s);    // 값을 넘길 때도 mut 키워드를 붙인다.
  }

  fn change(some_string: &mut String) {    // 타입명 앞에도 mut 키워드를 붙인다.
      some_string.push_str(", world");
  }
  ```

* 가변 참조자의 제약사항: 2번 이상 빌려줄 수 없다.
  ```rust
  let mut s = String::from("hello");

  let r1 = &mut s;
  let r2 = &mut s;    // s를 r1에게 빌려줬으므로 이것은 컴파일 오류를 발생시킴

  println!("{}, {}", r1, r2);  
  ```

* 새로운 스코프를 만들면 가변 참조자를 여러 개 만들면서 동시에 존재하는 상황을 회피할 수 있다.
  ```rust
  let mut s = String::from("hello");

  {
      let r1 = &mut s;
  } // 여기서 r1이 스코프 밖으로 벗어나며, 따라서 아무 문제없이 새 참조자를 만들 수 있습니다.

  let r2 = &mut s;
  ```

* 가변 참조자와 불변 참조자는 혼용해서 사용할 수 없다.
  - 이렇게 하는 이유는 가변 참조자에 의해 불변 참조자를 사용하는 부분에서 예기치 않은 값의 변경이 있어서는 안 되기 때문이다.
  - 불변 참조자는 여러 개 만들어도 된다.
    ```rust
    let mut s = String::from("hello");

    let r1 = &s; // 문제없음
    let r2 = &s; // 문제없음
    let r3 = &mut s; // 큰 문제

    println!("{}, {}, and {}", r1, r2, r3);
    ```
  - 단, 불변 참조자를 사용한 이후에는 가변 참조자를 사용할 수 있다.
    ```rust
    let mut s = String::from("hello");

    let r1 = &s; // 문제없음
    let r2 = &s; // 문제없음
    println!("{} and {}", r1, r2);
    // 이 지점 이후로 변수 r1과 r2는 사용되지 않습니다

    let r3 = &mut s; // 문제없음
    println!("{}", r3);
    ```

* 댕글링 참조 (dangling reference)
  - 댕글링 포인터: 어떤 메모리를 가리키는 포인터가 남아있는 상황에서 일부 메모리를 해제해 버림으로써, 다른 개체가 할당받았을지도 모르는 메모리를 참조하게 된 포인터를 말한다.
  - 러스트는 어떤 데이터의 참조자를 만들면, 해당 참조자가 스코프를 벗어나기 전에 데이터가 먼저 스코프를 벗어나는지 컴파일러에서 확인하여 댕글링 참조가 생성되지 않도록 보장한다.
  - 다음과 같이 소멸될 객체의 참조자는 리턴할 수 없습니다.
    ```rust
    fn main() {
        let reference_to_nothing = dangle();
    }

    fn dangle() -> &String {
        let s = String::from("hello");

        &s    // 함수를 벗어나면 String 객체는 해제되는데 참조자를 리턴한다? String 객체는 더 이상 유효하지 않게 되므로 컴파일 오류를 발생시킨다.
    }
    ```
  - 참조자를 만들지 않고 직접 소유권을 이전하면 정상적으로 작동한다.
    ```rust
    fn no_dangle() -> String {
        let s = String::from("hello");

        s    // &s 대신 s로 바꾸면 정상적으로 컴파일된다.
    }
    ```

### 슬라이스

* 컬렉션의 연속된 일련의 요소를 참조하는 기법으로 참조자의 일종이며 소유권을 갖지 않는다.
  - 문자열 슬라이스: String의 일부를 가리키는 참조자이다.
  - 슬라이스는 문자열 외에도 배열 등 모든 컬렉션에 적용 가능하다.
  - 아래 예시는 ASCII 문자에서는 유효하나 UTF-8 문자열의 경우 멀티바이트 문자의 중간 부분에 슬라이스를 생성하면 오류가 발생한다.
    ```rust
    let s = String::from("hello world");

    let hello = &s[0..5];    // "hello" (인덱스 0 ~ 4까지 해당됨)
    let world = &s[6..11];   // "world" (인덱스 6 ~ 10까지 해당됨)

    let slice = &s[0..2];    // "he" (인덱스 0 ~ 1까지 해당됨)
    let slice = &s[..2];     // "he"

    let len = s.len();

    let slice = &s[3..len];  // "lo world" (인덱스 3 ~ len-1까지 해당됨)
    let slice = &s[3..];     // "lo world"

    let slice = &s[0..len];  // "hello world"
    let slice = &s[..];      // "hello world"
    ```
