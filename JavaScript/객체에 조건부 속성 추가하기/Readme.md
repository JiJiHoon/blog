저는 현재 JavaScript를 사용해 인턴 과제를 진행하고 있습니다.
모듈을 사용하면서 config나, option, headers 같이 객체를 파라미터로 전달하는 함수를 많이 볼 수 있었는데, 이런 함수들을 사용하다 보니 조건에 따라 객체를 구성하고 싶다는 생각이 들어 찾아보았습니다.

제 첫 구현은 아래와 같습니다.
```javascript
const a = {
	foo: 1,
	bar: baz === true ? "hello" : undefined
};
``` 
이 방법은 간단하지만 항상 삼항연산자를 사용해야하고, `undefined`를 대입하는것이 마음에 들지 않았습니다.
또한 여러개의 속성을 추가하는 경우, 표현식을 중복해서 사용해야 한다는 점도 불편했습니다.
```javascript
const a = {
	foo: 1,
	bar: baz === true ? "hello" : undefined,
	qux: baz === true ? "hi" : undefined
};
```

그러다 stackoverflow에서 제가 원하는 질문을 발견했습니다. (역시 없는게 없는...)
[In JavaScript, how to conditionally add a member to an object? - Stack Overflow](https://stackoverflow.com/questions/11704267/in-javascript-how-to-conditionally-add-a-member-to-an-object)

가장 많은 추천을 받은 답변을 적용하면 아래와 같습니다.
```javascript
const a = {
	foo: 1,
	...(baz === true && {bar: "hello"})
}
```
이 방법은 여러개의 속성도 하나의 조건식으로 추가할 수 있습니다! 와우!
```javascript
const a = {
	foo: 1,
	...(baz === true && {bar: "hello", qux: "hi"})
}
```

저는 JavaScript에 익숙하지 않기 때문에, 이런 괴상한 표현식을 보고 어떻게 작동하는 것인지 궁금해졌습니다. 

### 1. `...`연산자?
`...`연산자는  `spread operator`라는 이름으로 불리며 배열이나 객체를 펼칩(spread)니다.
Python의 unpack(`*`) 연산자와 비슷한것 같습니다.
* 함수의 매개변수로 사용될 때에는 `varargs`(가변인자, 나머지 매개변수)로 작동하므로 궁금하다면 [문서](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)를 참고하자.
* `varargs`는 Java, C 등 다른 언어들에도 많이 사용되는 개념이므로 알아두면 좋습니다.


### 2. `baz === true && {bar: "hello"}`는 어떻게 작동하는걸까?
JavaScript의 `&&` 연산자는 앞의 표현식이 `truthy`인 경우, 뒷부분의 표현식을 반환합니다. 
조금 이상하지만, 코드를 통해 설명하자면 아래와 같습니다.
```javascript
// conditionA && conditionB => andOperator(conditionA, conditionB)
function andOperator(conditionA, conditionB) {
  if (conditionA) {
    return conditionB;
  } else {
	  return false;
  }
}
```

결과적으로 `{...(baz === true && {bar: "hello"})}`는 아래의 단계를 거치게 됩니다.
1. `{...(baz === true && {bar: "hello"})}` 
2. `{...(true && {bar: "hello"})}`
3. `{...({bar: "hello"})}`
4. `{...{bar: "hello"}}` 
5. `{bar: "hello"}`


# Reference
[In JavaScript, how to conditionally add a member to an object? - Stack Overflow](https://stackoverflow.com/questions/11704267/in-javascript-how-to-conditionally-add-a-member-to-an-object)
[Spread syntax (...) - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
[Rest parameters - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)
[Logical AND (&&) - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND)