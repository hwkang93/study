# RandomStringUtils 를 사용하여 Random 문자열 만들기

아파치에서 제공하는 ```apache.commons.lang3.RandomStringUtils``` 를 이용하여 랜덤한 문자열을 만드는 예제 소스코드이다

```java
public class RandomTest {

    @Test
    void generateRandomStringAndNumber() {
        int length = 15;                //문자열 길이

        boolean useLetters = true;      //문자 사용 여부
        boolean useNumbers = true;      //숫자 사용 여부

        String random = RandomStringUtils.random(length, useLetters, useNumbers);

        Assertions.assertThat(random.length()).isEqualTo(length);
        
        //예시 결과
        //FACpIe4T7XR6Jtj
    }
}
```

## 참고사항

랜덤하게 문자열을 생성하는 방법이나 숫자를 생성하는 것 등은 이것들 말고도 다양하게 있을 것이다. 
Random 객체가 생성하는 숫자 자체가 실제로는 Random 하지 않다는 의견도 있는 만큼, 이러한 방법들이 항상 최상의 결과를 가져온다고 생각하지 않는 것이 좋다.

더불어, 대부분의 방법들이 문자에 해당하는 숫자를 랜덤하게 뽑은 뒤 문자열로 변환하고 이를 모아서 출력해주는 형태를 가지고 있기 때문에, 
**TPS(Transaction Per Second)가 매우 높은 경우 성능에 영향을 줄 수 있다는 것**을 알고 있어야 한다.

만약 랜덤하게 생성한 것이 ID처럼 쓰이고, 앞뒤로 날짜나 시간 등을 붙여줄 수 있는 경우라면 간단히 현재시간의 밀리세컨드 값이나, 
timestamp의 마지막 3자리를 뽑아쓰는 등으로 가볍게 처리하기도 한다.

## Reference

https://linkeverything.github.io/java/java-random-string/