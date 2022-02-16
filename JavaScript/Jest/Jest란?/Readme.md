# Jest

# Jest란?
Facebook에서 지원하고 있는 simplicity를 강조한 JavaScript 테스트 프레임워크입니다.
테스트를 병렬로 실행하여 빠르고, Mocking이 편리하며 별도의 추가 설정 없이 코드 커버리지를 확인할 수 있다는 장점이 있습니다.
거대한 Node.js 백엔드 시스템에서는 유연성을 강조하는 Mocha라는 모듈을 이용하는 것이 더 편리할 수 있습니다.

# 설치
설치는 아래와 같이 진행됩니다.
```bash
npm install --save-dev jest
yarn add --dev jest
```

# 예제
더하기 함수를 테스트하는 예제를 통해 API를 확인해보겠습니다.
```javascript
// sum.js
function sum(a, b) {
  return a + b;
}
module.exports = sum;

// sum.test.js
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => { // 어떤것을 테스트하는지 적어줍니다.
  expect(sum(1, 2)).toBe(3); 
});

// package.json
{
  "scripts": {
    "test": "jest" // console에서 테스트를 실행하기 위해 수정합니다.
  }
}
```
`package.json`을 예시처럼 수정하면 `yarn test` 또는 `npm run test`명령어를 호출하여 test를 수행할 수 있습니다.
test를 수행하면 아래와 같은 결과를 얻을 수 있습니다.
![](Jest/image2021-12-8_16-15-15.png)

# API
더 많은 API는 [공식 문서](https://jestjs.io/docs/api)를 참고해보세요.
참고: timeout의 기본값은 5 sec 입니다.

## Globals
먼저, 테스트 전후로 Pre-Condition, Post-Condition을 구성하는 API들을 알아보겠습니다.
### afterAll(fn, timeout)
해당 테스트 파일의 모든 테스트가 실행되고 나서 `fn`을 호출합니다.
`describe` 블록 안에 있는 경우 `describe` 블록의 테스트가 모두 실행되고 나서 `fn`을 호출합니다.
### afterEach(fn, timeout)
`afterEach` 함수가 존재하는 범위(scope)내에서 각 테스트가 끝날 때마다 `fn`을 호출합니다.
### beforeAll(fn, timeout)
`beforeAll` 함수가 존재하는 범위(scope)내에서 모든 테스트를 시작하기 전 `fn`을 호출합니다.
### beforeEach(fn, timeout)
`beforeEach` 함수가 존재하는 범위(scope)내에서 각 테스트가 시작하기 전 마다 `fn`을 호출합니다.
### decribe(name, fn)
관련있는 테스트들을 묶어 그룹화합니다.
중첩하여 사용할 수도 있습니다.

아래의 예시는 `describe`를 중첩하여 사용한 예입니다.
```javascript
const binaryStringToNumber = binString => {
  if (!/^[01]+$/.test(binString)) {
    throw new CustomError('Not a binary number.');
  }

  return parseInt(binString, 2);
};

describe('binaryStringToNumber', () => {
  describe('given an invalid binary string', () => {
    test('composed of non-numbers throws CustomError', () => {
      expect(() => binaryStringToNumber('abc')).toThrowError(CustomError);
	  });

    test('with extra whitespace throws CustomError', () => {
      expect(() => binaryStringToNumber('  100')).toThrowError(CustomError);
    });
  });

  describe('given a valid binary string', () => {
    test('returns the correct number', () => {
      expect(binaryStringToNumber('100')).toBe(4);
    });
  });
});
```

### test(name, fn, timeout)
테스트를 실행합니다.
`it(name, fn, timeout)`으로도 사용 가능합니다.

참고 : 비동기 테스트
test 함수에서 `Promise`가 반환되는 경우 Jest는 test를 완료하기 위해 대기합니다.
또는 test함수에 인자를 제공해도 대기합니다. (인자는 통상적으로 done을 사용)
이 방법은 callback 함수를 처리할 때 매우 편리합니다.

아래의 예는 비동기 테스트를 진행하는 두 가지 방법을 보여줍니다.
```javascript
test('the data is peanut butter', () => {
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});

test('the data is peanut butter', done => {
  function callback(data) {
    try {
      expect(data).toBe('peanut butter');
      done();
    } catch (error) {
      done(error);
    }
  }

  fetchData(callback);
});
```

## Expect
JUnit, Assertj의 Assertions와 비슷합니다.
[API 문서](https://jestjs.io/docs/expect)에서 필요한 것들을 찾아 사용합시다.

### 간단한 예제 모음
`.toBe`에 대한 예제.
참고: 부동소수점형에는 `.toBe` 대신 `.toBeCloseTo`를 사용하는것이 좋습니다. 부동소수점은 정밀도 문제가 있기 때문입니다.
```javascript
const can = {
  name: 'pamplemousse',
  ounces: 12,
};

describe('the can', () => {
  test('has 12 ounces', () => {
    expect(can.ounces).toBe(12);
  });

  test('has a sophisticated name', () => {
    expect(can.name).toBe('pamplemousse');
  });
});
function bloop() {
  return null;
}

test('bloop returns null', () => {
  expect(bloop()).toBeNull();
});
```

참고: `.toBe`와 `.toEqual`은 다릅니다. `.toEqual`은 객체의 모든 properties를 비교합니다.(deep equality)
`.toBe`는 value비교에 `Object.is`를 사용하고 `.toEqual`은 value의 각 property를 비교하기 위해 `Object.is`를 사용합니다. ( 여기서 value는 expect(value)의 value )
```javascript
const can1 = {
  flavor: 'grapefruit',
  ounces: 12,
};
const can2 = {  
  flavor: 'grapefruit',
  ounces: 12,
};

describe('the La Croix cans on my desk', () => {
  test('have all the same properties', () => {
    expect(can1).toEqual(can2);
  });
  test('are not the exact same can', () => {
    expect(can1).not.toBe(can2); // 서로 다른 객체이기 때문에(객체의 주소(참조)가 다름) 다르다고 판단합니다.
  });
});

expect([1, 2, 3]).toHaveLength(3);

expect('abc').toHaveLength(3);

expect('').not.toHaveLength(5);
```

# Reference
 [https://jestjs.io](https://jestjs.io/) 