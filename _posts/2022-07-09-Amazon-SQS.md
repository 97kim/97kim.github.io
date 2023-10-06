---
layout: post
title: Amazon SQS
subtitle: SQS 찍먹
author: 97kim
categories: AWS
banner:
  video:
  loop:
  volume:
  start_at:
  image: /assets/images/sqs/sqs_bg.png
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 2.5em; font-weight: bold;"
  subheading_style: "color: gold"
tags: AWS SQS
sidebar: []
---

## AWS SQS

**SQS**는 **`Simple Queue Service`**의 약자다.  
간단한 메세지를 큐 형태로 쌓아서(send) 받는(recieve) 서비스다.  
이름에서 알 수 있듯 **간단**하게!

**`pub/sub`** 형태로 메세징 처리를 하고 싶다면 **`rabbitMQ`**나 **`JMS`**, **`Amazon MQ`**를 사용해야 한다.

### 큐가 필요한 이유

- **큐 사용하지 않은 예시**

![sqs1](/assets/images/sqs/sqs1.png)

1. 사용자가 주문을 한다.
2. 서버는 해당 요청에 대한 데이터를 데이터베이스에 추가한다.
3. 주문한 사용자에 대해 주문이 완료되었다는 메일을 발송한다.
4. 이때 한 명씩 모두에게 이메일을 발송하는 데 2시간이 소요된다고 하자.

> 사용자는 2시간 동안 기다려야 되는데 좋은 서비스인가?

- **큐 사용 예시**

![sqs2](/assets/images/sqs/sqs2.png)

1. 사용자가 주문을 한다.
2. 서버는 해당 요청에 대한 데이터를 데이터베이스에 추가한다.
3. 사용자가 주문을 했다는 사실을 메시지로 만들어 큐에 저장한다.
4. 사용자는 주문을 하고 그 데이터가 데이터베이스에 추가되는 시간만 기다린다.

> 사용자는 한 명씩 모두에게 이메일을 발송하는 시간을 기다리지 않아도 된다.

- SQS를 사용하면 흐름은 아래와 같다.

![sqs3](/assets/images/sqs/sqs3.png)

**참고 영상**

<iframe width="560" height="315" src="https://www.youtube.com/embed/D445NSU2Ra0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

![sqs4](/assets/images/sqs/sqs4.png)

대기열(큐) 생성 시 나오는 화면이다.

### **표준 (Standard Queue)**

- 초당 무제한에 가까운 TPS를 지원한다. (**`Unlimited Throughput`**)
- 최소 1회 전달이 보장된다. 단, 중복 수신이 될 수 있다. (**`At-Least-Once Delivery`**)
- 100% 순서가 보장되지 않지만 최대한 순서를 보장하고자 노력한다. (**`Best-Effort-Ordering`**)

### **FIFO (First In First Out Queue)**

- 초당 300TPS 제한이 존재한다. (**`High Throughput`**)
- 메시지 순서를 보장한다. (**`First-In-First-Out Delivery`**)
- 1번의 전송, 1번의 수신이 지켜진다. 중복수신이 방지 된다. (**`Exactly-Once Processing`**)

---

![sqs5](/assets/images/sqs/sqs5.png)

AWS SQS 요금이다. FIFO가 표준보다 더 비싸다.  
하지만 첫 100만 번의 요청까지 무료라고 한다.

![sqs6](/assets/images/sqs/sqs6.png)

- **표시 제한 시간**
  - 수신한 메시지는 해당 시간동안 다른 서버에게 보이지 않는다.
  - 서로 다른 서버가 같은 수신 메시지를 가지고 작업을 할 수 있기 때문에 중복 작업을 방지하기 위해 표시 제한 시간을 설정한다.
- **메시지 보존 기간**
  - 메시지 보존 기간이 더 길면 메시지의 생산(produce)과 소비(consume) 간의 간격도 좀 더 길게 정할 수 있는 유연성이 제공된다.
  - Amazon SQS 메시지 보존 기간은 1분에서 14일 사이의 값으로 구성할 수 있다. 기본 설정은 4일이다. 메시지 보존 할당량에 도달하면 메시지가 자동으로 삭제된다.
- **전송 지연**
  - 큐에 추가된 각 메시지의 첫 번째 전송에 대한 지연 시간이다. 일부러 첫 번째 전송을 늦추고 싶은 경우 전송 지연을 설정할 수 있다.
- **최대 메시지 크기**
  - 최대 메시지 크기를 구성하려면 콘솔이나 **`SetQueueAttributes`** 메서드를 사용하여 **`MaximumMessageSize`** 속성을 설정한다. 이 속성은 Amazon SQS 메시지가 포함할 수 있는 바이트 수를 지정한다. 이 특성을 1,024바이트(1KB)와 262,144바이트(256KB) 사이의 값으로 설정한다.
  - 256KB보다 큰 메시지를 전송하려면 [Java용 Amazon SQS 확장 클라이언트 라이브러리](https://github.com/awslabs/amazon-sqs-java-extended-client-lib)를 사용한다. 이 라이브러리를 사용하면 참조가 포함된 Amazon SQS 메시지를 최대 2GB까지 Amazon S3의 메시지 페이로드로 전송할 수 있다.
- **메시지 수신 대기 시간**
  - 폴링이 메시지를 사용할 수 있을 때까지 기다리는 최대 시간이다.

![sqs7](/assets/images/sqs/sqs7.png)

대기열(큐) 소유자만 전송, 수신하게 할 것인지에 대한 액세스 정책이다.

![sqs8](/assets/images/sqs/sqs8.png)

- **암호화**
  - 활성화하면 대기열(큐)에 들어오는 메시지들을 모두 암호화한다.
  - 권한 있는 소비자(consumer)에게 전송되는 경우에만 메시지가 해독된다.
  - 들어오는 메시지의 본문은 암호화하지만 대기열(큐)의 메타데이터, 메시지의 메타데이터, 대기열(큐)의 지표들은 암호화되지 않는다.
  - 활성화하면 **`AWS KMS(Key Management Service)`**를 이용해 대기열(큐) 메시지 내용을 보호한다.
- **배달 못한 편지 대기열**
  - **`DLQ (dead-letter-queue)`** 라고 한다.
  - 대기열(큐)의 메시지를 소비자(consumer)가 가져가서 처리하지 못하는 경우 그 횟수(receive_count)가 누적되는데, 횟수가 최대(max_receive_count)를 초과하는 경우 해당 메시지는 배달 못한 편지 대기열로 이동한다.
  - 메시지의 타임스탬프는 변경되지 않으며 배달 못한 편지 대기열의 보존 기간은 source 대기열(큐)의 보존 기간보다 길어야 한다.  
<br>

---

## SQS 메세지 보내기
**개발 환경**
- AWS SQS (Standard)
- Java 17
- Spring Boot 2.6.5
- Gradle 7.4.1

---

build.gradle
```groovy
dependencies {
    implementation group: 'org.springframework.cloud', name: 'spring-cloud-aws-messaging', version: '2.2.6.RELEASE'
    implementation group: 'org.springframework.cloud', name: 'spring-cloud-aws-autoconfigure', version: '2.2.6.RELEASE'
}
```

Spring Boot에서 AWS SQS 기능을 사용하려면 dependency를 추가해줘야 한다.

---

application.yml
```yml
cloud:
  aws:
    credentials:
      access-key: ${AWS_ACCESS_KEY}
      secret-key: ${AWS_SECRET_KEY}
    region:
      static: ${AWS_REGION}
    stack:
      auto: false

    # 여기 아래는 AWS 자격 증명 관련 내용은 아니다.
    sqs:
      queue:
        name: ${SQS_QUEUE_NAME}
```

AWS 자격 증명 관련 내용이다.  
민감한 정보는 환경 변수에 넣어 불러오게 하자.

IntelliJ를 사용한다면 **`Run`**의 **`Edit Configuration`**에서 **`Environment variables`**에서 환경변수 설정이 가능하다.

---

### Service

AmazonSQSSender.java
```java
public interface AmazonSQSSender {
    void sendMessage(MessageDto message);
}
```

---

AmazonSQSSenderImpl.java
```java
@Service
public class AmazonSQSSenderImpl implements AmazonSQSSender {

    private final QueueMessagingTemplate queueMessagingTemplate;

    @Value("${cloud.aws.sqs.queue.name}")
    private String queueName;

    public AmazonSQSSenderImpl(AmazonSQS amazonSQS) {
        this.queueMessagingTemplate = new QueueMessagingTemplate((AmazonSQSAsync) amazonSQS);
    }

    @Override
    public void sendMessage(MessageDto message) {
        Message<MessageDto> newMessage = MessageBuilder.withPayload(message).build();
        queueMessagingTemplate.send(queueName, newMessage);

        // SQS FIFO 이면 아래 코드 주석 풀기
        // Map<String, Object> headers = HashMap<>();
        // headers.put(SqsMessageHeaders.SQS_GROUP_ID_HEADER, "message-group-id") // 메세지 그룹 ID
        // headers.put(SqsMessageHeaders.SQS_DEDUPLICATION_ID_HEADER, "token") // 메세지 중복 제거 ID (선택 사항) - 메세지 중복 제거에  사용되는 토큰
        // Message<MessageDto> newMessage = MessageBuilder.withPayload(message).copyHeaders(headers).build();
        // queueMessagingTemplate.send(queueName, newMessage)
    }
}
```

---

### Controller

MessageAPI.java
```java
@RequiredArgsConstructor
@RestController
public class MessageAPI {

    private final AmazonSQSSender messageSender;

    @PostMapping("/api/v2/send")
    public String sendMessage(@RequestBody MessageDto message) {
        messageSender.sendMessage(message);
        return "success";
    }
}
```

---

### Dto

MessageDto.java

```java
public record MessageDto(MessageType type, String message) {
}
```

> record는 Java 14 버전부터 사용이 가능하다.

---

MessageType.java

```java
public enum MessageType {
    ORDER, CAMPAIGN
}
```

---

SQS 콘솔

![sqs9](/assets/images/sqs/sqs9.png)

### Postman

![sqs10](/assets/images/sqs/sqs10.png)

Postman으로 요청을 보냈고 success와 함께 200 OK!

---

![sqs11](/assets/images/sqs/sqs11.png)

요청에 성공하면 사용 가능한 메시지가 1 추가 된다.  
메시지 폴링을 눌러보자

---

![sqs12](/assets/images/sqs/sqs12.png)

아래 메시지에 하나가 떴다.

---

![sqs13](/assets/images/sqs/sqs13.png)

눌러보면 정상적으로 수신이 잘 된 것을 확인할 수 있다.  
수신한 메시지를 통해 그 다음 비즈니스 로직을 타게 한다면 SQS를 유용하게 사용할 수 있을 것만 같다!  

---

## SQS Lambda Trigger

> [Lambda](https://www.44bits.io/ko/keyword/aws-lambda)는 AWS에서 제공하는 서버리스 컴퓨팅 서비스다.
서버리스 컴퓨팅은 애플리케이션을 실행하기 위한 별도의 서버 설정 없이 곧바로 코드를 실행해주는 서비스를 의미하며, 고정 비용 없이 사용 시간에 대해서만 비용이 발생한다. 서버 설정 없이 사용할 수 있다고 하여 서버리스 컴퓨팅이라고 한다.

SQS 대기열(Queue)을 생성하면 기본적으로 Lambda 트리거는 없다.

![sqs14](/assets/images/sqs/sqs14.png)

Lambda 함수를 생성하고 연결시킨다면 수신하는 메시지에 대한 처리를 할 수 있을 것이다.  
일단 Lambda를 생성해보자.

![sqs15](/assets/images/sqs/sqs15.png)

블루프린트에서 SQS를 검색하면 SQS에서 메시지 폴링하는 것에 대한 샘플 코드가 있다. 이것을 기반으로 만들어보자.  
구성 버튼을 누르면 함수 이름, 역할 이름 등 정하는 부분이 있는데 편한대로 입력한다.

![sqs16](/assets/images/sqs/sqs16.png)

SQS 대기열(Queue)에는 우리가 만들었던 SQS 대기열(Queue)를 선택하고 생성한다.  
테스트 이벤트 구성은 기본적인 세팅으로 진행한다.

Lambda 함수를 만들고 들어가보면 아래와 같은 창이 보인다.
코드 소스에 우리의 비즈니스 로직을 넣으면 반영이 될 것이다.

샘플 코드는 아래와 같다.

```javascript
console.log('Loading function');

exports.handler = async (event) => {
    //console.log('Received event:', JSON.stringify(event, null, 2));
    for (const { messageId, body } of event.Records) {
        console.log('SQS message %s: %j', messageId, body);
    }
    return `Successfully processed ${event.Records.length} messages.`;
};
```

![](https://velog.velcdn.com/images/rudwnd33/post/5006534d-f2dc-478b-8a49-9ddeb135b05c/image.png)

Test라는 주황색 버튼은 테스트 이벤트 구성에서 설정한 형식의 JSON으로 요청이 오면 아래 코드가 실행되게 테스트 해볼 수 있는 버튼이다.

> 참고로 아래의 런타임 설정에서 Node.js 이외에도 다른 언어를 선택할 수 있다.
![](https://velog.velcdn.com/images/rudwnd33/post/7cb8df4f-5e0e-4d98-8eaf-cf6c71567920/image.png)

![](https://velog.velcdn.com/images/rudwnd33/post/e5d8ab85-bed6-425f-a570-5a15e379ac42/image.png)

테스트 버튼을 누르면 테스트 용도의 JSON 데이터를 잘 읽어오는 것을 확인할 수 있다.  
이제 저장을 하고 실제 우리가 요청 보내는 데이터로 테스트 해보자.

저장 후 Deploy 버튼을 클릭한다.

![](https://velog.velcdn.com/images/rudwnd33/post/587cb868-cca6-4397-8758-ffec49191497/image.png)

그리고 Lambda의 구성으로 가서 SQS 트리거를 활성화시킨다.

다시 Postman으로 요청을 보낸다.

![](https://velog.velcdn.com/images/rudwnd33/post/3a0de279-21fe-42bb-b36c-cd8ce8b0af0a/image.png)

요청에 성공했다.

로그를 확인해보자. AWS CloudWatch에서 로그를 확인할 수 있다.

![](https://velog.velcdn.com/images/rudwnd33/post/a16aa3f8-60c9-4399-87e1-9ce1073a6768/image.png)

Lambda 함수가 잘 실행되었다.

> Postman에서 api 호출 → 서버가 람다 호출  → 람다에서 응답 → AWS CloudWatch에서 확인 가능

SQS 대기열(Queue)에 메시지가 전송이 되어서 성공적으로 대기열에 쌓이면 Polling 과정을 Lambda가 해준다.

![](https://velog.velcdn.com/images/rudwnd33/post/097abd43-9603-4b24-8cdd-2405f556784e/image.png)

찍먹 끝!

![sqs_bg](/assets/images/sqs/sqs_bg.png)

<br>
> **참고**  
[AWS SQS 들이파기](https://lannstark.tistory.com/88)  
[Java 형식으로 AWS SQS에 메세지 보내기](https://mand2.github.io/til/send-messages-to-sqs-by-java/)  
[[AWS] spring-cloud-aws-messaging을 이용한 FIFO 유형의 AWS SQS 연동하기](https://uchupura.tistory.com/109)  
[SQS with Spring](https://bebong.tistory.com/entry/SQS-with-Spring)