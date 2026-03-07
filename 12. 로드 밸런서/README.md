# 12. 로드 밸런서

## **12.1 부하 분산이란?**

- 서비스 규모가 커지면 서버 한 대로 모든 요청을 처리하기 어렵다.
- 서버를 여러 대로 늘리면 서버별 IP가 달라 사용자가 어느 서버로 접속할지 결정이 필요하다.
- 로드 밸런서는 다수 서버에 사용자 요청을 분산해 성능과 가용성을 높이는 구성 요소다.
- 단일 서버 구성의 문제는 서버 장애 시 서비스 전체가 중단될 수 있다는 점이다.
- 로드 밸런서를 두면 사용자는 하나의 서비스 주소로 접속하고 장비가 서버로 분산한다.
    
    <img width="692" height="496" alt="image" src="https://github.com/user-attachments/assets/ba2c080b-ad3c-4be1-904e-aa7c394f0a03" />
    
- 용어 정리
    - VIP는 서비스 대표 IP다.
    - Real IP는 실제 서버 IP다.
    - 사용자는 VIP로 접속하고 로드 밸런서가 Real IP로 전달한다.

## **12.2 부하 분산 방법**

- 로드 밸런서는 사용자 요청을 받아 서버 그룹 중 한 대로 전달한다.
- 서비스는 IP뿐 아니라 포트까지 포함해 정의하는 경우가 많다.
- VIP는 서비스 접속 지점이며 각 서버는 Real IP로 서비스를 제공한다.
- 부하 분산 구성 예
    - 서버가 여러 대일 때 서비스별로 분산 그룹을 만들 수 있다.
    - 동일 VIP에서 서비스 포트별로 다른 Real 서버 포트로 매핑할 수 있다.
    - VIP 포트와 Real 포트가 다를 수 있어 포트 변환이 필요할 수 있다.

## **12.3 헬스 체크**

- 로드 밸런서는 서버가 비정상일 때 트래픽을 보내지 않기 위해 헬스 체크를 수행한다.
- 헬스 체크가 실패하면 서버를 서비스 그룹에서 제외하고 정상 복구 시 다시 포함한다.

### **12.3.1 헬스 체크 방식**

- ICMP 방식
    - ping으로 생존 여부만 확인한다.
    - 서비스 레벨 정상 여부를 확인하기 어려워 단독 사용은 한계가 있다.
- TCP 서비스 포트 방식
    - 지정 포트로 연결 가능 여부를 확인한다.
    - SYN → SYN + ACK → ACK 후 정상 종료로 연결을 정리할 수 있다.
        
        <img width="582" height="640" alt="image" src="https://github.com/user-attachments/assets/f3ebc279-24a9-40ba-b0ed-02627577bcd3" />
        
- TCP Half Open 방식
    - 3-way 핸드셰이크를 끝까지 완료하지 않고 SYN + ACK 수신 후 RST로 종료한다.
    - 헬스 체크 부하를 줄이기 위한 방식이다.
        
        <img width="596" height="414" alt="image" src="https://github.com/user-attachments/assets/889c2b86-9052-48d9-bbfa-692e7a2db917" />
        
- HTTP 상태 코드 방식
    - HTTP 요청 후 200 OK 같은 정상 상태 코드를 확인한다.
    - 포트는 열려 있지만 애플리케이션이 비정상인 상황을 걸러낼 수 있다.
        
        <img width="580" height="722" alt="image" src="https://github.com/user-attachments/assets/30d4d99e-f72e-4efb-92b2-3dc601360a60" />
        
- 콘텐츠 확인 방식
    - 응답 본문에 특정 문자열이 포함되는지 확인한다.
    - 단순 문자열만 확인하면 오류 페이지도 통과할 수 있어 상태 코드와 함께 설계하는 것이 안전하다.
        
        <img width="580" height="712" alt="image" src="https://github.com/user-attachments/assets/8d0f07de-ba98-4136-8f2f-ee6906682978" />
        

### **12.3.2 헬스 체크 주기와 타이머**

- 주기 Interval
    - 로드 밸런서가 헬스 체크 패킷을 보내는 간격이다.
- 응답 시간 Response
    - 응답을 기다리는 시간이다.
    - 이 시간 내 응답이 없으면 해당 시도는 실패로 본다.
- 시도 횟수 Retries
    - 실패 시 재시도 횟수다.
    - 최대 시도 횟수 이전에 성공하면 실패 카운트를 초기화한다.
- 타임아웃 Timeout
    - 헬스 체크 실패로 판단하기까지의 최대 대기 시간이다.
- 서비스 다운 시 주기 Dead Interval
    - 서버가 다운 상태일 때 헬스 체크 주기를 늘려 장비 부하를 줄이는 용도로 사용한다.

## **12.4 부하 분산 알고리즘**

- 로드 밸런서는 사전에 설정한 알고리즘으로 서버를 선택해 요청을 분산한다.
    
    <img width="990" height="504" alt="image" src="https://github.com/user-attachments/assets/bf0569ad-f6c8-46ad-b971-aa28c5682406" />
    
- 라운드 로빈 Round Robin
    - 서버를 순서대로 돌아가며 분산한다.
    - 활성 세션 수는 시간대에 따라 달라질 수 있다.
        
        <img width="762" height="306" alt="image" src="https://github.com/user-attachments/assets/0ec62ce6-293d-49b9-aa21-aa5b2d2a3b19" />
  
- 최소 접속 방식 Least Connection
    - 현재 세션이 가장 적은 서버로 분산한다.
    - 로드 밸런서는 요청을 각 장비로 보내줄 때마다 세션 테이블이 생성되므로 각 장비에 연결된 현재 세션 수를 알 수 있다.
        
        <img width="782" height="308" alt="image" src="https://github.com/user-attachments/assets/20fec5de-ae63-4a01-a3d2-be51486a0604" />

- 해시 Hash
    - 해시 결과값으로 특정 서버에 고정적으로 매핑되게 분산한다.
    - 동일 기준값이면 항상 동일 서버로 분산될 수 있어 세션 유지에 유리하다.
    - 특정 서버로 쏠림이 생길 수 있어 기준값 선택이 중요하다.
    - 세션을 유지해야 하는 서비스에 적합한 분산 방식이다.
        
        <img width="772" height="316" alt="image" src="https://github.com/user-attachments/assets/8a25fff2-6e68-4995-9dab-271db40e6a91" />

- 알고리즘 선택 시 고려
    - 세션 유지 필요 여부를 먼저 판단해야 한다.
    - 사용자 경험상 서버가 바뀌면 문제가 되는 서비스는 해시 계열이 유리할 수 있다.
    - 해시와 최소 접속을 혼합하거나 세션 타임아웃을 함께 고려해야 한다.

## **12.5 로드 밸런서 구성 방식**

- 로드 밸런서의 위치에 따라 크게 원암 구성과 인라인 구성으로 구분한다.
    
    <img width="908" height="478" alt="image" src="https://github.com/user-attachments/assets/21e8675f-a09d-47d6-b696-27bd8eef24eb" />


### **12.5.1 원암 구성**

- 로드 밸런서가 스위치 옆에 붙는 형태다.
- 서버로 가는 트래픽 중 부하 분산이 필요한 트래픽만 로드 밸런서를 경유하게 설계할 수 있다.
    
    <img width="556" height="394" alt="image" src="https://github.com/user-attachments/assets/9562397d-ccf9-49c4-8a46-d67df84cd6fe" />

- 원암 구성의 예
    - 로드 밸런서와 스위치 간 연결이 여러 개인 구성도 원암으로 볼 수 있다.
        
        <img width="908" height="392" alt="image" src="https://github.com/user-attachments/assets/2fc30348-babd-4957-a209-d09129865884" />

    - 부하 분산 트래픽은 로드 밸런서를 경유한다.
 
        <img width="556" height="378" alt="image" src="https://github.com/user-attachments/assets/7e564c54-90bb-4a3a-b71c-9a4c5d73af1a" />

    - 부하 분산이 필요 없는 트래픽은 로드 밸런서를 경유하지 않을 수 있다.
        
        <img width="606" height="376" alt="image" src="https://github.com/user-attachments/assets/347817d2-382c-475c-b147-7f85bf136649" />

- 원암 구성 포인트
    - 로드 밸런서를 경유하지 않는 트래픽이 존재하면 로드 밸런서 부하를 줄일 수 있다.
    - 대신 서비스 트래픽과 비서비스 트래픽을 구분해 설계해야 한다.
    - 서버 응답 경로가 로드 밸런서를 경유하지 않으면 Source NAT나 DSR 같은 추가 설계가 필요해질 수 있다.

### **12.5.2 인라인 구성**

- 로드 밸런서가 서버로 가는 일직선 경로에 위치한다.
    
    <img width="540" height="314" alt="image" src="https://github.com/user-attachments/assets/286880fd-bde0-47e8-8cab-f273c4945461" />

- 부하 분산 여부와 관계없이 서버로 가는 트래픽이 로드 밸런서를 모두 통과한다.
    
    <img width="762" height="320" alt="image" src="https://github.com/user-attachments/assets/24559f3c-cbf7-437e-846c-61f96470bf53" />

- 인라인 구성 포인트
    - 구조가 단순해 이해하기 쉽다.
    - 모든 트래픽이 통과하므로 처리 성능과 패킷 처리량을 충분히 고려해야 한다.

## **12.6 로드 밸런서 동작 모드**

- 로드 밸런서의 동작 모드는 트래픽 흐름과 NAT 적용 방식에 직접 영향을 준다.
- 주요 동작 모드는 트랜스패런트 모드, 라우티드 모드, DSR 모드가 있다.

### **12.6.1 트랜스패런트 모드**

- 로드 밸런서가 L2 브리지처럼 동작하는 모드다.
- VIP와 Real 서버가 동일 네트워크 대역에 있는 구성이 일반적이다.
- 원암 구성과 인라인 구성 모두에서 사용할 수 있다.
    
    <img width="894" height="368" alt="image" src="https://github.com/user-attachments/assets/11a64401-dfa4-47df-ac62-a9c95b3e3348" />

- 서비스 요청 시 흐름
    - 사용자는 VIP로 요청한다.
    - 로드 밸런서가 목적지 IP를 Real IP로 변경해 전달하는 Destination NAT를 수행한다.
        
        <img width="674" height="370" alt="image" src="https://github.com/user-attachments/assets/b4bfba41-d46a-4c89-b002-dd9283de1771" />

- 서비스 응답 시 흐름
    - 서버가 사용자에게 응답할 때 출발지 IP를 VIP로 바꿔 응답한다.
    - 이때 응답 경로가 로드 밸런서를 경유하지 않으면 문제가 될 수 있다.
        
        <img width="676" height="368" alt="image" src="https://github.com/user-attachments/assets/dcdd9272-1337-4375-8318-bceb4e4f7a80" />


### **12.6.2 라우티드 모드**

- 로드 밸런서가 라우팅 역할을 수행하는 모드다.
- 사용자 네트워크와 서버 네트워크를 분리해 구성한다.
- 원암 구성과 인라인 구성 모두에서 구성 가능하다.
    
    <img width="828" height="436" alt="image" src="https://github.com/user-attachments/assets/689bd30a-9658-4219-a5bf-bc303d6285cb" />

- 서비스 요청 시 흐름
    - VIP로 들어온 요청을 Real IP로 DNAT 후 서버로 전달한다.
    - 라우팅을 수행하므로 MAC 주소 변경과 L3 경로 처리가 포함된다.
        
        <img width="672" height="376" alt="image" src="https://github.com/user-attachments/assets/43f2f581-d249-4c9e-8255-d9239ede7814" />

- 서비스 응답 시 흐름
    - 서버가 사용자에게 응답할 때 로드 밸런서를 통해 나가도록 설계한다.
    - 출발지와 목적지 정보가 라우팅 흐름에 맞게 처리된다.
        
        <img width="674" height="366" alt="image" src="https://github.com/user-attachments/assets/aba98a9e-4608-401e-b972-f2ecf9431250" />


### **12.6.3 DSR 모드**

- Direct Server Return 방식으로 서버가 사용자에게 직접 응답하는 모드다.
- 로드 밸런서는 요청 트래픽만 처리하고 응답 트래픽은 처리하지 않는다.
- 응답 트래픽이 커서 로드 밸런서 부하를 줄이고 싶을 때 효과적이다.
- L2 DSR과 L3 DSR
    - 서버 네트워크와 로드 밸런서 사이가 L2인지 L3인지에 따라 L2 DSR과 L3 DSR로 구분한다.
        
        <img width="836" height="372" alt="image" src="https://github.com/user-attachments/assets/287142db-fc66-4e83-b119-7e5c8c480dee" />

- DSR 모드의 핵심
    - 서버가 VIP를 목적지로 받은 요청을 처리해야 한다.
    - 서버가 직접 응답하므로 Source NAT를 할 수 없다.
        
        <img width="930" height="296" alt="image" src="https://github.com/user-attachments/assets/aea05a3c-130f-472a-9a6e-2d4305b8d532" />

- 서비스 요청 흐름
    - 로드 밸런서는 VIP로 들어온 요청을 Real 서버로 전달하되 목적지 IP는 VIP를 유지하도록 처리한다.
    - 이 경우에 루프백 IP를 VIP와 동일하게 설정해야 한다.
        
        <img width="706" height="444" alt="image" src="https://github.com/user-attachments/assets/7ef1b249-c60a-4cd8-ab65-038ce0001514" />

- 서비스 응답 흐름
    - 서버는 로드 밸런서를 거치지 않고 사용자에게 직접 응답한다.
    - 출발지 IP는 VIP로 보이게 구성한다.
        
        <img width="696" height="446" alt="image" src="https://github.com/user-attachments/assets/07fab666-580e-4d19-9e66-c714bf4f2a0d" />

- DSR을 위한 서버 추가 설정
    - 루프백 인터페이스에 VIP를 설정해야 한다.
    - ARP 응답이 꼬이지 않도록 커널 파라미터를 조정해야 한다.
    - 운영체제별 설정이 필요하다.

## **12.7 로드 밸런서 유의사항**

- 로드 밸런서는 구성 방식과 동작 모드에 따라 트래픽 흐름이 달라지고, 그 흐름이 장애와 운영 이슈로 이어진다.

### **12.7.1 원암 구성의 동일 네트워크 사용 시**

- 원암 구성에서 서비스 IP와 서버 네트워크가 동일 대역이면 응답 트래픽이 로드 밸런서를 경유하지 않을 수 있다.
- 이 경우 사용자는 VIP로 요청했는데 Real IP로 응답을 받아 비정상으로 판단할 수 있다.
    
    <img width="632" height="408" alt="image" src="https://github.com/user-attachments/assets/446c9be4-34d0-4e78-b94e-849a7dccea20" />

- 해결 방법 1) 게이트웨이를 로드 밸런서로 설정
    - 서버의 기본 게이트웨이를 로드 밸런서로 두어 응답이 로드 밸런서를 경유하게 만든다.
        
        <img width="630" height="420" alt="image" src="https://github.com/user-attachments/assets/71b8a284-1575-466b-bb39-c49b52c80c1c" />

    - 다만 원암 구성에서 로드 밸런서를 경유하지 않던 트래픽이 늘어 부하 감소 효과가 줄 수 있다.
- 해결 방법 2) Source NAT 사용
    - 로드 밸런서가 DNAT뿐 아니라 SNAT도 수행해 흐름을 강제한다.
        
        <img width="698" height="416" alt="image" src="https://github.com/user-attachments/assets/4f415231-c614-4a85-b2f3-57bae0721f81" />

    - 서버는 응답을 로드 밸런서로 보내게 되어 대칭 경로가 형성된다.
- 해결 방법 3) DSR 모드
    - 동일 네트워크에서 DSR로 요청을 전달하고 서버가 VIP로 직접 응답하게 한다.
        
        <img width="694" height="414" alt="image" src="https://github.com/user-attachments/assets/9bd9b461-5ab8-4aee-a749-6190831362e9" />

    - 서버에 루프백 VIP 설정과 ARP 제어가 필요하다.

### **12.7.2 동일 네트워크 내에서 서비스 IP(VIP) 호출**

- 서버가 동일 네트워크에서 VIP로 호출하는 상황이 문제가 될 수 있다.
- 어떤 서버는 로드 밸런서를 경유해 Real 서버로 전달되지만, 응답이 로드 밸런서를 거치지 않으면 호출 서버가 응답을 비정상으로 처리할 수 있다.
    
    <img width="978" height="730" alt="image" src="https://github.com/user-attachments/assets/01db2753-41ce-4378-a6f5-acea9f1dfb43" />

- 대응 방향
    - Source NAT 또는 DSR 같은 방식으로 응답 흐름을 일관되게 만들어야 한다.
    - 서비스 특성에 따라 서버 간 호출 구조를 재설계할 수도 있다.
 

## **12.8 HAProxy를 사용한 로드 밸런서 설정**

- HAProxy는 소프트웨어 기반 로드 밸런서다.
- 하드웨어 장비가 제공하는 기능을 소프트웨어로 제공하는 NFV 관점의 대표 사례로 볼 수 있다.

### **12.8.1 HAProxy 설치**

- 패키지로 설치할 수 있다.
- 저장소 버전이 낮으면 최신 소스를 내려받아 컴파일해 설치할 수 있다.
- 설치 후 init 스크립트와 실행 파일 링크, 실행에 필요한 디렉터리를 준비한다.

### **12.8.2 HAProxy 설정**

- 기본 설정 파일 경로는 /etc/haproxy/haproxy.cfg다.
- 설정 파일은 다음 섹션들로 구성된다.
    - global 섹션
        - 프로세스 전반 설정을 정의한다.
        - log, daemon, maxconn 같은 항목을 둔다.
    - defaults 섹션
        - 기본 동작 값을 정의한다.
        - mode, timeout connect, timeout client, timeout server 같은 항목을 둔다.
    - frontend 섹션
        - 클라이언트가 접속하는 리스닝 지점을 정의한다.
        - bind로 서비스 포트를 연다.
        - default_backend로 백엔드를 지정한다.
    - backend 섹션
        - 실제 서버 그룹을 정의한다.
        - balance 알고리즘과 server 목록, check를 설정한다.
    - listen 섹션
        - frontend와 backend를 한 섹션에서 함께 정의할 수 있다.
        - 상태와 통계 UI 용도로도 자주 사용한다.

### **12.8.3 HAProxy 동작 및 모니터링**

- 서비스로 실행하거나 설정 파일을 지정해 직접 실행할 수 있다.
- 리스닝 포트를 확인해 정상 기동 여부를 점검한다.
- 정상 분산 동작은 접속 시 백엔드 서버로 번갈아 응답되는 방식으로 확인할 수 있다.
- 통계 UI 제공
    - stats enable과 stats uri, 인증 정보를 설정해 상태 페이지를 제공할 수 있다.
    - 대시보드에서 백엔드 서버 상태와 트래픽 현황을 확인할 수 있다.
