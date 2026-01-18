# 03. 네트워크 통신하기

## **3.1 유니캐스트, 멀티캐스트, 브로드캐스트, 애니캐스트**

- 단말이 데이터를 누구에게 보내는지에 따라 통신 방식이 나뉜다.
- 유니캐스트
    - 1:1 통신
    - 대부분의 일반 통신은 유니캐스트 기반이다.
- 브로드캐스트
    - 1:모든 통신
    - 같은 네트워크(브로드캐스트 도메인) 내 모든 단말에 전송한다.
    - 트래픽이 불필요하게 커질 수 있어 범위를 통제해야 한다.
- 멀티캐스트
    - 1:그룹(멀티캐스트 구독 호스트) 통신
    - 특정 그룹에 가입한 단말에만 전송한다.
    - 다수 수신자가 같은 데이터를 받아야 할 때 효율적이다.
- 애니캐스트
    - 1:1 통신(목적지는 그룹 내의 1개 호스트)
    - 동일한 목적지 주소를 여러 장비가 가질 때, 가장 가까운 장비로 전달되는 방식이다.
    - 서비스 분산과 지연 감소에 활용된다.

<img width="535" height="263" alt="image" src="https://github.com/user-attachments/assets/25990b40-bb38-4921-abb2-2e199c8bd28c" />

- BUM 트래픽
    - B(Broadcast), U(Unknown Unicast), M(Multicast)
    - 브로드캐스트, 언노운 유니캐스트, 멀티캐스트 트래픽을 묶어 관리 관점에서 본다.
    - 네트워크가 커질수록 BUM이 성능과 안정성에 영향을 준다.

## **3.2 MAC 주소**

- 2계층에서 통신 상대를 식별하기 위한 물리 주소다.
- 같은 네트워크 안에서 실제 프레임 전달은 MAC 주소를 기반으로 동작한다.

### **3.2.1 MAC 주소 체계**

- MAC 주소는 48비트로 표현한다.
- 앞부분은 제조사 식별, 뒷부분은 장비 식별 역할을 한다.
- 같은 네트워크에서 MAC 주소가 겹치면 2계층 통신 자체가 흔들릴 수 있다.

### **3.2.2 MAC 주소 동작**

- NIC는 전기 신호가 들어오면 2계층에서 데이터 형태(패킷)로 변환하여 도착지 MAC주소를 확인한다. 만약 도착지 MAC 주소가 자신의 주소가 아니면 패킷을 폐기한다. 만약 목적지 주소가 자신이거나 브로드캐스트, 멀티캐스트와 같은 그룹 주소이면 패킷을 상위 계층으로 전달한다.
    
    <img width="670" height="296" alt="image" src="https://github.com/user-attachments/assets/15dac479-79b4-4af5-a22a-22e353c1930f" />
    
- 스위치는 MAC 주소 테이블을 학습해 목적지 포트로 프레임을 전달한다.
- 목적지 MAC을 모르면 일단 브로드캐스트 성격으로 흘러 트래픽이 커질 수 있다.
- 목적지 MAC 학습이 끝나면 이후 통신은 불필요한 확산 없이 진행된다.

## **3.3 IP 주소**

### **3.3.1 IP 주소 체계**

- IP는 3계층에서 목적지 네트워크와 호스트를 식별하기 위한 논리 주소다.
- IP는 네트워크 부분과 호스트 부분으로 나뉜다.
- 클래스 기반 구분은 과거 방식이며 주소 낭비 문제가 있다.
- 네트워크 주소와 브로드캐스트 주소는 호스트에 할당하지 않는다.

### **3.3.2 서브넷 마스크 표현**

- 클래스리스 네트워크에서는 네트워크와 호스트 주소를 나누는 구분자를 사용해야 하는데, 이 구분자를 서브넷 마스크(Subnet Mask)라고 부른다.
- 서브넷 마스크는 네트워크 부분과 호스트 부분을 나누는 기준이다.
    
    <img width="640" height="341" alt="image" src="https://github.com/user-attachments/assets/d89d39f0-6c58-4987-b21e-d6be84b2598e" />
    
- 10진수 표기와 비트 길이 표기 모두 사용한다.
- 비트 길이 표기는 네트워크 비트 개수를 직접 드러내 계산과 설계에 유리하다.
    
    <img width="491" height="262" alt="image" src="https://github.com/user-attachments/assets/dccd693f-fcab-4506-9077-167f2408d83b" />
    

### **3.3.3 서브네팅**

- 원래 클래스풀 방식의 고정된 네트워크 크기를 그대로 쓰지 않고, 필요한 만큼 프리픽스를 조정해 네트워크를 더 잘게 나눠 쓰는 것을 **서브네팅(Subnetting)** 이라고 한다.
- 왜 필요한가
    - 네트워크를 목적 단위로 분리해 관리와 장애 범위를 줄이기 위해 필요하다.
    - 필요한 만큼만 호스트 수를 확보해 IP 낭비를 줄이기 위해 필요하다.
- 어떻게 동작하는가
    - 서브넷 마스크로 네트워크 비트와 호스트 비트를 나눈다.
    - 같은 네트워크인지 판단할 때 IP와 서브넷 마스크의 AND 결과를 사용한다.
        
        <img width="633" height="189" alt="image" src="https://github.com/user-attachments/assets/a266301a-33bd-4ec9-ab7e-207a110b25f4" />
        

### **3.3.3.1 네트워크 사용자의 서브네팅**

- 목적은 내가 속한 네트워크 범위와 사용 가능한 IP 범위를 정확히 구하는 것이다.
    
    <img width="711" height="471" alt="image" src="https://github.com/user-attachments/assets/fc49347c-f6c4-4d9b-a96c-1180a70e5269" />
    
- 계산 흐름
    - IP를 2진수로 표현한다.
    - 서브넷 마스크를 2진수로 표현한다.
    - AND 연산으로 네트워크 주소를 구한다.
    - 호스트 비트를 모두 1로 만들어 브로드캐스트 주소를 구한다.
    - 네트워크 주소 다음부터 브로드캐스트 주소 전까지를 유효 IP 범위로 본다.

### **3.3.3.2 네트워크 설계자 입장**

- 목적은 필요한 단말 수와 필요한 서브넷 수를 만족하도록 마스크를 정하는 것이다.
- 설계 시 먼저 정리할 것
    - 서브넷 하나에 몇 개의 IP가 필요한가
    - 서브넷된 네트워크가 몇 개 필요한가
- 예시 흐름
    - 필요한 IP 개수를 수용하는 가장 작은 블록 크기를 고른다.
    - 블록 경계 기준으로 네트워크 주소들을 나열한다.
    - 각 서브넷의 네트워크 주소와 브로드캐스트 주소, 유효 IP 범위를 확정한다.

### **3.3.4 공인 IP와 사설 IP**

- 공인 IP
    - 인터넷에서 유일해야 하는 주소다.
    - 사용 가능한 주소가 제한적이라 할당과 운영이 까다롭다.
- 사설 IP
    - 내부 네트워크에서만 쓰는 주소다.
    - 외부 통신은 NAT 같은 변환 기술을 통해 공인 IP로 나가게 된다.
        
        <img width="460" height="203" alt="image" src="https://github.com/user-attachments/assets/5ca9203f-8236-4179-8897-1880f888cade" />
        
- Bogon IP
    - 인터넷 라우팅에서 사용되지 않거나 예약된 주소 범위다.
    - 만약 Bogon IP 대역에서 통신 시도가 있다면 해킹 목적으로 IP를 스푸핑했거나 실수로 할당된 IP인 경우이므로 적절히 필터링하는 것이 좋다.

## **3.4 TCP와 UDP**

- 4계층은 애플리케이션이 사용할 통신 단위를 만들고, 데이터의 전달 방식을 규정한다.
- 같은 IP 통신이라도 어떤 4계층 프로토콜을 쓰느냐에 따라 품질과 지연이 달라진다.

### **3.4.1 4계층 프로토콜(TCP, UDP)과 서비스 포트**

- 포트는 한 호스트 안에서 어떤 프로세스와 통신할지 구분하는 값이다.
- 인캡슐레이션과 디캡슐레이션 과정에 각 계층별로 필요한 헤더가 추가된다.
    - 각 계층이 정의하는 정보
    - 상위 프로토콜 지시자 정보
        
        <img width="705" height="443" alt="image" src="https://github.com/user-attachments/assets/7fcd44e6-1cd8-40bb-ac24-a7f081ca3be5" />
        
- TCP/IP 프로토콜 스택에서 4계층의 상위 프로토콜 지시자는 포트 번호이다.
    
    <img width="612" height="389" alt="image" src="https://github.com/user-attachments/assets/582c7fa8-d909-49bd-95b2-9d3de1365342" />
    
- 포트 범위 개념
    - 잘 알려진 포트는 주로 서버가 고정으로 사용하는 포트다.
    - 등록 포트는 서비스가 신청해 쓰는 범위다.
    - 동적 포트는 클라이언트 임시 포트로 자주 쓰인다.
- 클라이언트 서버 통신에서 요청과 응답은 출발지 포트와 목적지 포트가 서로 뒤바뀐다.
    
    <img width="598" height="282" alt="image" src="https://github.com/user-attachments/assets/a4230635-142c-4708-935c-e03d2aac674e" />
    

### **3.4.2 TCP**

- TCP는 신뢰성 있는 전송을 위해 순서 제어, 오류 제어, 흐름 제어를 제공한다.
- 왜 필요한가
    - 네트워크가 불안정해도 애플리케이션이 안정적으로 데이터를 받게 하기 위해 필요하다.
    - 유실과 재전송, 순서 뒤바뀜을 애플리케이션이 직접 처리하지 않게 하기 위해 필요하다.
- TCP 헤더 구성은 다음이 핵심이다.
    - 시퀀스 번호와 ACK 번호
    - 윈도 사이즈
    - 플래그

### **3.4.2.1 패킷 순서, 응답 번호**

- TCP는 데이터를 세그먼트로 분할해 전송하며, 각 세그먼트에 시퀀스 번호와 확인 응답(ACK) 번호를 부여해 수신 측이 올바른 순서로 재조립하도록 한다.
- 시퀀스 번호는 보내는 데이터의 순서를 나타낸다.
- ACK 번호는 수신자가 다음에 받고 싶은 시퀀스 번호를 의미한다.
- 둘을 함께 쓰면
    - 유실 여부를 감지할 수 있다.
    - 순서가 어긋난 상황을 파악할 수 있다.
        
        <img width="575" height="321" alt="image" src="https://github.com/user-attachments/assets/5d7492bc-e969-45a9-9f53-66d945cf5ebe" />
        
        <img width="716" height="447" alt="image" src="https://github.com/user-attachments/assets/490e173e-297a-444b-8089-8fb6c7b6efd8" />
        

### **3.4.2.2 윈도 사이즈와 슬라이딩 윈도**

- 한 번에 보낼 수 있는 데이터량을 조절해 전송 효율을 높인다.
- 왜 필요한가
    - 매 패킷마다 ACK를 기다리면 RTT가 커질수록 전송이 느려진다.
    - 네트워크 상태가 나쁠 때 무작정 많이 보내면 유실이 늘어난다.
- 슬라이딩 윈도는 네트워크 상황에 따라 윈도 사이즈를 조절하는 방식이다.
    
    <img width="489" height="454" alt="image" src="https://github.com/user-attachments/assets/33865b16-86e5-4445-9402-ea94bc7082d2" />
    

### **3.4.2.3 3방향 핸드셰이크**

- 통신 시작 전에 양쪽이 연결 준비가 됐는지 확인하는 절차다.
- 왜 필요한가
    - 수신 준비가 안 된 상태에서 데이터가 일방적으로 들어오는 상황을 막기 위해 필요하다.
    - 연결 상태를 명확히 만들어 이후 데이터 전송의 기준을 세우기 위해 필요하다.
- 상태 전이 흐름
    
    <img width="543" height="372" alt="image" src="https://github.com/user-attachments/assets/098150da-6c4c-4032-9480-4bb1cd2c5940" />
    
    <img width="547" height="388" alt="image" src="https://github.com/user-attachments/assets/e9d3cb0a-a292-4af2-9cd0-5167f62df0fa" />
    
- TCP는 3-way 핸드셰이크로 연결을 설정하기 때문에, 기존 연결과 새 연결을 구분해야 한다. 이를 위해 TCP 헤더의 플래그(예: SYN, ACK, FIN)를 사용해 연결의 상태와 제어 정보를 주고받는다.
    
    <img width="668" height="291" alt="image" src="https://github.com/user-attachments/assets/22e5438d-7fa9-49f8-aa3b-535cd3ea5c35" />
    
- 플래그 의미는 연결 제어의 핵심이다.
    - SYN: 연결 시작
    - ACK: 응답 유효 표시
    - FIN: 연결 종료
    - RST: 강제 종료
    - URG: 긴급 데이터
    - PSH: 버퍼링 없이 즉시 전달 지시

### **3.4.3 UDP**

- UDP는 연결을 만들지 않고 데이터를 보낸다.
- TCP에 비해 신뢰성 기능이 거의 없다.
    - 시퀀스 번호, ACK, 윈도 같은 제어가 없다.
    - 헤더 구조가 단순하다.
        
        <img width="707" height="190" alt="image" src="https://github.com/user-attachments/assets/adf5c22d-c379-4fa4-be1c-043e840e0b4b" />
        
- 왜 사용하는가
    - 지연이 치명적인 서비스는 재전송으로 인한 끊김이 더 문제일 수 있다.
    - 실시간 음성, 영상, 실시간 스트리밍 같은 유형에서 선택될 수 있다.
        
        <img width="492" height="328" alt="image" src="https://github.com/user-attachments/assets/e6341ca2-8ab2-4bc1-8fb8-b37c888e9ee9" />
        

## **3.5 ARP**

- IP는 3계층 주소이고 MAC은 2계층 주소다.
- 같은 네트워크 안에서 실제 프레임 전달을 하려면 목적지 MAC이 필요하다.
- ARP는 상대방의 MAC 주소를 알아내기 위한 프로토콜이다.

### **3.5.1 ARP란?**

- IP와 MAC 사이에 직접 관계가 없기 때문에 매핑 과정이 필요하다. 이때 사용되는 프로토콜이 ARP이다.
    
    <img width="554" height="250" alt="image" src="https://github.com/user-attachments/assets/75aaaee7-00dd-48c7-9c8a-5ca220449e83" />
    
- 목적지 MAC을 모르면 2계층 캡슐화를 완료할 수 없다.
- ARP는 같은 L2 도메인에서 특정 IP 주소에 대응하는 MAC 주소를 알아내기 위해 ARP Request를 브로드캐스트로 전송하고, 해당 IP를 가진 장비가 ARP Reply를 유니캐스트로 응답한다.
- 만약, 목적지 IP가 다른 네트워크에 있으면 실제 목적지 대신 기본 게이트웨이 IP에 대해 ARP를 수행한다.
- ARP 테이블은 일정 시간 캐시로 유지된다.
- 네트워크 장비는 ARP 처리를 CPU로 수행하는 경우가 있어 과도한 ARP는 부하가 될 수 있다.

### **3.5.2 ARP 동작**

- ARP 패킷에서 핵심 필드 4가지
    - 송신자 MAC
    - 송신자 IP
    - 대상자 MAC
    - 대상자 IP
        
        <img width="558" height="246" alt="image" src="https://github.com/user-attachments/assets/0ea43dbe-1554-413f-80a5-4f9c78458b7e" />
        
- 흐름 정리
    - 목적지 MAC을 모르면 ARP 요청을 브로드캐스트한다.
        
        <img width="580" height="264" alt="image" src="https://github.com/user-attachments/assets/fa343720-1ba7-4f42-805c-8ec339e46c79" />
        
        <img width="585" height="243" alt="image" src="https://github.com/user-attachments/assets/23b45567-992c-4731-ab83-aba0a7559223" />
        
    - 대상 IP를 가진 장비만 ARP 응답을 유니캐스트로 보낸다.
        
        <img width="586" height="274" alt="image" src="https://github.com/user-attachments/assets/37ba2346-50b1-4244-9a47-e172e700f6b2" />
        
    - 송신자는 ARP 캐시를 갱신하고 이후 정상 통신을 한다.
        
        <img width="569" height="270" alt="image" src="https://github.com/user-attachments/assets/d7fd9bfb-b05b-4a25-af1e-f32b714b74a0" />
        

### **3.5.3 GARP**

- GARP는 ARP 형식을 빌려 자신의 IP와 MAC 정보를 네트워크에 알리는 용도로 사용한다.
- 패킷 특징
    - 목적지 MAC은 브로드캐스트다.
    - 대상자 MAC은 0으로 채운다.
    - 송신자 IP와 대상자 IP를 자신의 IP로 둔다(그림 3-40)
- 왜 필요한가
    - IP 주소 충돌 감지
    - 네트워크 장비나 단말의 ARP 테이블 갱신
    - 고가용성 구성에서 장애 전환 직후 경로를 즉시 정상화

### **3.5.3.1 IP 주소 충돌 감지**

- 같은 IP를 다른 장비가 쓰고 있으면 통신 장애가 발생한다.
- GARP를 보내고 응답이 오면 해당 IP가 이미 사용 중임을 알 수 있다.

### **3.5.3.2 상대방(동일 서브넷에 있는)의 ARP 테이블 갱신**

- 액티브-스탠바이 구조에서 VIP가 이동하면 단말의 ARP 캐시가 낡아질 수 있다.
- GARP로 단말들의 ARP 테이블을 새 MAC으로 갱신해 서비스 단절을 줄인다.
    
    <img width="690" height="426" alt="image" src="https://github.com/user-attachments/assets/b2ebdfde-0b86-42cb-9669-77acec5f8db3" />
    

### **3.5.3.3 클러스터링, FHRP(VRRP, HSRP)**

- VRRP, HSRP는 디폴트 게이트웨이 장애를 대비하는 1홉 이중화 개념이다.
- 가상 MAC을 쓰는 경우
    - 단말의 ARP 테이블 갱신 부담은 줄어든다.
    - 대신 스위치 MAC 테이블 갱신이 중요해질 수 있다.
        
        <img width="690" height="437" alt="image" src="https://github.com/user-attachments/assets/36cf693f-b5d5-436f-b166-5b0cbaf2bbf6" />
        
- 장애 전환이 느릴 때는 GARP 송신 방식이나 스위치 갱신 특성도 함께 점검해야 한다.

### **3.5.4 RARP**

- RARP는 MAC을 알고 IP를 모르는 단말이 IP 할당을 요청하는 개념이다.
    
    <img width="237" height="219" alt="image" src="https://github.com/user-attachments/assets/f75cd7c6-55e7-4694-9f1b-d92bbfbc6679" />
    
- 현재는 BOOTP, DHCP로 대체돼 거의 쓰지 않는다.

## **3.6 서브넷과 게이트웨이**

- ARP는 같은 L2 도메인(같은 LAN 또는 VLAN) 내에서만 유효하다.
- 다른 네트워크로 통신하려면 라우팅을 수행하는 3계층 장비가 필요하다.
- 이때 원격지 네트워크로 나가는 출구 역할을 하는 장비를 게이트웨이(기본 게이트웨이)라고 하며, 라우터나 L3 스위치가 그 역할을 수행한다.

### **3.6.1 서브넷과 게이트웨이의 용도**

- ARP 브로드캐스트는 원격 네트워크로 전달되지 않는다.
    
    <img width="529" height="463" alt="image" src="https://github.com/user-attachments/assets/cab01eb6-5321-4bc9-af2f-bd19626b169a" />
    
- 출발지 입장에서 먼저 목적지가 같은 네트워크인지 판단해야 한다.
    - 서브넷 마스크로 네트워크 범위를 계산한다.
- 같은 네트워크면 ARP로 목적지 MAC을 알아내 직접 통신한다.
- 다른 네트워크면 기본 게이트웨이로 보낸다.
    - 목적지 MAC은 게이트웨이 MAC이 된다.

### **3.6.2 2계층 통신 vs 3계층 통신**

- 실무에서 2계층 통신, 3계층 통신은 로컬 통신과 원격 통신을 구분하는 표현으로 쓴다.
    
    <img width="695" height="322" alt="image" src="https://github.com/user-attachments/assets/6b4d8e64-34fe-47a2-b484-811dc8912e5f" />
    
- 2계층 통신
    - 같은 네트워크 안에서 ARP로 상대 MAC을 얻고 직접 통신한다.
- 3계층 통신
    - 다른 네트워크라 라우터 도움이 필요하다.
    - 단말은 게이트웨이 MAC을 목적지 MAC으로 사용해 프레임을 보낸다.
        
        <img width="499" height="461" alt="image" src="https://github.com/user-attachments/assets/1befab42-8d83-4754-814e-89b104f31174" />
        
- 로컬 통신(L2 통신)은 도착지 MAC 주소와 도착지 IP 주소가 같은 반면, 원격지 통신(L3 통신)은 도착지 MAC 주소와 도착지 IP 주소가 다르다.
    
    <img width="580" height="286" alt="image" src="https://github.com/user-attachments/assets/5da10a08-5741-4ed8-8d63-e7140f933db5" />
