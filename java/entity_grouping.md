# 객체 Grouping 하기

데이터 리스트를 그룹핑 하는 예제이다.
```lombok``` 라이브러리를 추가로 사용하였으며, Stream API 사용을 위해 Java 1.8 이상의 버전을 사용했다.

- **UseAt.java**

```java
public enum UseAt {
    Y , N;
}
```

- **User.java**

```java
@Getter
public class User {
    private long userId;
    private String userName;
    private int age;

    @Builder
    public User(long userId, String userName, int age) {
        this.userId = userId;
        this.userName = userName;
        this.age = age;
    }
}
```

- **UserResponse.java**

```java
@Getter
@AllArgsConstructor
public class UserResponse {
    private long userId;
    private String userName;
    private int age;
    private String groupId;
    private String groupName;
    private UseAt groupUseAt;

    public User toUser() {
        return User.builder()
                .userId(this.userId)
                .userName(this.userName)
                .age(this.age)
                .build();
    }
}
```


- **UserGroup.java**

```java
@Getter @ToString
@EqualsAndHashCode(of = "groupId")
public class UserGroup {
    private String groupId;
    private String groupName;
    private UseAt useAt;

    private List<User> userList;

    @Builder
    public UserGroup(String groupId, String groupName, UseAt useAt) {
        this.groupId = groupId;
        this.groupName = groupName;
        this.useAt = useAt;
    }

    public UserGroup(UserResponse userResponse) {
        this.groupId = userResponse.getGroupId();
        this.groupName = userResponse.getGroupName();
        this.useAt = userResponse.getGroupUseAt();
    }

    public void addUser(List<User> userList) {
        if(this.userList == null) {
            this.userList = new ArrayList<>();
        }

        this.userList.addAll(userList);
    }
}
```

```@EqualsAndHashCode``` 어노테이션을 사용하여(Lombok 라이브러리에서 제공하는 어노테이션)
어떤 두 개의 ```UserGroup``` 객체의 ```groupId``` 의 값이 같다면 두 ```UserGroup``` 객체는 같은 객체로 판단한다.

여기서 중요한 게 ```@EqualsAndHashCode``` 옆에 붙어있는 id 값인데, 만약 id 값이 없다면 모든 컬럼을 대상으로 객체의 동일성을 판단하기 때문에, id 를 반드시 명시해주는 것이 좋다.

추가로 변경가능한 객체에 ```@EqualsAndHashCode``` 어노테이션 사용은 지양하도록 하자.


- **GroupingTest.java**

```java
@SpringBootTest
class GroupingTest {

	static List<UserResponse> userResponseList;

	@BeforeAll
	static void beforeAll() {
		userResponseList = Arrays.asList(
				new UserResponse(1L, "사용자1", 10, "group1", "그룹1", UseAt.Y),
				new UserResponse(2L, "사용자2", 10, "group1", "그룹1", UseAt.Y),
				new UserResponse(3L, "사용자3", 10, "group1", "그룹1", UseAt.Y),
				new UserResponse(4L, "사용자4", 10, "group1", "그룹1", UseAt.Y),
				new UserResponse(5L, "사용자5", 10, "group2", "그룹2", UseAt.Y),
				new UserResponse(6L, "사용자6", 10, "group2", "그룹2", UseAt.Y),
				new UserResponse(7L, "사용자7", 10, "group2", "그룹2", UseAt.Y),
				new UserResponse(8L, "사용자8", 10, "group2", "그룹2", UseAt.Y),
				new UserResponse(9L, "사용자9", 10, "group2", "그룹2", UseAt.Y),
				new UserResponse(10L, "사용자10", 10, "group3", "그룹3", UseAt.Y),
				new UserResponse(11L, "사용자11", 10, "group3", "그룹3", UseAt.Y),
				new UserResponse(12L, "사용자12", 10, "group3", "그룹3", UseAt.Y),
				new UserResponse(13L, "사용자13", 10, "group3", "그룹3", UseAt.Y),
				new UserResponse(14L, "사용자14", 10, "group4", "그룹4", UseAt.Y),
				new UserResponse(15L, "사용자15", 10, "group4", "그룹4", UseAt.Y)
		);
	}

	@Test
	void groupingUserResponse() {
		List<UserGroup> userGroupsList = userResponseList.stream()
				.collect(Collectors.groupingBy(UserGroup::new))
				.entrySet().stream()
				.map( obj -> {
					UserGroup group = obj.getKey();
					List<User> userList = obj.getValue().stream()
							.map(UserResponse::toUser)
							.collect(Collectors.toList());

					group.addUser(userList);

					return group;
                })
                .collect(Collectors.toList());

		//group1 -> UserList.size() = 4
		UserGroup group1 = userGroupsList.stream()
				.filter(userGroup -> userGroup.getGroupId().equals("group1"))
				.findFirst()
				.get();

		Assertions.assertThat(group1.getUserList().size()).isEqualTo(4);

		//group2 -> UserList.size() = 5
		UserGroup group2 = userGroupsList.stream()
				.filter(userGroup -> userGroup.getGroupId().equals("group2"))
				.findFirst()
				.get();

		Assertions.assertThat(group2.getUserList().size()).isEqualTo(5);

		//group3 -> UserList.size() = 4
		UserGroup group3 = userGroupsList.stream()
				.filter(userGroup -> userGroup.getGroupId().equals("group3"))
				.findFirst()
				.get();

		Assertions.assertThat(group3.getUserList().size()).isEqualTo(4);

		//group4 -> UserList.size() = 2
		UserGroup group4 = userGroupsList.stream()
				.filter(userGroup -> userGroup.getGroupId().equals("group4"))
				.findFirst()
				.get();

		Assertions.assertThat(group4.getUserList().size()).isEqualTo(2);

	}
}
```

메인로직을 간단히 설명하자면, 
```
userResponseList.stream()
    .collect(Collectors.groupingBy(UserGroup::new))
```

먼저 ```userResponseList``` 를 ```Map<UserGroup, List<UserResponse>>``` 의 형태로 그룹핑한다.

<br/>

```
.entrySet().stream()
```

그리고 Key 값에 대해 Stream 을 다시 연다.

<br/>

```
.map( obj -> {
    UserGroup group = obj.getKey();
    List<User> userList = obj.getValue().stream()
            .map(UserResponse::toUser)
            .collect(Collectors.toList());

    group.addUser(userList);

    return group;
})
```

여기서 ```obj``` 는 ```Key = UserGroup , value = List<UserResponse>``` 의 형태를 가진 객체로,

```obj``` 의 ```value``` 즉, ```UserResponse``` 객체를 ```User``` 객체로 변경한다.(```toUser()```)

그리고 ```UserGroup``` 객체에 ```userList``` 를 추가하고 리턴한다.

<br/>

```
.collect(Collectors.toList());
```

마지막으로 결과값을 ```List``` 형태로 리턴받는다.



[참고 블로그](https://kwonnam.pe.kr/wiki/java/lombok/pitfall)