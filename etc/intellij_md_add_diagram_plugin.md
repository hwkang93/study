# IntelliJ 마크다운 문법에서 다이어그램 사용하기

1. ```File``` > ```Settings``` (```Ctrl``` + ```Alt``` + ```S```) 을 통해 설정 팝업을 연다.
2. Languages & Frameworks > Markdown 을 클릭한다.

<img src="https://user-images.githubusercontent.com/49870384/178197347-c9f43c8c-72e0-4d2c-9ad8-d9df08bc8351.PNG" width="80%">

3. Markdown extensions 탭의 ```PlantUML``` 과 ```Mermaid``` 을 체크하고 [OK] 버튼을 클릭한다.
4. 문법을 작성하고 결과물이 정상적으로 표출되는지 확인한다.

```
//예시

　```mermaid
    graph TD;
        A-->B;
        A-->C;
        B-->D;
        C-->D;
　```
```

```mermaid
    graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

## Reference

https://www.jetbrains.com/help/idea/markdown.html#diagrams