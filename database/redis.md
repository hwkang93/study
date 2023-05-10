# Redis

## 오류

#### WRONGTYPE Operation against a key holding the wrong kind of value

레디스는 기본적으로 5가지 타입을 제공하고 있으며 데이터베이스에 한번 타입이 결정된 상태에서 해당 타입과 상관없는 명령을 수행하려고 할때 위와 같은 에러 메시지가 출력된다.   
예를 들어 hget 으로 조회해야 하는데 get 으로 조회하는 경우 위 에러가 발생한다.

다음은 각 상황에 맞는 조회 명령어이다.

- if value is of type string -> GET <key>
- if value is of type hash -> HGET or HMGET or HGETALL <key>
- if value is of type lists -> lrange <key> <start> <end>
- if value is of type sets -> smembers <key>
- if value is of type sorted sets -> ZRANGEBYSCORE <key> <min> <max>
- if value is of type stream -> xread count <count> streams <key> <ID>.


## Reference

https://m.blog.naver.com/newwodudrj/221376339003