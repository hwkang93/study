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