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

## 구조체

### 구조체 정의 및 인스턴스화

* 구조체 정의 형식은 다음과 같다. (각 변수는 필드라고 부르며, 이름과 타입을 명시하면 된다)
  ```rust
  struct User {
      active: bool,
      username: String,
      email: String,
      sign_in_count: u64,
  }
  ```

* 구조체를 기반으로 인스턴스를 만드는 방법은 다음과 같다.
  ```rust
  fn main() {
      let user1 = User {
          active: true,
          username: String::from("someusername123"),
          email: String::from("someone@example.com"),
          sign_in_count: 1,
      };
  }
  ```

* 구조체 내 필드에 접근하려면 . 표기법을 사용하면 된다. (인스턴스 전체에 가변성이 적용됨)
  ```rust
  fn main() {
      let mut user1 = User {
          active: true,
          username: String::from("someusername123"),
          email: String::from("someone@example.com"),
          sign_in_count: 1,
      };

      user1.email = String::from("anotheremail@example.com");
  }
  ```

* 다음은 파라미터로 일부 값들을 받고 인스턴스를 생성하여 리턴하는 함수이다. (단, 파라미터와 필드명이 같으면 2번째 경우처럼 표기를 축약할 수 있음)
  - ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            active: true,
            username: username,
            email: email,
            sign_in_count: 1,
        }
    }
    ```
  - ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            active: true,
            username,
            email,
            sign_in_count: 1,
        }
    }
    ```

* 기존 인스턴스로 새 인스턴스 만들기
  - ```rust
    fn main() {
        // --생략--

        let user2 = User {
            active: user1.active,
            username: user1.username,
            email: String::from("another@example.com"),
            sign_in_count: user1.sign_in_count,
        };
    }    
    ```
  - 만약 필드명이 같으면 `..user1`처럼 객체명을 활용하여 표기를 축약할 수 있다.
    ```rust
    fn main() {
        // --생략--

        let user2 = User {
            email: String::from("another@example.com"),
            ..user1
        };
    }
    ```
  - __주의사항: 만약 user2에 할당하는 값 중에서 힙 데이터가 하나라도 있으면 user2로 할당한 후에는 user1을 사용할 수 없게 된다.__

* 명명된 필드 없는 튜플 구조체를 사용하여 다른 타입 만들기
  - ```rust
    struct Color(i32, i32, i32);
    struct Point(i32, i32, i32);

    fn main() {
        let black = Color(0, 0, 0);
        let origin = Point(0, 0, 0);
    }
    // Color와 Point는 구성이 동일하더라도 서로 다른 구조체이므로 할당 호환이 안 된다.
    ```

* 필드가 없는 유사 유닛 구조체
  - 유사 유닛 구조체: 어떤 타입에 대해 트레이트를 구현하고 싶지만 타입 내부에 어떤 데이터를 저장할 필요는 없을 경우 유용하다.
  - ```rust
    struct AlwaysEqual;    // 중괄호, 괄호도 없다!

    fn main() {
        let subject = AlwaysEqual;
    }
    ```
  - 나중에 AlwaysEqual의 모든 인스턴스는 언제나 다른 모든 타입의 인스턴스와 같도록 하는 동작을 구현하여, 이미 알고 있는 결과값의 테스트 용도로 사용한다고 가정해 본다. 이런 동작에는 데이터가 필요 없을 것이다!

### 트레이트 파생으로 유용한 기능 추가하기

* 구조체는 `println!` 및 `{}` 자리표시자(placeholder)와 함께 사용하기 위한 `Display` 구현체가 기본 제공되지 않기 때문에 다음 코드는 오류가 발생한다.
  - ```rust
    struct Rectangle {
        width: u32,
        height: u32,
    }

    fn main() {
        let rect1 = Rectangle {
            width: 30,
            height: 50,
        };

        println!("rect1 is {}", rect1);
    }
    ```

* 다음과 같이 Debug 트레이트를 구현하여 구조체 내용을 println!으로 출력할 수 있다.
  - ```rust
    #[derive(Debug)]    // 구조체 정의에 outer attribute를 작성해 주어야 한다.
    struct Rectangle {
        width: u32,
        height: u32,
    }

    fn main() {
        let rect1 = Rectangle {
            width: 30,
            height: 50,
        };

        println!("rect1 is {:?}", rect1);    // {:?}는 println!에 Debug 출력 형식을 사용하고 싶다고 요청하는 것과 같다.
        // {:?} 대신 {:#?}를 사용하면 좀 더 읽기 편한 형태로 출력될 것이다.
    }
    ```

* Debug 포맷을 사용하여 값을 출력하는 그밖의 방법은 dbg! 매크로를 사용하는 것이다.
  - println!(참조자를 사용함)과 달리 dbg! 매크로는 표현식의 소유권을 가져와서 매크로를 호출한 파일 및 라인 번호를 결과값과 함께 출력하고 다시 소유권을 반환한다.
  - 또한 dbg! 매크로는 표준 출력 콘솔 스트림(stdout) 대신 표준 오류 콘솔 스트림(stderr)에 출력한다.
  - ```rect
    #[derive(Debug)]
    struct Rectangle {
        width: u32,
        height: u32,
    }

    fn main() {
        let scale = 2;
        let rect1 = Rectangle {
            width: dbg!(30 * scale),
            height: 50,
        };

        dbg!(&rect1);
    }
    ```
  - 결과는 다음과 같다.
    ```
    $ cargo run
       Compiling rectangles v0.1.0 (file:///projects/rectangles)
        Finished dev [unoptimized + debuginfo] target(s) in 0.61s
         Running `target/debug/rectangles`
    [src/main.rs:10] 30 * scale = 60
    [src/main.rs:14] &rect1 = Rectangle {
        width: 60,
        height: 50,
    }
    ```

### 메서드 문법

* 메서드는 함수와 유사하다.
  - `fn` 키워드와 함수명으로 선언하고, 파라미터와 리턴 값을 가지며, 다른 어딘가로부터 호출될 때 실행된다.
  - 메서드는 함수와 달리 구조체 (또는 열거형, 트레이트 객체) 안에서 정의되고, 1번째 파라미터는 항상 `self`이다.
  - `self` 파라미터는 메서드를 호출하고 있는 구조체 인스턴스를 의미한다.

* 다음은 Rectangle 구조체에 area 메서드를 정의한 것입니다.
  - ```rust
    #[derive(Debug)]
    struct Rectangle {
        width: u32,
        height: u32,
    }

    // imple 키워드를 사용하여 메서드를 정의한다.
    impl Rectangle {
        // rectangle: &Rectangle 대신 &self를 사용한다. (&self는 실제로는 self: &Self를 줄인 것이다)
        // 가변 구조체를 받고 싶으면 &mut self를 사용할 것
        // self라고 하면 인스턴스의 소유권을 가져올 수 있으나 흔치는 않음
        fn area(&self) -> u32 {
            self.width * self.height
        }
    }

    fn main() {
        let rect1 = Rectangle {
            width: 30,
            height: 50,
        };

        println!(
            "The area of the rectangle is {} square pixels.",
            rect1.area()
        );
    }
    ```

* 연관 함수 (associated function)
  - `impl` 블록 내에 구현된 모든 함수를 연관 함수라고 한다.
  - 동작하는 데 해당 타입의 인스턴스가 필요하지 않다면 1번째 파라미터인 `self`를 생략한 (메서드가 아닌) 연관 함수를 정의할 수도 있다.
  - 메서드가 아닌 연관 함수는 구조체의 새 인스턴스를 리턴하는 생성자로 자주 활용된다.
    ```rust
    impl Rectangle {
        fn square(size: u32) -> Self {
            Self {
                width: size,
                height: size,
            }
        }
    }
    // 연관 함수를 호출할 땐 `let sq = Rectangle::square(3);`처럼 구조체 명에 `::` 구문을 붙여서 호출한다.
    ```

## 열거형과 패턴 매칭

### 열거형 정의하기

* 열거형은 다음과 같이 정의/사용할 수 있습니다.
  ```rust
  enum IpAddrKind {
      V4,
      V6,
  }

  struct IpAddr {
      kind: IpAddrKind,
      address: String,
  }

  let home = IpAddr {
      kind: IpAddrKind::V4,
      address: String::from("127.0.0.1"),
  };

  let loopback = IpAddr {
      kind: IpAddrKind::V6,
      address: String::from("::1"),
  };
  ```

* 각 열거형 배리언트에 데이터를 직접 넣는 방식을 사용하면 열거형을 구조체의 일부로 사용하는 방식보다 더 간결하게 표현할 수 있다.
  - 이렇게 하면 구조체를 사용할 필요가 없어진다.
    ```rust
    enum IpAddr {
        V4(String),
        V6(String),
   }

    let home = IpAddr::V4(String::from("127.0.0.1"));
    let loopback = IpAddr::V6(String::from("::1"));
    ```
  - 또 다음과 같이 각 배리언트가 다른 타입과 다른 양의 연관된 데이터를 가질 수도 있다.
    ```rust
    enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);
    let loopback = IpAddr::V6(String::from("::1"));
    ```

* `Option` 열거형이 널 값보다 좋은 점들
  - `Option` 타입은 값이 있거나 없을 수 있는 상황을 의미한다.
  - 러스트는 다른 언어들에서 흔하게 볼 수 있는 널(null) 개념이 없다.
  - 대신 널(null)에 의해 발생할 수 있는 수많은 오류는 방지하면서 `Option` 열거형으로 동일한 개념을 표현할 수 있다.
    ```rust
    enum Option<T> {
        None,
        Some(T),
    }
    ```
  - 아래는 숫자 타입과 문자열 타입을 갖는 `Option` 값에 대한 예시입니다.
    ```rust
    let some_number = Some(5);
    let some_char = Some('e');

    let absent_number: Option<i32> = None;
    ```

### match 제어 흐름 구조

* if 문과 비슷해 보이지만 다음과 같은 차이점이 있다.
  - if 문은 반드시 bool 값을 리턴해야 하지만, match 제어문은 어떤 타입이든 리턴할 수 있다.

* 다음은 열거형과 열거형의 배리언트를 패턴으로 사용하는 match 표현식입니다. (참고로 열거형은 또 다른 열거형을 파라미터로 받을 수도 있다)
  - ```rust
    enum Coin {
        Penny,
        Nickel,
        Dime,
        Quarter,
    }

    fn value_in_cents(coin: Coin) -> u8 {
        match coin {
            Coin::Penny => {
                println!("Lucky penny!");
                1
            }
            Coin::Nickel => 5,
            Coin::Dime => 10,
            Coin::Quarter => 25,
        }
    }
    ```

* `Option<T>`를 이용하는 매칭
  - 다음 코드는 내부에 값이 있으면 그 값에 1을 더하고, 값이 없으면 `None` 값을 리턴하는 함수입니다.
  - ```rust
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            None => None,
            Some(i) => Some(i + 1),
        }
    }

    let five = Some(5);
    let six = plus_one(five);
    let none = plus_one(None);
    ```

* 포괄 패턴과 `_` 자리표시자
  - 열거형을 사용하면서 특정한 몇 개의 값들에 대해 특별한 동작을 하고, 그 외의 값들에 대해서는 기본 동작을 취하도록 할 수 있다.
  - ```rust
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),    // 주사위 값이 3이 나오면 멋진 모자를 얻는다.
        7 => remove_fancy_hat(),    // 주사위 값이 7이 나오면 모자를 잃게 된다.
        other => move_player(other),    // 해당 숫자만큼 칸을 움직인다.
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn move_player(num_spaces: u8) {}
    ```
  - 만약 위 코드에서 `other` 변수 대신 `_`를 사용하면, 3 또는 7 이외의 숫자가 나오면 주사위를 다시 굴린다.
  - ```rust
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),    // 주사위 값이 3이 나오면 멋진 모자를 얻는다.
        7 => remove_fancy_hat(),    // 주사위 값이 7이 나오면 모자를 잃게 된다.
        _ => reroll(),    // 3 또는 7 이외의 숫자가 나오면 주사위를 다시 굴린다.
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn reroll() {}
    ```

### if let을 사용한 간결한 제어 흐름

* if let 문법은 if와 let을 조합하여 하나의 패턴만 매칭시키고 나머지 경우는 무시하도록 값을 처리하는 간결한 방법을 제공한다.
  - 다음은 어떤 값이 `Some`일 때에만 코드를 실행하도록 하는 `match` 문이다.
  - ```rust
    let config_max = Some(3u8);
    match config_max {
        Some(max) => println!("The maximum is configured to be {}", max),
        _ => (),
    }
    ```
  - 다음은 앞의 `match` 문을 `if let`으로 대체한 코드이다. (이렇게 하면 `_ => ()`를 생략할 수 있다)
  - ```rust
    let config_max = Some(3u8);
    if let Some(max) = config_max {
        println!("The maximum is configured to be {}", max);
    }
    ```

## 패키지, 크레이트, 모듈로 관리하기

* 프로젝트 규모가 커지면 코드를 여러 모듈, 여러 파일로 나누어 관리해야 한다.
  - 한 패키지에는 여러 개의 바이너리 크레이트와 라이브러리 크레이트가 포함될 수 있다.
  - 커진 프로젝트의 각 부분을 크레이트로 나눠서 외부 라이브러리처럼 쓸 수 있다.

* 모듈 시스템은 다음 기능들이 포함되어 있다.
  - 패키지: 크레이트를 빌드, 테스트, 공유하는 데 사용하는 카고 기능
  - 크레이트: 라이브러리나 실행 가능한 모듈로 구성된 트리 구조
  - 모듈과 use: 구조, 스코프를 제어하고, 조직 세부 경로를 감추는 데 사용함
  - 경로: 구조체, 함수, 모듈 등의 이름을 지정함

### 패키지와 크레이트

* 크레이트(crate): 러스트가 컴파일 한 차례에 고려하는 가장 작은 코드 단위
  - 크레이트는 여러 모듈을 담을 수 있다.
  - 크레이트는 바이너리 혹은 라이브러리일 수 있다.
    * 바이너리 크레이트: 실행 가능한 실행 파일로 컴파일할 수 있는 프로그램이며, main 함수를 포함하고 있어야 한다.
    * 라이브러리 크레이트: main 함수를 가지고 있지 않고, 실행 파일 형태로 컴파일되지 않는다.

* 패키지(package): 일련의 기능을 제공하는 하나 이상의 크레이트로 구성된 번들
  - 패키지에는 이 크레이트들을 빌드하는 법이 설명된 Cargo.toml 파일이 포함되어 있다.
  - 카고는 실제로 코드를 빌드하는 데 사용하는 커맨드 라인 도구의 바이너리 크레이트가 포함된 패키지이다.
  - 카고 패키지에는 또한 이 바이너리 크레이트가 의존하고 있는 라이브러리 패키지도 포함되어 있다.
  - 다른 프로젝트도 카고의 라이브러리 크레이트에 의존할 수 있다.
  - 패키지에는 여러 개의 바이너리 크레이트가 포함될 수 있으나, 라이브러리 크레이트는 하나만 넣을 수 있다.

### 모듈을 정의하여 스코프 및 공개 여부 제어하기

* 모듈 작동 방법 및 절차는 다음과 같습니다.
  - 크레이트 루트부터 시작: 크레이트를 컴파일할 때 컴파일러는 먼저 크레이트 루트 파일을 본다.
    * 라이브러리 크레이트의 경우: src/lib.rs
    * 바이너리 크레이트의 경우: src/main.rs
  - 모듈 선언: 크레이트 루트 파일에는 새로운 모듈을 선언할 수 있다. (`mod <모듈명>;`)
    * 컴파일러는 다음 중 이 모듈의 코드가 있는지 살펴본다.
      - `mod <모듈명> { ... }`에서 {} 안에 있는 인라인 코드
      - `src/<모듈명>.rs`
      - `src/<모듈명>/mod.rs` (과거 스타일)
  - 서브모듈 선언: 크레이트 루트가 아닌 다른 파일에서는 서브모듈을 선언할 수 있다.
    * 컴파일러는 부모 모듈 이름의 디렉토리 안쪽에 위치한 아래의 장소들에서 이 서브모듈의 코드가 있는지 살펴본다.
      - `mod <서브모듈명> { ... }`에서 {} 안에 있는 인라인 코드
      - `src/<모듈명>/<서브모듈명>.rs`
      - `src/<모듈명>/<서브모듈명>/mod.rs` (과거 스타일)
  - 모듈 내 코드로의 경로: 일단 모듈이 크레이트의 일부로서 구성되면, 공개 규칙이 허용하는 한도 내에서라면 해당 코드의 경로를 사용하여 동일한 크레이트의 어디에서든 이 모듈의 코드를 참조할 수 있다.
    * 예시: `crate::garden::vegetables::Asparagus`
  - 비공개 vs 공개
    * 모듈 내의 코드는 기본적으로 부모 모듈에게 비공개(private)이다.
    * 모듈을 공개(public)로 만들려면, `mod` 대신 `pub mod` 키워드를 사용하여 선언하십시오.
  - use 키워드: 어떤 스코프 내에서 `use` 키워드는 긴 경로의 반복을 줄이기 위한 어떤 아이템으로의 단축 경로를 만들어준다.
    * 예시: `use crate::garden::vegetables::Asparagus;` --> 이렇게 하면 `Asparagus`만 작성하면 된다.

* 다음과 같은 디렉토리 구조와 파일이 있다고 가정하자.
  - ```
    backyard
    |- Cargo.lock
    |- Cargo.toml
    |- src
        |- garden
        |   |- vegetables.rs
        |- garden.rs
        |- main.rs (크레이트 루트)
    ```
  - 다음은 src/main.rs 파일의 내용이다.
    ```rust
    use crate::garden::vegetables::Asparagus;

    pub mod garden;

    fn main() {
        let plant = Asparagus {};
        println!("I'm growing {:?}!", plant);
    }
    ```
  - 다음은 src/garden.rs 파일의 내용이다.
    ```rust
    pub mod vegetables;
    ```
  - 다음은 src/garden/vegetables.rs 파일의 내용이다.
    ```rust
    #[derive(Debug)]
    pub struct Asparagus {}
    ```

* 모듈로 관련된 코드 묶기
  - 모듈은 크레이트의 코드를 읽기 쉽고 재사용하기도 쉽게끔 구조화를 할 수 있게 해준다.
  - 모듈 내의 코드는 기본적으로 비공개(private)이므로, 모듈은 아이템의 공개 여부를 제어할 수 있도록 해준다.
  - `cargo new --lib restaurant` 명령어를 실행하여 `restaurant` 라이브러리를 생성하여 다음과 같은 파일을 만든다. (src/lib.rs)
    ```
    mod front_of_house {
        mod hosting {
            fn add_to_waitlist() {}
            fn seat_at_table() {}
        }

        mod serving {
            fn take_order() {}
            fn serve_order() {}
            fn take_payment() {}
        }
    }
    ```
  - 모듈 트리 구조는 다음과 같다.
    ```
    crate
    |- front_of_house
        |- hosting
        |   |- add_to_waitlist
        |   |- seat_at_table
        |- serving
            |- take_order
            |- serve_order
            |- take_payment
    ```

### 경로를 사용하여 모듈 트리의 아이템 참조하기

* 함수를 호출하려면 그 함수의 경로를 알아야 한다.
  - 절대 경로: 크레이트 루트로부터 시작되는 전체 경로를 의미한다. 외부 크레이트로부터 접근할 때에는 해당 크레이트 이름으로 절대 경로가 시작된다. 현재 크레이트로부터에 대해서는 `crate` 리터럴로부터 시작된다.
  - 상대 경로: 현재의 모듈을 시작점으로 하여 `self`, `super` 혹은 현재 모듈 내의 식별자를 사용한다.

* 절대 경로, 상대 경로 뒤에는 `::`로 구분된 식별자가 하나 이상 나온다.
  - ```rust
    // 자식은 부모의 private 요소에 접근 가능하지만, 부모는 자식의 private 요소에 접근이 불가능함
    mod front_of_house {    // eat_at_restaurant() 함수와 같은 모듈 안에 있으므로 pub가 아니어도 접근 가능함
        pub mod hosting {                  // pub 키워드를 추가해야 컴파일 오류가 발생하지 않음 (경로 노출 효과)
            pub fn add_to_waitlist() {}    // pub 키워드를 추가해야 컴파일 오류가 발생하지 않음 (경로 노출 효과)
        }
    }

    pub fn eat_at_restaurant() {
        // 절대 경로
        crate::front_of_house::hosting::add_to_waitlist();

        // 상대 경로
        front_of_house::hosting::add_to_waitlist();
    }    
    ```

* super로 시작하는 상대 경로
  - `super`로 시작하면 현재 모듈 혹은 크레이트 루트 대신 자기 부모 모듈부터 시작되는 상대 경로를 만들 수 있다. (파일시스템의 `..` 역할)
  - ```rust
    fn deliver_order() {}

    mod back_of_house {
        fn fix_incorrect_order() {
            cook_order();
            super::deliver_order();
        }

        fn cook_order() {}
    }
    ```

* 구조체 공개하기
  - 구조체 정의에 `pub` 키워드를 쓰면 구조체는 공개되어도 구조체 필드는 비공개로 유지된다. (공개 여부는 각 필드마다 정해야 함)
  - ```rust
    mod back_of_house {
        pub struct Breakfast {
            pub toast: String,
            seasonal_fruit: String,
        }

        impl Breakfast {
            pub fn summer(toast: &str) -> Breakfast {
                Breakfast {
                    toast: String::from(toast),
                    seasonal_fruit: String::from("peaches"),
                }
            }
        }
    }

    pub fn eat_at_restaurant() {
        // 호밀 (Rye) 토스트를 곁들인 여름철 조식 주문하기
        let mut meal = back_of_house::Breakfast::summer("Rye");
        // 먹고 싶은 빵 바꾸기
        meal.toast = String::from("Wheat");
        println!("I'd like {} toast please", meal.toast);

        // 다음 라인의 주석을 해제하면 컴파일되지 않습니다; 식사와 함께
        // 제공되는 계절 과일은 조회나 수정이 허용되지 않습니다
        // meal.seasonal_fruit = String::from("blueberries");
    }
    ```

* 열거형 공개하기
  - 열거형을 공개하는 방법은 `enum` 키워드 앞에 `pub` 키워드를 작성하면 된다.
  - 구조체와 달리 모든 배리언트가 공개된다.
  - ```rust
    mod back_of_house {
        pub enum Appetizer {
            Soup,
            Salad,
        }
    }

    pub fn eat_at_restaurant() {
        let order1 = back_of_house::Appetizer::Soup;
        let order2 = back_of_house::Appetizer::Salad;
    }
    ```

### use 키워드로 경로를 스코프 안으로 가져오기

* `use` 키워드를 사용하면 단축경로(shortcut)를 만들 수 있다.
  - 다음은 HashMap 표준 라이브러리 구조체를 바이너리 크레이트의 스코프로 가져오는 관용적인 코드 예시이다.
  - ```rust
    use std::collections::HashMap;

    fn main() {
        let mut map = HashMap::new();
        map.insert(1, 2);
    }
    ```

* as 키워드로 새로운 이름 제공하기
  - `use` 키워드로 동일한 이름의 타입을 스코프로 여러 개 가져오고 싶으면, 경로 뒤에 `as` 키워드를 작성하고 새로운 이름이나 타입 별칭을 작성하면 된다.
  - ```rust
    use std::fmt::Result;
    use std::io::Result as IoResult;

    fn function1() -> Result {
        // --생략--
    }

    fn function2() -> IoResult<()> {
        // --생략--
    }
    ```

* pub use로 다시 내보내기
  - `use` 키워드로 이름을 가져오면, 해당 이름은 새 위치의 스코프에서 비공개가 된다.
  - `pub use` 키워드를 사용하면, 우리 코드를 호출하는 코드가 해당 스코프에 정의된 것처럼 해당 이름을 참조할 수 있다.
  - 아이템을 스코프로 가져오는 동시에 다른 곳에서 아이템을 가져갈 수 있도록 해준다. (다시 내보내기, re-exporting이라고 함)
  - ```rust
    mod front_of_house {
        pub mod hosting {
            pub fn add_to_waitlist() {}
        }
    }

    pub use crate::front_of_house::hosting;    // 외부 코드에 내보내는 효과가 있음

    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
    }
    ```
  - `pub use` 키워드 대신 `use` 키워드를 썼다면, add_to_waitlist() 함수를 호출하려면 `restaurant::front_of_house::hosting::add_to_waitlist()`를 해야 한다.
  - `pub use` 키워드를 썼다면, `restaurant::hosting::add_to_waitlist()`를 사용할 수 있다.

* 외부 패키지 사용하기
  - 외부 패키지를 사용하려면 Cargo.toml 파일의 의존성에 외부 패키지를 추가해야 한다.
  - ```
    ...

    [dependencies]
    rand = "0.8.5"
    ```
  - 이렇게 하면 카고가 [crates.io](https://crates.io/)에서 `rand` 패키지를 비롯한 모든 의존성을 다운로드하고 프로젝트에서 `rand` 패키지를 사용할 수 있게 된다.
  - ```rust
    use rand::Rng;

    fn main() {
        let secret_number = rand::thread_rng().gen_range(1..=100);
    }
    ```

* 중첩 경로를 사용하여 대량의 use 나열을 정리하기
  - 동일한 크레이트나 동일한 모듈 내에 정의된 아이템을 여러 개 사용할 경우 다음과 같이 `use` 문이 많아지게 된다.
  - 예시 1)
    ```rust
    // 줄이기 전
    use std::cmp::Ordering;
    use std::io;
    ```
    ```rust
    // 줄인 후
    use std::{cmp::Ordering, io};
    ```
  - 예시 2)
    ```rust
    use std::io;
    use std::io::Write;
    ```
    ```rust
    use std::io::{self, Write};
    ```

* 글롭 (glob) 연산자
  - 경로에 글롭 (glob) 연산자 `*`를 붙이면 경로 안에 정의된 모든 공개 아이템을 가져올 수 있다. (남용하지 않는 편이 좋다)
  - ```rust
    use std::collections::*;
    ```

### 별개의 파일로 모듈 분리하기

* 만약 프로젝트 규모가 커지면 한 파일에 여러 모듈을 정의하지 않고 모듈을 여러 파일로 나누는 편이 유리하다.
  - 모듈 선언: 크레이트 루트 파일에는 새로운 모듈을 선언할 수 있다. (`mod <모듈명>;`)
    * 컴파일러는 다음 중 이 모듈의 코드가 있는지 살펴본다.
      - `mod <모듈명> { ... }`에서 {} 안에 있는 인라인 코드
      - `src/<모듈명>.rs`
      - `src/<모듈명>/mod.rs` (과거 스타일)
  - 서브모듈 선언: 크레이트 루트가 아닌 다른 파일에서는 서브모듈을 선언할 수 있다.
    * 컴파일러는 부모 모듈 이름의 디렉토리 안쪽에 위치한 아래의 장소들에서 이 서브모듈의 코드가 있는지 살펴본다.
      - `mod <서브모듈명> { ... }`에서 {} 안에 있는 인라인 코드
      - `src/<모듈명>/<서브모듈명>.rs`
  - __현재 스타일과 과거 스타일은 혼용할 수 없다! (컴파일 오류 발생)__
  - 다른 모듈을 불러올 때 `use` 키워드 또는 `pub use` 키워드를 사용하면 된다.
  - 다른 모듈에게 공개된 모듈만 불러올 수 있다. (`pub mod` 키워드로 정의된 모듈에 한해)

## 일반적인 컬렉션

* 컬렉션의 특정
  - 다수의 값을 담을 수 있는 데이터 구조이다.
  - 빌트인 배열, 튜플과 달리 컬렉션의 데이터들은 힙에 저장된다. (데이터의 양이 컴파일 타임에 결정되지 않으며 컬렉션 크기가 동적이므로)

* 컬렉션의 종류
  - 벡터(Vec): 여러 개의 값을 연속적으로 저장할 수 있게 해준다.
  - 문자열(String): 문자(char)의 모음
  - 해시맵(HashMap): 키-값 형태의 값의 모습

### 벡터에 여러 값의 목록 저장하기

### 문자열에 UTF-8 텍스트 저장하기

### 해시맵에 서로 연관된 키와 값 저장하기
