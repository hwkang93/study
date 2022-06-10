# .bat 파일에서 임의의 여러 파일 복사하는 예제 코드

```shell
set packages=package1 package2 package3 package4 package5

set directory=C:\Users\KHW\IdeaProjects\geon\

for %%p in (%packages%) do (
    copy %directory%%%p\target\%%p-1.0.jar %directory%total-build\build\%%p-latest.jar
)

pause
```

### 코드 설명

#### set

- 변수 선언
- Java 와는 다르게 '=' 사이에 띄어쓰기가 있을 경우 띄어쓰기도 변수명 or 데이터로 인식하기 때문에, 주의가 필요하다
- JavaScript 의 변수 선언과 비슷하게 배열, 문자열 등 자료형과는 상관없이 ```set``` 으로 선언한다.
- 배열의 경우 ' '(공백)을 기준으로 값을 할당할 수 있다.
- 위의 예제 소스코드에서 첫 번째 set 은 배열 값을 할당한 것이고, 두 번째 set 은 문자열 값을 할당한 것이다.


#### for

- ```for [루프변수] in ([배열]) do ( 반복적으로 진행할 소스코드 )``` 의 형태로 이루어진다.
- [루프변수]는 '%%문자열' 의 형태로 생성할 수 있으며, 하나의 문자열로만 이루어져야 한다.
- [배열]은 ```a b c``` 의 형태로 작성이 가능하며, 이미 선언된 배열 변수를 사용하고자 하는 경우 ```%변수명%``` 으로 사용해야 한다.

> 공백 대신 ';' 과 ',' 을 사용해도 된다.

#### copy

- 파일 복사 명령어
- copy A B 의 형태로 이루어짐
- A,B 는 디렉토리 혹은 파일명으로 이루어지며 파일명의 경우 ```배치 파일이 있는 경로/파일명``` 으로 잡힌다.


### 추가

- ```rem``` : 주석
- ```@echo``` : 자바에서 System.out.println 과 같은 역할


## Reference

[batch for 문 정리 - DARKER THAN BLACK 黒の契約者](https://janggom.tistory.com/460)