# Function Scope, Block Scope and Lexical Scope



## 스코프 (scope)

\- **범위, 영역** [네이버 사전]

\- 어느 범위까지 **참조**하는지. 즉, 변수와 매개변수(parameter)의 **접근성과 생존기간**을 뜻합니다. [블로그]

\- 스코프는 **참조 대상 식별자**(identifier, 변수, 함수의 이름과 같이 어떤 대상을 다른 대상과 구분하여 식별할 수 있는 유일한 이름)를 찾아내기 위한 규칙이다. [poiemaWeb]

\- 자바스크립트에서 스코프란 어떤 변수들에 **접근**할 수 있는지를 정의합니다. [블로그]



Quiz)

```javascript
if(true) {
  let i = 10;
}

console.log(i) ??
```



## 스코프의 구분

> 전역 스코프 (Global scope)

코드 어디에서든지 참조할 수 있다.

> 지역 스코프 (Local scope or Function-level scope)

함수 코드 블록이 만든 스코프로 함수 자신과 하위 함수에서만 참조할 수 있다. 함수 내에서 선언된 매개변수와 변수는 함수 외부에서는 유효하지 않다

```javascript
var x = 'global';
function ex() {
  var x = 'local';
  x = 'change';
}
ex(); 
alert(x); 
```



> block-level scope

let과 const로 선언한 변수는 블록이 만든 스코프로 자신과 하위 블록에서만 참조하게 된다.

```javascript 
function foo() {
  for(let i = 1; i < 10; i++) {
    console.log(i) // 1 ~ 10
  }
  console.log(i) // i is not defined
}

foo();
```



## 스코프 체인

- 변수의 범위를 함수의 지역 스코프부터 전역 변수들이 있는 전역 스코프까지 점차 넓혀가며 찾는 관계
- 자신과 상위 스코프들의 변수 객체



## lexical scoping (어휘적 범위 ≒ 정적 범위)

스코프는 함수를 **호출** 할때가 아니라 **선언할** 때 생긴다

```javascript
var name = 'zero';
function log() {
  console.log(name);
}

function wrapper() {
  name = 'nero';
  log();
}
wrapper(); //nero
```



```javascript
var name = 'zero';
function log() {
  console.log(name);
}

function wrapper() {
  var name = 'nero';
  log();
}
wrapper(); //zero
```



## 켄텍스트

스크립트를 로딩하는 순간 모든 것을 포함하는 **전역 컨텍스트**가 생기고 종료될 때까지 유지된다. 

또한 함수를 호출할 때마다 **함수 컨텍스트**가 생긴다.

#### 컨텍스트 원칙

- 먼저 전역 컨텍스트 하나 생성 후, 함수 호출 시마다 컨텍스트가 생긴다
- 컨텍스트 생성 시 컨텍스트 안에 **변수객체(arguments, variable), scope chain, this**가 생성된다
- 컨텍스트 생성 후 함수가 실행되는데, 사용되는 변수들은 변수 객체 안에서 값을 찾고, 없다면 스코프 체인을 따라 올라가며 찾는다.
- 함수 실행이 마무리되면 해당 컨텍스트는 사라진다(클로저 제외). 페이지가 종료되면 전역 컨텍스트가 사라진다.

#### 

```javascript
var name = 'zero'; // (1)변수 선언 (6)변수 대입
function wow(word) { // (2)변수 선언 (3)변수 대입
  console.log(word + ' ' + name); // (11)
}
function say () { // (4)변수 선언 (5)변수 대입
  var name = 'nero'; // (8)
  console.log(name); // (9)
  wow('hello'); // (10)
}
say(); // (7)
```

```js
'wow 컨텍스트': {
  변수객체: {
    arguments: [{ word : 'hello' }],
    variable: null,
  },
  scopeChain: ['wow 변수객체', '전역 변수객체'],
  this: window,
}
```

- lexical scoping에 따라 wow함수의 스코프 체인은 선언 시에 이미 정해져있다. 따라서 say스코프는 wow컨텍스트의 **scope chain**이 아니다.



## 호이스팅

- 변수를 선언하고 초기화했을 때 선언 부분이 최상단으로 끌어올려지는 현상
  - 함수 표현식이 아니라 **함수 선언식**일 때는 식 자체가 통째로 호이스팅 됨



```jsx
sayWow(); // (3)
sayYeah(); // (5) 여기서 대입되기 전에 호출해서 에러
var sayYeah = function() { // (1) 선언 (6) 대입
  console.log('yeah');
}
function sayWow() { // (2) 선언과 동시에 초기화(호이스팅)
  console.log('wow'); // (4)
}
```

일단 처음 실행 시 전역 컨텍스트가 먼저 생긴다. sayWow 함수는 함수 선언식이므로 컨텍스트 생성 후 바로 대입된다.



```jsx
'전역 컨텍스트': {
  변수객체: {
    arguments: null,
    variable: [{ sayWow: Function }, 'sayYeah'],
  },
  scopeChain: ['전역 변수객체'],
  this: window,
}
```



## [클로저](../closure/closure.md)

- 비공개 변수를 가질 수 있는 환경에 있는 함수
- 비공개 변수는 클로저 함수 내부에 생성한 변수도 아니고, 매개변수도 아닌 변수를 의미한다.
- 클로저를 말할 때는 스코프/컨텍스트/비공개 변수화 함수의 관계를 항상 같이 말해주어야한다.





> 참고자료: 
>
> - [실행 컨텍스트와 자바스크립트의 동작 원리](https://poiemaweb.com/js-execution-context)

