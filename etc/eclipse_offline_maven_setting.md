# 오프라인의 경우 Eclipse 에서 Maven 세팅

다음은 폐쇄망 사용 등의 이유로 오프라인에서 Maven 의 환경을 설정하는 방법이다.

**기본적으로 온라인 환경에서 최종 버전으로 메이븐 업데이트가 되어 있다는 것을 전제로 한다.**
아래에 나올 세팅 방법은 이미 프로젝트에서 사용하는 라이브러리들은 모두 다운로드 받아져있다고 가정하고,
해당 라이브러리들을 참조하는 경로만 정의하는 것이다.

### 1. Setting.xml 설정

다음은 ```settings.xml``` 의 기본 내용이다. 추가 속성값이 필요없다면, 다음 ```<localRepository>``` 속성값에 repository 경로만 정의해준다.

```settings.xml```

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

<!-- 레파지토리 디렉토리 경로 -->
<localRepository>S:\maven\repository</localRepository> 
<interactiveMode>true</interactiveMode>

<offline>false</offline>

</settings>
```


### 2. Eclipse 설정

1. ```Window - Preferences``` 접속한다.
2. 검색 창에 maven 을 검색하고 Maven 을 클릭하면 다음과 같은 화면이 나온다.

<img src="https://user-images.githubusercontent.com/49870384/173524692-d0ac5ab7-be91-4b69-a4f2-ae6861f195a7.PNG">

3. Offline 체크박스를 체크한다.
4. Maven > User Settings 를 클릭한다.

<img src="https://user-images.githubusercontent.com/49870384/173524800-e5c2297a-1dc7-4406-836e-ce3078e2d282.PNG">

5. 위에서 작성한 ```settings.xml``` 파일을 찾아 선택한다.
6. [Apply and Close] 을 누르면 끝난다.
7. 메이븐 업데이트를 진행한다.

