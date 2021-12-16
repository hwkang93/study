> 자료 만들기 위해 정리 한번 함

# Naming Conventions (명명 규칙)

오라클에서 권장하는 Naming Conventions 이다.


### Packages

가장 앞에 작성되는 패키지 명은 항상 소문자로 작성되어야 하며, 최상위 도메인 이름 중 하나여야 한다.
그 다음부터는 자체 내부 명명 규칙을 따른다.


### Classes

클래스는 명사로 작성되어야 한다. 단어의 첫 글자는 대문자로 시작하며 다음 글자부터는 소문자로 작성한다. 
단어가 두 가지 이상 사용되는 경우에도 같은 규칙을 적용한다(ImageSprite).
약어 사용은 지양하도록 한다.


### Interfaces

클래스와 동일


### Methods

메소드는 동사여야 하며 소문자로 시작한다.
단어가 두 개 이상 있는 경우 다음 단어의 첫 글자는 대문자로 작성한다.


### Variables

변수는 소문자로 시작하며, 단어가 두 개 이상 있는 경우 다음 단어의 첫 글자는 대문자로 작성한다.
변수 명은 _ $ 로 시작할 수 없다.

변수는 짧지만 의미가 있어야 한다. 다른 사람에게 변수의 의미를 충분히 전달할 수 있어야 한다.
임시 변수 (for 문 안에 사용하는 int i 등)를 제외한 변수는 한글자로 만드는 것을 지양한다. 


### Constants

상수는 대문자로 작성한다.
단어가 두 개 이상 있는 경우 _ 로 구분한다.


## Reference

[ORACLE - Naming Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html)