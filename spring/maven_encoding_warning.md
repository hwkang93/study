# [WARNING] Using platform encoding (MS949 actually) to copy filtered resources, i.e. build is platform dependent!

```
[WARNING] Using platform encoding (MS949 actually) to copy filtered resources, i.e. build is platform dependent!
```


## 해결 방법

1. pom.xml 에 다음 프로퍼티를 추가한다.

```
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

2. Eclipse IDE 를 사용하고 있는 경우 eclipse.ini 파일에 다음 프로퍼티를 추가한다.

```
-Dfile.encoding=UTF-8
```
