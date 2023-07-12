# heap 메모리 최소,최대값 조회하는 예제 소스코드

```java
public static void main(String[] args) {
    int mb = 1024 * 1024;
    MemoryMXBean memoryBean = ManagementFactory.getMemoryMXBean();
    long xmx = memoryBean.getHeapMemoryUsage().getMax() / mb;
    long xms = memoryBean.getHeapMemoryUsage().getInit() / mb;
    logger.info("Initial Memory (xms) : " + xms + " mb");
    logger.info("Max Memory (xmx) : " + xmx + " mb");
}
```

```java
public class ResourceInfo {

    private final int cpus;
    private final String totalMemory;
    private final String maxMemory;
    private final String javaVersion;
    private final String freeMemory;

    public ResourceInfo() {
        //cpu 개수
        this.cpus = Runtime.getRuntime().availableProcessors();

        //JVM 사용하려고 시도한 최대 메모리양 (xmx)
        this.maxMemory = toMb(Runtime.getRuntime().maxMemory()) + "MB";

        //현재 애플리케이션에 할당된 총 힙 메모리 양. (새로운 객체에 쓰일 메모리 영역 포함)
        //JVM 의 모든 메모리양 
        this.totalMemory = toMb(Runtime.getRuntime().totalMemory()) + "MB";

        //JVM 에 할당된 힙 메모리 중 사용가능한 크기
        this.freeMemory = toMb(Runtime.getRuntime().freeMemory()) + "MB";

        //VM 버전
        this.javaVersion = ManagementFactory.getRuntimeMXBean().getVmVersion();
    }

    private static long toMb(long memorySize) {
        return memorySize / (1000 * 1000);
    }

    public int getCpus() {
        return cpus;
    }

    public String getTotalMemory() {
        return totalMemory;
    }

    public String getMaxMemory() {
        return maxMemory;
    }

    public String getJavaVersion() {
        return javaVersion;
    }

    public String getFreeMemory() {
        return freeMemory;
    }
}
```

그림으로 나타내면 다음과 같다

![Runtime getMemory](https://github.com/hwkang93/study/assets/49870384/a0555c49-5218-4b0d-a273-bdc74e728575)

## Reference

https://pub-diary.tistory.com/17

https://ivvve.github.io/2018/12/02/java/ETC/runtime_memory/