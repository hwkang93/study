# Typora 마크다운 에디터에서 폰트 변경하는 방법

기본 베이스는 **GitHub** 테마로 하되 폰트만 변경하는 방법에 대해 정리해보겠다.

1. 먼저 적용하고자 하는 폰트를 특정 위치에 저장한다. 아래에서 만들 css 파일에 폰트 위치를 명시해야 하기 때문에 테마 폴더 근처에 저장하는 걸 추천한다.
2. Typora 에디터에서 [파일] - [환경설정] 탭으로 접속한다. (단축키 : CTRL + ,)
3. 모양 탭에서 [테마] > [테마 폴더 열기] 를 클릭한다.
4. ```github.user.css``` 파일을 생성한다. 아래는 다운로드받은 JetBrainsMono 폰트를 적용하는 예제 소스코드이다.

```github.user.css```

```css
@font-face {
font-family: 'JetBrainsMono';
font-style: normal;
font-weight: normal;
src: local('JetBrainsMono-Medium'),url('./github/JetBrainsMono-Medium.ttf') format('ttf')  
}
@font-face {
font-family: 'JetBrainsMono';
font-style: italic;
font-weight: normal;
src: local('JetBrainsMono-MediumItalic'),url('./github/JetBrainsMono-MediumItalic.ttf') format('ttf')
}

@font-face {
font-family: 'JetBrainsMono';
font-style: normal;
font-weight: bold;
src: local('JetBrainsMono-Bold'),url('./github/JetBrainsMono-Bold.ttf') format('ttf')
}
@font-face {
font-family: 'JetBrainsMono';
font-style: italic;
font-weight: bold;
src: local('JetBrainsMono-BoldItalic'),url('./github/JetBrainsMono-BoldItalic.ttf') format('ttf')
}
```

위의 소스코드에서

```
src: local('JetBrainsMono-BoldItalic'),url('./github/JetBrainsMono-BoldItalic.ttf') format('ttf')
```

이 부분만 따로 살펴볼 필요가 있다.

- ```local``` : 폰트파일의 명칭을 명시한다.
- ```url``` : 폰트가 저장된 경로를 명시한다. 위의 경우, 폰트 저장 경로가 css 파일이 있는 위치에서 github 폴더 내에 JetBrainsMono-BoldItalic.ttf 이라는 명칭으로 저장되어 있음을 뜻한다.
- ```format``` : 폰트의 확장자를 명시한다.

폰트를 다운로드 받지 않고, URL 로 직접 요청하는 경우에는 다음과 같이 작성한다. (나눔바른펜 폰트이다.)

```
src:url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_two@1.0/NanumBarunpen.woff')format('woff');
```

5. ```github.css``` 파일에 새로 만든 폰트를 추가한다. (JetBrainsMono)

```github.css```

```css
/* 생략 */

body {
    font-family: "JetBrainsMono", "Open Sans","Clear Sans", "Helvetica Neue", Helvetica, Arial, 'Segoe UI Emoji', sans-serif;
    color: rgb(51, 51, 51);
    line-height: 1.6;
}

/* 생략 */

```
6. Typora 를 종료 후 재시작한다.

### 여담

폰트를 바꾸고 보니, IntelliJ, 메모장 등에서 사용하는 JetBrainsMono 글씨체와는 한글에서 괴리감이 있어 다른 글씨체를 찾아보고 있다. 에휴 ㅜㅜ
