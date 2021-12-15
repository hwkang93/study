# 객체 배열 정렬하기

## 들어가며

어떤 객체가 배열 형태로 메모리에 할당되어 있고, 해당 배열을 정렬해야 하는데 정렬 기준이 가, 나, 다 혹은 a, b, c 순서가 아닐 수도 있다.

개발자가 특정 정렬 순서를 정해 놓고 그 순서에 맞게 정렬하는 방법 중 하나를 기록하려 한다.

모든 소스코드가 그렇겠지만, 이 예제 역시 정답은 아니고 그렇다고 오답도 아닌 여러 길 중 하나일 뿐임을 다시 한번 되짚으면서 시작하자.
(물론 이 때 당시 내 최선이긴 했음)

## 목표

게시판 내에 있는 각각의 메뉴를 정렬해야 한다. 카테고리는 DB 에 다음과 같이 저장 돼있다.

```
ID  CATEGORY
1   공지사항
2   오류신고
3   자료실
4   자료요청
5   질의응답
```

그리고, 다음과 같은 순서로 출력하려고 한다.

```오류신고 - 자료실 - 질의응답 - 자료요청 - 공지사항```

## 구현 방법

정렬 기준으로 잡을 열거형을 하나 만들고, 정렬을 위해 ```Comparable``` 인터페이스를 사용했다. 

먼저 정렬 기준이 될 열거형(Enum) ```MenuSortOrder``` 을 생성한다.

```java
@AllArgsConstructor
@Getter
public enum MenuSortOrder {
    //공지사항
    NOTICE("1", 5),
    //오류신고
    ERROR_REPORT("2", 1),
    //자료실
    REFERENCE("3", 2),
    //자료요청
    REFERENCE_REQUEST("4", 4),
    //질의응답
    QNA("5", 3);

    private String menuId;
    private int order;

    public static MenuSortOrder findBy(String menuId) {
        return Stream.of(MenuSortOrder.values())
                .filter(menu -> menu.getMenuId().equals(menuId))
                .findFirst()
                .orElseThrow(() -> {
                    throw new IllegalArgumentException("정의되지 않은 메뉴입니다. [ " + menuId + " ] ");
                });
    }

    public static int compare(String menuId, String compareMenuId) {
        return findBy(menuId).getOrder() - findBy(compareMenuId).getOrder();
    }
}
```

간단하게 열거형에 대한 설명을 하겠다.

```MenuSortOrder``` 은
1. 먼저 각각의 메뉴의 ID 값과 정렬 순서 필드로 이루어져 있다.
2. ```findBy``` 메소드를 통해 메뉴 ID 로 메뉴값을 찾고
3. 그 값의 순번(```order```)을 기준으로 오름차순으로 정렬되도록 구현되어 있다. (```compare()```)

> 내림차순으로 구현하고 싶으면 ```compare``` 메소드 구현 부분을
> ```findBy(compareMenuId).getOrder() - findBy(menuId).getOrder()``` 로 순서만 바꾸면 된다.

다음은 ```Menu``` 클래스를 만든다.

```java
@AllArgsConstuctor
@Getter @ToString
public class Menu implements Comparable<Menu> {
    private String menuId;
    private String menuNm;
    
    @Override
    public int compareTo(Menu o) {
        return MenuSortOrder.compare(menuId, o.getMenuId());
    }
}
```

```Menu``` 클래스는 ```Comparable<T>``` 인터페이스의 구현체이다.

여기서 T 는 비교 대상 클래스를 의미하는데, 이번 예제의 경우 다른 메뉴와의 비교를 원하기 때문에 ```Menu``` 클래스로 작성했다.

```Compare<T>``` 인터페이스를 구현하려면 ```compareTo(T o)``` 메소드를 정의해야 하며 구현 부분에
```MenuSortOrder``` 열거형에서 만든 ```compare``` 메소드를 사용하면 된다.

## 테스트

간단한 테스트 후 마치도록 하겠다.

```java
public class SortTest {
    
    @Test
    @DisplayName("정렬 테스트")
    void sortTest() {
        List<Menu> menuList = Arrays.asList(
        	new Menu("M001", "공지사항"), 
                new Menu("M002", "오류신고"), 
                new Menu("M003", "자료실"), 
                new Menu("M004", "자료요청"), 
                new Menu("M005", "질의응답")
        );
        
        Collections.sort(menuList);
        
        Assertions.assertThat(menuList.get(0).getMenuId()).isEqualTo("M002");
        Assertions.assertThat(menuList.get(1).getMenuId()).isEqualTo("M003");
        Assertions.assertThat(menuList.get(2).getMenuId()).isEqualTo("M005");
        Assertions.assertThat(menuList.get(3).getMenuId()).isEqualTo("M004");
        Assertions.assertThat(menuList.get(4).getMenuId()).isEqualTo("M001");
    }
}
```

여기서 중요한 건 ```Collections.sort(menuList)``` 메소드를 호출해야 정렬이 진행된다는 것이다.

> Collections.sort() 의 시간복잡도는 O(n log(n)) 이다. (최악의 경우에도 그렇다고 한다.)
> 
> 참고로 Arrays.sort() 의 평균 시간복잡도는 O(n log(n)) 이며 최악의 경우 O(n^2) 의 시간복잡도를 가질 수 있다. 