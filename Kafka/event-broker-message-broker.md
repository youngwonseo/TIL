
# Kafak, RabbitMQ, Redis Queue의 차이점

## Messaging Platform은 2가지로 구분

* Messageing Platform은 Message Broker와 Event Broker로 구분
* Message Broker는 Event Broker의 역할을 할 수 없음
* Event Broker는 Meesage Broker의 역할을 할 수 있음

### Message Broker

* 대규모 메시지 기반 미들웨어 아키테처에서 사용
* 큐에 데이터를 보내고 받는 프로듀서와 컨슈머를 통해 메시지 통신과 네트워크
* 메시지를 받아서 처리하면 즉시 또는 짧은 시간안에 삭제됨
* 레디스 큐나 레빗엠큐

### Event Broker

* 이벤트(메시지)는 하나만 관리하고 인덱스를 통해 개별 액세스 관리
* 업무상 필요한 시간동안 이벤트를 보존
* 즉 데이터를 삭제하지 않고 이벤트 브로커의 큐에 저장
  * 단일 진실 공급원
  * 장애 발생시 장애가 일어난 지점부터 재처리 가능
  * 많은 양의 실시간 스트림을 효과적으로 처리 가능
  * 이벤트 기반 마이크로서비스에서 중요한 역할
* 카프카, 키네시스

## References

* https://www.youtube.com/watch?v=H_DaPyUOeTo