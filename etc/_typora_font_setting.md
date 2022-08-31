# Typora 마크다운 에디터에서 폰트 변경하는 방법

기본 베이스는 **GitHub** 테마로 하되 폰트만 변경하는 방법에 대해 정리해보겠다.

1. Typora 에디터에서 [파일] - [환경설정] 탭으로 접속한다. (단축키 : CTRL + ,)
2. 모양 탭에서 [테마] > [테마 폴더 열기] 를 클릭한다.
3. 

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

폰트를 바꾸고 보니, IntelliJ, 메모장 등에서 사용하는 JetBrainsMono 글씨체와는 한글에서 괴리감이 있어 다른 글씨체를 찾아보고 있다.
