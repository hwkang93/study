# Hoisting

Javascript 에서 호이스팅(Hoisting)이란, 인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것을 의미한다.
```var``` 로 선언한 변수의 경우 호이스팅 시 ```undefined``` 로 변수를 초기화하지만, 
```let``` 과 ```const``` 로 선언한 변수의 경우 호이스팅 시 변수를 초기화하지 않는다.

> 변수 선언이 해당 변수의 Scope(유효 범위) 최 상단에 이동하는 것과 같은 행동으로 정의하기도 한다. 

```javascript
/**
 * var 타입 호출 후 선언하기
 */
name;
var name = 'kang';
//result : undefined

/**
 * const 타입 호출 후 선언하기
 */
age;
const age = 10;
//result : Uncaught ReferenceError: age is not defined

/**
 * let 타입 호출 후 선언하기
 */
job;
let job = developer;
//result : Uncaught ReferenceError: job is not defined
```

```var``` 로 선언한 변수의 경우 선언하기 전에 호출하면 ```undefined``` 로 초기화된 상태이기 때문에, ```undefined``` 가 출력되지만,
```let, const``` 의 경우 정의되지 않은 변수라는 에러가 발생한다.

## const 상수와 let 변수의 Hoisting 에 대한 오해

위의 예시 코드를 보면, ```let``` 과 ```const``` 키워드로 호출하고 선언한 경우 ```'variable' is not defined``` 라는 에러 메시지가 나온다.
이를 보고 ```let``` 과 ```const``` 는 Hoisting 이 안된다고 생각하는 사람들이 조금 있는 것 같다.
하지만 ```var``` 과 마찬가지로 ```let``` , ```const``` 모두 Hoisting 이 되고 있는데 이에 대해 간단하게 정리하고자 한다.

### Temporal Dead Zone(TDZ) - 일시적 사각지대

```let``` 과 ```const``` 의 Hoisting 에 대해 이해하려면 Temporal Dead Zone 에 대한 이해가 필요하다.

먼저 **Temporal Dead Zone (일시적 사각지대) 이란, 변수 스코프의 맨 위에서부터 변수의 초기화 완료 시점까지를 말한다.**

> Mozilla 에서는 Temporal Dead Zone 을 **시간상 사각지대** 라고 표현을 했다.
> 여기서 **"시간상"** 사각지대로 표현한 이유는, 사각지대가 코드의 작성 순서(위치)가 아니라 코드의 실행 순서(시간)에 의해 형성되기 때문이라고 한다.
> 
> ```javascript
>   {
>       const func = () => console.log(letVar);
>       let letVar = 3;
>       func();
>   }
>```
> 
> 위의 코드에서 ```let``` 변수 선언 코드가 그 변수에 접근하는 함수보다 아래에 위치하지만 (```const func```),
> 함수의 호출 시점이 사각지대 밖이므로 정상 동작한다.

### var 변수의 TDZ





## Hoisting of Function

Javascript 에서 함수를 표현하는 방법은 크게 함수 선언식(Function Declaration)과 함수 표현식(Function Expression)으로 나눌 수 있는데,
함수 선언식은 호이스팅이 적용되지만, 함수 표현식은 호이스팅이 적용되지 않는다.

```javascript
getName();

function getName() {
    return 'kang';
}
//result : 'kang'

getAge();
var getAge = function () {
    return 10;
}
//result : Uncaught TypeError: getAge is not a function
```

# 참고블로그

[mozilla - Hoisting](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)

[mozilla - let](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/let)