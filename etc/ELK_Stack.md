# Mac에 ELK Stack 구성하기

- ELK 3개의 버전이 동일해야 된다

### 1. Homebrew 설치 or 업데이트

### 2. Java 8 설치, 환경변수 변경

[Logstash 설치](https://www.elastic.co/guide/kr/logstash/current/installing-logstash.html)

Logstash는 Java9를 지원하지 않기 때문에 Java8 설치 후 ```JAVA_HOME``` 환경변수를 변경해야 한다

**JAVA_HOME 환경변수 변경**

파일 열기

```bash
vi ~/.bash_profile
```

파일 내용 변경

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_설치한버전.jdk/Contents/Home
```

환경변수 다시 로드
```bash
source ~/.bash_profile
```

### 3. Elasticsearch 설치, 시작

**설치**
```bash
brew install elasticsearch && brew info elasticsearch
```

**시작**
```bash
brew services start elasticsearch
```

**확인**
```bash
curl -X GET localhost:9200
```
또는 브라우저로 `http://localshot:9200` 에 접속해 아래와 같은 내용이 나오는지 확인
```json
{
    "name": "XXXXXX",
    "cluster_name": "XXXXX",
    ...
}
```

### 4. Logstash 설치, 시작

**설치**
```bash
brew install logstash
```

**시작**
```bash
brew services start logstash
```

### 5. Kibana 설치, 설정, 시작

**설치**
```bash
brew install kibana
```

**설정**

설정 파일 열기
```bash
sudo vi /usr/local/etc/kibana/kibana.yml
```

파일 내용 변경
- `server.port`, `elasticsearch.hosts` 라인의 주석 해제
- `server.host` 라인의 주석 해제, `server.hosts:"0.0.0.0"`으로 변경

**시작**
```bash
brew services start kibana
```

**확인**

브라우저로 `http://localhost:5601/status` 에 접속해 아래와 같은 내용이 보여지는지 확인

<p align="center">
    <img src="../image/kibana_status.png"  width="1000" height="auto">
</p>

<br/>

---

<br/>

출처 및 참고
- [Getting Started with Logstash](https://www.elastic.co/guide/en/logstash/7.10/first-event.html)
- 엘라스틱 스택 개발부터 운영까지(김준영, 정상운)
