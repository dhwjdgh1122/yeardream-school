# 논리 연산자

자바스크립트엔 세 종류의 논리 연산자 `||`(OR), `&&`(AND), `!`(NOT)이 있다.



# || (OR)
'OR' 연산자는 두 개의 수직선 기호로 만들 수 있다.

```js
result = a || b;
```

전통적인 프로그래밍에서 OR 연산자는 불린값을 조작하는 데 쓰인다. 인수 중 하나라도 `ture`이면 `ture`를 반환하고, 그렇지 않으면 `false`를 반환한다.

```js
alert( true || true); // true
alert( false || true); // true
alert( true || false); // true
alert( false || false); // false
```

피연산자가 불린형이 아니면, 평가를 위해 불린형으로 반환된다.

예를 들어, 연산 과정에서 숫자 `1`은 `true`로, 숫자 `0`은 `false`로 바뀐다.

```js
if (1 || 0) { // ture || false 와 동일하게 동작
  alert(truthy);
}
```

OR 연산자 `||`은 `if`문에서 자주 사용한다.


