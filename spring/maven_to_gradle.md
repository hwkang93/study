# maven pom.xml 에 정의된 의존성을 gradle 의존성으로 바꾸기


### maven 형태

```xml
<repository>
	<id>GroupDocsJavaAPI</id>
	<name>GroupDocs Java API</name>
	<url>http://repository.groupdocs.com/repo/</url>
</repository>
<dependency>
        <groupId>com.groupdocs</groupId>
        <artifactId>groupdocs-metadata</artifactId>
        <version>21.4</version> 
</dependency>
```

### gradle 형태

```
repositories {
    
    ...
    
    maven { url "https://repository.groupdocs.com/repo/" }
}

dependencies {
    
    ...
    
    implementation group: 'com.groupdocs', name: 'groupdocs-metadata', version: 21.4
}
```
