# 마크다운 문법 정리

자주 사용하는 마크다운 문법을 정리한 것이다.

## 제목

제목은 ```#``` 으로 표현한다. #은 총 6개(######)까지 지원한다.

### 사용 예시

```
# 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6
####### 제목 7
```

### 결과

# 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6
####### 제목 7

## 목록

```*```, ```+```, ```-``` 문자를 지원하며 어떤 문자열을 사용하던 동일한 결과를 나타낸다.
뎁스(depth)는 Tab 으로 구분한다.

### 사용 예시

```
- 과일
    + 사과
    + 바나나
    + 복숭아
        * 물복
        * 딱복

- 소프트웨어 엔지니어
    - 백엔드
    - 프론트엔드
    - DBA
```

### 결과

- 과일
    + 사과
    + 바나나
    + 복숭아
        * 물복
        * 딱복

- 소프트웨어 엔지니어
    - 백엔드
    - 프론트엔드
    - DBA

## 문단 및 줄바꿈

마크다운 문법은 여느 문서처럼 엔터를 통한 줄바꿈을 지원하지 않는다.
줄바꿈을 위해서는 문장 마지막에 **띄어쓰기 3칸** 이상을 해야 하며, 문단을 바꾸기 위해서는
엔터 두 번이 필요하다.

```
줄을 바꾸기 위해서는   
해당 라인의 마지막에 띄어쓰기가 세 번 필요합니다.

문단을 바꾸기 위해서는

사이에 공백 열(엔터)이 있어야 합니다.
```
    
## 글자 강조, 취소선, 기울기

### 사용 예시

```
**강조**

~~취소선~~

_기울게_
```

### 결과

**강조**

~~최소선~~

_기울게_

## 인용구

### 사용 예시

```
> 인용구입니다.
> 
> 내부에 다른 마크다운 문법 사용이 가능해요!
> 
> ```java
> public class Test {
>   public static void main(String... args) {
>       System.out.println("Hello World!");
>   } 
> } 
> ```

### 결과

```
> 인용구입니다.
> 
> 내부에 다른 마크다운 문법 사용이 가능해요!
> 
> ```java
> public class Test {
>   public static void main(String... args) {
>       System.out.println("Hello World!");
>   } 
> } 
> ```

## 이미지
### 사용 예시
### 결과

## 링크
### 사용 예시
### 결과

## 수평선
### 사용 예시

```
----------
**********
```

### 결과

----------
**********

## 코드 블럭

코드블럭코드("```")를 사용하여 코드 블럭을 만들 수 있다. 코드블럭 시작점에 언어를 선언하면
해당 언어에 대한 문법 강조(Syntax highlighting)가 적용된다.


### 사용 예시

```
　```
    public class Test {
        public static void main(String... args) {
            System.out.println("Hello world!");
        }
    }
　```

　```java
    public class Test {
        public static void main(String... args) {
            System.out.println("Hello world!");
        }
    }
　```
```

### 결과

```
public class Test {
    public static void main(String... args) {
        System.out.println("Hello world!");
    }
}
```

```java
public class Test {
    public static void main(String... args) {
        System.out.println("Hello world!");
    }
}
```


-----

여기서부터는 마크다운 문법이라기보다는 플러그인 등을 통한 확장 기능이다.
따라서 에디터에 따라 정상적으로 적용되지 않을 수도 있고, 비정상적으로 표출될 수 있다. 

-----

## 수식

https://latex.codecogs.com/

https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/writing-mathematical-expressions

$\sqrt{3x-1}+(1+x)^2$

## UML & Grape

해당 문법은 별도의 플러그인이 필요하며, 모든 마크다운 문법에서 지원하지는 않는다.
개인적으로 사용하는 Typora, Github, IntelliJ 에서는 정상적으로 적용됨을 확인했다.

> [IntelliJ .md 파일에서 다이어그램 사용하기](https://github.com/hwkang93/study/blob/main/etc/intellij_md_add_diagram_plugin.md)

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

https://github.com/hwkang93/baekjoon_algorithm/edit/master/src/level21/README.md

## 접기 / 펼치기

github 

```
<details>
    <summary> 펼치기 <summary>
    
    안녕하세요
</details>
```

<details>
    <summary> 펼치기 </summary>

    ### 안녕하세요!
</details>

## Reference

https://github.com/occidere/TIL/issues/95

https://mermaid-js.github.io/mermaid/#/

https://gist.github.com/ihoneymon/652be052a0727ad59601

https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams