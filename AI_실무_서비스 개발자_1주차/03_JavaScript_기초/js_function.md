# 함수

유사한 동작을 하는 코드가 여러 곳에서 필요할 때가 있다.

사용자가 로그인이나 로그아웃을 했을 때 안내 메시지를 보여주는 동작처럼

함수는 프로그램을 구성하는 주요 '구성 요소' 이다. 함수를 이용하면 중복 없이 유사한 동작을 하는 코드를 여러 번 호출할 수 있다.

예시를 들자면, `alert(message)`, `prompt(message, default)`, `confirm(question)` 과 같은 내장 함수가 있다.


# 함수 선언

함수 선언 방식을 이용해서 함수를 만들어보자.

```js
function showMessage () {
  alert("안녕");
}
```

`function` 키워드, 함수이름, 괄호로 둘러싼 매개변수를 차례로 써준다.

위 함수에선 매개변수가 없는데, 만약 매개변수가 여러 개 있다면 각 매개변수를 콤마로 구분해주자.


함수를 구성하는 코드의 모임인 '함수 본문(body)'을 중괄호로 감싸 붙여주자.

```js
function name(parameter1, parameter2,...parameterN) {
  // 함수 본문
}
```

정의한 함수는 함수 이름 옆에 괄호를 붙여 호출할 수 있다.

```js
function showMessage () {
  alert("안녕");
}

showMessage();
```

`showMessage()`로 함수를 호출하면 함수 본문이 실행된다.



# 지역 변수

함수 내에서 선언한 변수인 지역 변수(local variable)는 함수 안에서만 접근할 수 있다.

```js
function showMessage () {
  let message = "안녕"; // 지역 변수

  alert(message);
}

showMessage(); // 안녕

alert(message); // ReferenceError: message is not defined
```
변수 message는 함수 내에서 선언한 변수인 지역 변수이기 때문에 에러가 발생한다.

# 외부 변수 

함수 내부에서 함수 외부의 변수인 외부 변수에 접근할 수 있다.

```js
let userName = 'jungho';

function showMessage() {
  let message = 'hello' + userName;
  alert(message)
}

showMessage(); // hellojungho
```

함수에선 외부 변수에 접근하는 것뿐만 아니라, 수정도 할 수 있다.



함수 내부에 외부 변수와 동일한 이름을 가진 변수가 선언되었다면, 내부 변수는 외부 변수를 가린다(안보이게).


```js
let userName = 'jungho';  // 1

function showMessage () {
  let userName = 'not jungho';  // 2,3

  let message = 'hello' + userName;
  alert(message);
}


showMessage(); // 4 hellonot jungho

alert(userName); // 5
```

1. 함수 외부에 `userName` 변수 'jungho' 가 있음
2. 함수 내부에 같은 변수의 이름인 `userName` 'not jungho' 가 있음
3. 함수 내부의 `userName` 변수 'not jungho'가 함수 외부의 `userName` 'jungho'를 가림
4. `showMessage();`를 호출하면 hellonot jungho가 출력됨 
5. alert(userName); 함수밖에서 작성된 코드이기 때문에 지역변수 `let userName=''not jungho`이 출력되지 않고 함수 밖의 `let userName='jungho'`가 출력된다.



### 핵심 정리
- 지역 변수는 함수안에서만 접근할 수 있다.
- 함수 내부에서 외부 변수에 접근 및 수정할 수 있다.
- 함수 내부와 외부의 변수의 이름이 동일하다면, 내부 변수는 외부 변수를 가린다.




# 전역 변수

위 코드의 `userName` 처럼, 함수 외부에 선언된 변수는 **전역 변수**(global variable)라고 부른다.

전역 변수는 같은 이름을 가진 지역 변수에 의해 가려지지만 않는다면 모든 함수에서 접근할 수 있다.




# 매개 변수

매개 변수(parameter)를 이용하면 임의의 데이터를 함수 안에 전달할 수 있다. 매개변수는 인자 라고 불리기도 한다. 

좀 더 쉽게 설명하자면, 함수를 선언할 때 매개변수(인자)를 만들어 두면, 함수를 호출할 때 바깥에서 원하는 진짜 데이터(인수)를 함수 내부로 안전하게 전달할 수 있다.



아래 코드에서 showMessage 함수는 매개변수 `from` 과 `text`를 가진다.

```js
function showMessage(from, text) { // 1
  alert(from + text); // 2
}

showMessage('oh','jungho'); // 3
```
1. 이름이 `showMessage`이고, 매개변수 `from` 과 `text`를 가진 상자 함수를 만든다.
2. 실행될 본문 코드로 두 매개변수의 값을 더해 화면에 띄우는 `alert`를 준비한다.
3. `showMessage` 함수를 호출한다.
4. 호출 시 전달한 인수 `oh` 와 `jungho`가 각각 매개변수 `from` 과 `text` 상자에 복사되어 들어간다.
5. 상자에 복사된 값들을 사용해 2번 코드가 실행된다.


전역 변수 `from`이 있고, 이 변수를 함수에 전달했다. 함수가 `from`을 변경하지만, 변경 사항은 외부 변수 `from`에 반영되지 않았다. 함수는 언제나 복사된 값을 사용하기 때문


```js
// 1. 외부(전역) 변수 : 원본 종이
let from = '초코';

function chageName(from) {
  // 2. 함수 내부: 복사본 종이에 "우유"를 더함
  from = from + "우유";
  alert("함수 안: " + from);
}

// 3. 함수 실행(원본 "초코"의 복사본을 함수로 보냄)
changeName(from); // 출력: "함수 안: 초코우유"

// 4. 외부 변수 확인
alert("함수 밖: " + from); // 출력: "함수 밖: 초코"
```


함수의 매개변수에 전달된 값을 **인수**(argument)라고 부르기도 한다.

정리하면,

- 매개변수는 함수 선언 방식 괄호 사이에 있는 변수이다.(선언 시 쓰이는 용어)
- 인수는 함수를 호출할 때 매개변수에 전달되는 값이다.(호출 시 쓰이는 용어)

즉, 함수 선언 시 매개변수를 나열하고, 함수를 호출할 땐 인수를 전달해 호출한다.



# 기본값

매개변수를 만들어 두었지만, 함수를 부를 때 값을 전달하지 않으면, 어떻게 될까, 이를 해결하는 방법은 무엇일까?

함수에 매개변수가 2개 설정되어 있는데, 호출할 때 인수를 1개만 보내거나 보내지 않으면 전달받지 못한 매개변수에는 `undefined`가 자동으로 연결된다.

```js
function showMessage(from, text) {
  alert(from + text);
}

showMessage('jungho'); // text에 값을 주지 않음
// 출력 : jungho undefined
```

이렇게 `undefined`가 출력되는 것을 방지하기 위해, 값이 들어오지 않았을 때 사용할 '기본값'을 미리 정해둘 수 있다.

```js
// text에 값이 없으면 기본으로 'hello'를 사용해라
function showMessage(from, text ='hello') {
  alert(from + text);
}

showMessage('jungho'); // 출력:junghohello(기본값사용)
showMessage('jungho', 'welcome') // 출력:junghowelcome(전달값 사용)
```


# 반환 값

함수를 호출했을 때 함수를 호출한 그곳에 특정 값을 반환하게 할 수 있다.

함수는 단순히 안에서 코드를 실행하고 끝낼 수도 있지만, 작업한 결과물을 함수 바깥으로 짠! 하고 돌려줄(반환할)수도 있다. 이때 사용하는 키워드가 `return`이다.


쉽게 생각해서 함수를 '재료를 넣으면 결과물이 나오는 자판기'라고 보면 된다.

```js
function sum(a,b) {   // 1
  return a+b;          // 2
}

let result = sum(1,2);    //3
alert(result); // 4
```

- 3번 코드 `sum(1,2)` 호출 인수 1과2를 던진다.

- 1번 코드 매개변수 a와b에 각각 1,2가 복사되어 들어간다.

- 2번 코드 `return a+b;`가 실행되어 3이라는 값을 만든다.

- 3번 코드 호출 위치로 복귀하여 함수를 불렀던 - 3번 코드줄의 `sum(1,2)` 자리가 결과값 `3`으로 치환된다.
- 4번 코드 최종적으로 `let result =3;`이 되어 `result` 변수에 3이 들어간다.

