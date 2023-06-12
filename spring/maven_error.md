# maven 에서 발생하는 에러 기록




### omitted for conflict with x.x.x version

해당 라이브러리를 컴파일할 때 요구되는 라이브러리 버전이 있으나, 해당 프로젝트 또는 상위 프로젝트에서 대상이되는 라이브러리 버전을 직접 의존성(버전을 직접 명시) 을 추가한 경우 발생하는 충돌. 
이 때는 직접 명시한 버전을 사용하게 된다.


### java.util.ZipException: invalid LOC header (bad signature)

Maven 은 레파지토리 서버에서 필요한 라이브러리(*.jar)들을 가져와 관리해주는데, 간혹 깨진 라이브러리도 가져온다.
프로젝트가 클수록 깨진 라이브러리 파일을 찾는 것은 많은 시간이 소요되기 때문에 편의상 repository(디폴트 : .m2) 를 전부 날리고,
maven install or update project 를 하면 정상적으로 실행된다.