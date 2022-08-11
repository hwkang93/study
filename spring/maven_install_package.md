# Maven Install 을 위한 패키지 pom.xml 예제

Maven 프로젝트 진행 시 멀티 모듈로 프로젝트를 관리하고자 할 때, 공통 모듈(common 등)을 먼저 빌드하고 개별 모듈들을 빌드해야 한다.

이와 같은 상황에 빌드만을 목적으로 하는 프로젝트를 만들 수 있는데 해당 프로젝트에는 소스코드 없이 pom.xml 만 구현하면 된다.

다음은 pom.xml 예시이다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 ./src/main/resources/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
		
	<properties>
		<java.version>1.8</java.version>
		<geopaas.version>1.0</geopaas.version>
	</properties>

	<groupId>my.project</groupId>
	<artifactId>master</artifactId>
	<version>${project.version}</version>
	<packaging>pom</packaging>

	<modules>
		<module>../common</module>
		
		<module>../projectA</module>
		<module>../projectB</module>
	</modules>
	
</project>
```