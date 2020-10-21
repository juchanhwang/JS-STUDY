# Closure (클로저)

- 비공개 변수를 가질 수 있는 환경에 있는 함수
- 비공개 변수는 클로저 함수 내부에 생성한 변수도 아니고, 매개변수도 아닌 변수를 의미한다.
- 클로저를 말할 때는 스코프/컨텍스트/비공개 변수화 함수의 관계를 항상 같이 말해주어야한다.

- 클로저는 자바스크립트 고유의 개념이 아니라 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어(Functional Programming language: 얼랭(Erlnag), 스칼라(Scala), 하스켈(Haskell), 리스프(Lisp)…)에서 사용되는 중요한 특성이다.

> “A closure is the combination of a function and the lexical environment within which that function was declared.”
> 클로저는 함수와 그 함수가 선언됐을 때의 렉시컬 환경(Lexical environment)과의 조합이다.
>
> 출처: MDN



```js
function makeFn() {
  var name = "ju"
  function showName() {
    console.log(name);
  }
  return showName;
}

var myFn = makeFn();
//myFn변수에 showName을 리턴
//유효범위의 어휘적 환경을 유지
myFn();
//리턴된 showName 함수를 호출
```

`showName`함수는 실행되기 전에 외부함수인 makeFn()로부터 리턴되어 `myFn`변수에 저장된다.

몇몇 프로그래밍 언어에서, 함수 안의 지역 변수들은 그 함수가 처리되는 동안에만 존재한다. `makeFn()`호출이 끝나면(`showName`함수가 리턴되고 나면) `name`변수에 더이상 접근할 수 없게 될 것으로 예상할 수도있다.

하지만 JS는 다르다. JS는 함수를 리턴하고, 리턴하는 함수가 클로저를 형성하기 때문이다. 클로저는 함수와 함수가 선언된 어휘적 환경의 조합이다. 이 환경은 클로저가 생성된 시점의 유효 범위 내에 있는 모든 지역 변수로 구성된다. 첫 번째 예시의 경우, **myFn는 makeFn이 호출될 때 생성된 showName함수의 인스턴스에 대한 참조다**.

`showName` 의 인스턴스는 변수 name이 있는 어휘적 환경에 대한 참조를 유지한다. 이런 이유로 myFn이 호출될 때 변수 name은 사용할 수 있는 상태로 남게 되고 "ju"가 console.log에 전달된다. 



### 클로저의 활용

> 클로저는 자신이 생성될 때의 환경(lexical environment)을 기억해야 하므로 메모리 차원에서 손해를 볼 수 있다. 하지만 클로저는 자바스크립트의 강력한 기능으로 이를 적극적으로 사용해야한다. 클로저가 유용하게 사용되는 상황에 대해 살펴보자.

> 클로저는 어떤 데이터(어휘적 환경)와 그 데이터를 조작하는 함수를 연관시켜주기 때문에 유용하다. 이것은 객체가 어떤 데이터와(그 객체의 속성) 하나 혹은 그 이상의 메소드들을 연관시킨다는 점에서 객체지향 프로그래밍과 분명히 같은 맥락에 있다.
>
> 결론적으로 오직 하나의 메소드를 가지고 있는 객체를 일반적으로 사용하는 모든 곳에 클로저를 사용할 수 있다.
>
> 



#### - 상태 유지

- 클로저가 가장 유용하게 사용되는 상황은 현재 상태를 기억하고 변경된 최신 상태를 유지하는 것이다.

#### - 전역 변수의 사용 억제

```html
<!DOCTYPE html>
<html>
  <body>
  <p>클로저를 사용한 Counting</p>
  <button id="inclease">+</button>
  <p id="count">0</p>
  <script>
    var incleaseBtn = document.getElementById('inclease');
    var count = document.getElementById('count');

    var increase = (function () {
      // 카운트 상태를 유지하기 위한 자유 변수
      var counter = 0;
      // 클로저를 반환
      return function () {
        return ++counter;
      };
    }());

    incleaseBtn.onclick = function () {
      count.innerHTML = increase();
    };
  </script>
</body>
</html>

```

스크립트가 실행되면 즉시실행함수가 호출되고 변수 increase에는 함수 `function () { return ++counter; }` 가 할당된다. 이 함수는 자신이 생성되었을 때의 렉시컬 환경을 기억하는 클로저다. 즉시실행함수는 호출된 이후 소멸되지만 즉시실행함수가 반환하는 함수는 변수 increase에 할당되어 increase 버튼을 클릭하면 클릭 이벤트 핸들러 내부에서 호출된다. 이때 클로저인 이 함수는 자신이 선언됐을 때의 렉시컬 환경인 즉시실행함수의 스코프에 속한 지역변수 counter를 기억한다. 따라서 즉시실행함수의 변수 counter에 접근할 수 있고 변수 counter는 자신을 참조하는 함수가 소멸될 때까지 유지된다.



즉시실행함수는 한번만 실행되므로 increase가 호출될 때마다 변수 counter가 재차 초기화될 일은 없을 것이다. 변수 counter는 외부에 직접 접근할 수 없는 private 변수이므로 전역 변수를 사용했을 때와 같이 의도되지 않은 변경을 걱정할 필요도 없기 때문에 보다 안정적인 프로그래밍이 가능하다. 

> 변수의 값은 누군가에 의해 언제든지 변경될 수 있어 오류 발생의 근본적인 원인이 될 수 있다. 상태 변경이나 가변 데이터를 피하고 불변셩을 지향하는 함수형 프로그래밍에서 부수(side effect)를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이기 위해 클로저는 적극적으로 사용된다. 



#### - 정보의 은닉

```js 
function Counter() {
  // 카운트를 유지하기 위한 자유 변수
  var counter = 0;
  
  // 클로저
  this.increase = function () {
    return ++counter;
  };
  
  // 클로저
  this.decrease = function () {
    return --counter;
  };
}

const counter - new Counter();

console.log(counter.increase()); // 1
console.log(counter.decrease()); // 0
```

생성자 함수 Counter는 increase, decrease 메소드를 갖는 인스턴스를 생성한다. 이 메소드들은 모두 자신이 생성됐을 때의 렉시컬 환경인 생성자 함수 Counter의 스코프에 속한 변수 counter를 기억하는 클로저이며 렉싴러 환경을 공유한다. 생성자 함수가 생성한 객체의 메소드는 객체의 프로퍼티에만 접근할 수 있는 것이 아니며 자신이 기억하는 렉시컬 환경의 변수에도 접근할 수 있다.

이때 생성자 함수 Counter의 변수 counter는 this에 바인딩된 프로퍼티가 아니라 변다. counter가 this에 바인딩된 프로퍼티라면 생성자 함수 Counter가 생성한 인스턴스를 통해 외부에 접근이 간으한 public 프로퍼티가 되지만 생성자 함수 Counter가 생성한 인스턴스의 메소드인 increase, decrease는 클로저이기 때문에 자신이 생성됐을 때의 렉시컬 환경인 생성자 함수 Counter의 변수 counter에 접근할 수 있다. 이러한 클로저의 특징을 사용해 클래스 기반 언어의 `private` 키워드를 흉내낼 수 있다.



#### - 클로저 스코프 체인

모든 클로저에는 세가지 스코프(범위)가 있다.

- 지역 범위(Local Scope, Own Scope)
- 외부 함수 범위(Outer Functions Scope)
- 전역 범위(Global Scope)

따라서, 우리는 클로저에 대해 세가지 범위 보두 접근할 수 있지만, 중첩된 내부 함수가 있는 경우 종종 실수를 저지른다. 아래 예제를 확인해보자





## 결론







