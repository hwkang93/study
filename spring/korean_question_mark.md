# Spring + Maven 환경에서 한글이 깨져 나올 때

### 1. application.properties 파일에 속성 추가

```properties
server.servlet.encoding.force-response=true
```

## 서브 프로젝트에서 한글이 깨지는 경우

메인 프로젝트가 아닌 서브 프로젝트에서 한글이 깨지는 경우 pom.xml 을 수정해볼 수 있다.

```xml
<project>

    <!-- 생략 -->
    
    <properties>
        <!-- 소스 속성 파일 등의 인코딩 설정 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!-- 보고서 등의 출력 자원의 인코딩 설정 -->
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>
    
    <!-- 생략 -->
</project>>
```