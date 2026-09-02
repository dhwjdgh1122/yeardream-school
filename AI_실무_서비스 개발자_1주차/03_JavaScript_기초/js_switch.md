# switch문

복수의 `if`조건문은 `switch`문으로 바꿀 수 있다.

```js
switch(x) {
  case 'value1':  // if (x === 'value1')
    ...
    [break]

  case 'value2':  // if (x === 'value2')
    ...
    [break]

  default:
    ...
    [break]
}
```

- 변수 x의 값이랑 첫 번째 case ``value1``를 일치 비교한 후, 두 번째 case문의 `value2`와 비교한다. 이런 과정이 반복된다.


# switch에서의 자료형의 중요성

switch문은 일치 비교로 조건을 확인한다. 비교하려는 값과 `case`문의 값의 형과 값이 같아야 해당 `case`문이 실행된다.


```js
let arg= prompt("값을 입력하시오");
switch (arg) {
  case '0':
  case '1':
    alert(" 0이나 1을 입력했다");
    break;

  case 3:
    alert("입력한 3은 문자열3입니다 = case가 숫자 3 입니다.");
    break;
  
  case '3':
    alert("입력한 3은 문자열3이며, case가 '3'이 맞습니다");
    break;
}
```

`case 3:` 은 실행되지 않는다 why? 현재 `prompt`는 문자열(String)으로 입력받기 때문에 

`case '3'`의 alert가 실행되며,

`+prompt`로 `+` 연산자를 사용해야한다.