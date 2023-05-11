LC MAY 10

# Redis


## redis-cli 명령어

- 

```shell
# 접속
# localhost:6379 로 접속
redis-cli

# {호스트명}:{포트명} 으로 접속
redis-cli -h {호스트명} -p {포트명}
```

redis-cli 에 접속해있다면 아래부터는 redis-cli 생략이 가능하다.

```shell
## 정보 조회
redis-cli info

## 모니터링
redis-cli monitor

## 모든 key 검색
redis-cli keys *

## key 의 잔류 시간 확인
redis-cli ttl {키}

## key 에 맞는 value 조회
redis-cli get {키}

## key 존재 여부 확인
redis-cli exists {키}

## key 의 타입 조회
redis-cli type {키}

## 모든 key 삭제
redis-cli FLUSHALL
```


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

https://freeblogger.tistory.com/10

https://m.blog.naver.com/newwodudrj/221376339003