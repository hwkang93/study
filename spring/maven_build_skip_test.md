# Maven 실행 시에 테스트 생략하는 방법

메이븐 패키징 작업을 수행하면, 프로젝트의 테스트도 함께 실행된다.
테스트 실패 시 정상적으로 패키징이 되지 않는데, pom.xml 에 옵션을 추가해 패키징 작업에 테스트를 제외할 수 있다.


### pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"   
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  ...중략...
  <properties>
    <maven.test.skip>true</maven.test.skip>
  </properties>
   ...중략...   
</project>
```