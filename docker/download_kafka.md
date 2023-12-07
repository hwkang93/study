# Window Docker 에 Kafka 설치하기

> Window 에 도커가 깔려 있고,(PowerShell 에서 docker 명령어가 유효하고)   
> Git 이 깔려 있음을 전제로 한다.

## Kafka Git Clone

PowerShell 을 실행시키고 Kafka 프로젝트를 다운로드를 받을 경로로 이동한다.  
그 후 아래 명령어를 입력하면 해당 디렉토리에 kafka-docker 가 다운로드 받아진다.

```$ git clone https://github.com/wurstmeister/kafka-docker ```


## Property 수정

kafka-docker 디렉토리 안에 있는 docker-compose-single-broker.yml 파일을 수정한다

KAFKA_ADVERTISED_HOST_NAME 값을 **127.0.0.1** 로 변경한다(localhost)

## Kafka 실행

```
cd kafka-docker
docker-compose -f docker-compose-single-broker.yml up
```

```
failed to solve: process "/bin/sh -c set -eux ;     apt-get update ;     apt-get upgrade -y ;     
apt-get install -y --no-install-recommends jq net-tools curl wget ;     
apt-get install -y --no-install-recommends gnupg lsb-release ; 
\tcurl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg ; 
\techo   \t\t\"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] 
https://download.docker.com/linux/debian   \t\t$(lsb_release -cs) stable\" | tee /etc/apt/sources.list.d/docker.list > 
/dev/null ;     apt-get update ;     apt-get install -y --no-install-recommends docker-ce-cli ;     apt remove -y gnupg 
lsb-release ;     apt clean ;     apt autoremove -y ;     apt -f install ;     apt-get install -y --no-install-recommends netcat ;
chmod a+x /tmp2/*.sh ;     mv /tmp2/start-kafka.sh /tmp2/broker-list.sh /tmp2/create-topics.sh /tmp2/versions.sh /usr/bin ;
sync ;     /tmp2/download-kafka.sh ;     tar xfz /tmp2/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz -C /opt ; 
rm /tmp2/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz ;     ln -s /opt/kafka_${SCALA_VERSION}-${KAFKA_VERSION} ${KAFKA_HOME} ;
rm -rf /tmp2 ;     rm -rf /var/lib/apt/lists/*" did not complete successfully: exit code: 2
```

위 오류가 발생했다면 다음 명령어를 실행한다

git config --global core.autocrlf false


## Reference

https://ppaksang.tistory.com/25