# 01. 네트워크 시작하기

## **1.1 네트워크 구성도 살펴보기**

### **1.1.1 홈 네트워크**

- 기본 구성은 인터넷 회선, 모뎀, 공유기, 단말이다.
- 홈 네트워크 구성 요소는 단말, 네트워크 장비, 케이블이다.
- 유선 연결은 유선 랜 카드와 랜 케이블이 필요하다.
- 무선 연결은 무선 랜 카드와 무선 신호 전달 매체가 필요하다.

### **1.1.2 데이터 센터 네트워크**

- 목표는 안정적 서비스 제공과 대용량 트래픽 처리다.
- 서버 수와 통신량이 크므로 10G, 25G, 40G, 100G, 400G 같은 고속 이더넷을 사용한다.
- 전통적 구조는 3계층 구조다.
- 스케일 아웃과 동서 트래픽 증가로 2계층 기반 스파인 리프 구조가 확산된다.
    
    <img width="703" height="258" alt="image" src="https://github.com/user-attachments/assets/bc6bd549-d07d-49a9-889f-d133201e41cf" />
    
- TOR 스위치가 랙에 위치해 서버와 연결되고, TOR 스위치가 리프에 해당한다.
- 리프 스위치는 스파인 스위치와 연결되며 상위 구간은 더 큰 대역폭 링크를 사용한다.
    
    <img width="668" height="327" alt="image" src="https://github.com/user-attachments/assets/c2324c00-ed06-4cc3-93be-84d75127dff7" />
    

## **1.2 프로토콜**

- 프로토콜은 통신 규약이다.
- 물리적 측면 프로토콜은 전송 매체, 신호 규약, 회선 규격을 다룬다.
- 논리적 측면 프로토콜은 장치 간 통신 규칙을 다룬다.
- 최근 네트워크는 TCP/IP 기반 프로토콜 중심으로 정리되는 흐름이다.
- 초기에는 비트 기반 프로토콜 비중이 높다.
- 애플리케이션 계층에는 문자 기반 프로토콜도 많다. (HTTP, SMTP)
- 문자 기반 프로토콜은 사람이 읽기 쉽고 확장에 유리하지만 오버헤드가 커질 수 있다.

## **1.3 OSI 7계층과 TCP/IP**

### **1.3.1 OSI 7계층**

- OSI 7계층은 네트워크 동작을 계층으로 나눠 이해하는 참조 모델이다.
- 계층 분리는 역할 분담과 모듈화를 가능하게 한다.
- 계층별 데이터 단위 개념이 존재한다.
    
    <img width="413" height="294" alt="image" src="https://github.com/user-attachments/assets/475469df-51b9-4e9a-ad06-c1451096e5c3" />
    
- 1계층부터 4계층은 하위 계층, 5계층부터 7계층은 상위 계층으로 구분한다.
    
    <img width="433" height="271" alt="image" src="https://github.com/user-attachments/assets/e338a195-25bc-414c-addf-74c689ddc054" />
 
- 네트워크를 바라보는 흐름은 상위에서 하위로 내려가는 방식과 하위에서 상위로 올라가는 방식으로 나뉜다.

### **1.3.2 TCP/IP 프로토콜 스택**

- 실제 네트워크는 대부분 TCP/IP와 이더넷 기반이다.
- TCP/IP 스택은 TCP와 IP뿐 아니라 UDP, ICMP, ARP와 애플리케이션 프로토콜을 포함한다.
- OSI는 7계층, TCP/IP는 4계층 모델로 정리한다.
    
    <img width="495" height="265" alt="image" src="https://github.com/user-attachments/assets/04d4ec17-4dd9-4024-9dbc-bf1c52e3e91a" />
    

## **1.4 OSI 7계층별 이해하기**

### **1.4.1 1계층 피지컬 계층**

- 물리 연결과 전기 신호 전달을 정의한다.
- 대표 구성 요소는 케이블, 커넥터, 리피터, 허브 등이다.
- 주소 개념 없이 신호 전달이 중심이다.

### **1.4.2 2계층 데이터 링크 계층**

- 전기 신호를 사람이 알아볼 수 있는 데이터 형태로 처리한다.
- 같은 링크 안에서 출발지와 목적지 구분이 필요하다.
- 핵심 주소는 MAC 주소다.
- 네트워크 인터페이스 카드가 송수신 인터페이스 역할을 한다.
    
    <img width="495" height="276" alt="image" src="https://github.com/user-attachments/assets/956f4431-13ce-4cc9-b610-e8204774ca50" />
    
- 스위치는 MAC 주소 학습과 포워딩을 수행한다.
    
    <img width="372" height="429" alt="image" src="https://github.com/user-attachments/assets/e35cc1a7-a06e-42f5-97f1-b7366093bc0a" />
    
- 받는 사람이 데이터를 받을 수 있는지 확인하는 플로 컨트롤 작업이 진행된다.
    
    <img width="516" height="161" alt="image" src="https://github.com/user-attachments/assets/c44449ea-8dcc-4a93-8b2c-4e8144706a5c" />


### **1.4.3 3계층 네트워크 계층**

- 논리 주소인 IP 주소를 사용한다.
- IP 주소는 네트워크 부분과 호스트 부분으로 나뉜다.
    
    <img width="403" height="141" alt="image" src="https://github.com/user-attachments/assets/aa213d21-ca05-490d-9afa-ab48aae8293b" />
    
- 라우터가 경로 선택과 패킷 포워딩을 담당한다.
    
    <img width="474" height="363" alt="image" src="https://github.com/user-attachments/assets/e9a2aced-5f46-43e2-a0a0-1dc8bd2afbeb" />


### **1.4.4 4계층 트랜스포트 계층**

- 1, 2, 3 계층은 신호와 데이터를 올바른 위치로 보내고 실제 신호를 잘 만들어 보내는 데 집중한다.
- 4계층은 실제로 데이터들이 정상적으로 잘 보내지도록 확인하는 역할을 한다.
- 데이터 분할과 재조립을 담당한다.
- 패킷 유실과 순서 변경 같은 전송 문제를 다룬다.
- 시퀀스 번호와 ACK 번호로 신뢰성 흐름을 관리한다.
- 포트 번호로 애플리케이션을 구분한다.
- 로드 밸런서, 방화벽 같은 장비가 4계층에서 동작할 수 있다.

### **1.4.5 5계층 세션 계층**

- 연결 설정, 유지, 종료를 관리한다.
- 에러로 중단된 통신에 대한 에러 복구와 재전송도 수행한다.

### **1.4.6 6계층 프레젠테이션 계층**

- 표현 형식을 맞추는 변환 계층이다.
- MIME 인코딩, 압축, 암호화, 코드 변환이 포함된다.

### **1.4.7 7계층 애플리케이션 계층**

- 사용자 서비스와 직접 연결되는 계층이다.
- 대표 프로토콜은 HTTP, FTP, SMTP, TELNET 등이다.

## **1.5 인캡슐레이션과 디캡슐레이션**

- 인캡슐레이션은 상위 계층 데이터에 하위 계층 헤더를 붙여 전송 가능한 형태로 만드는 과정이다.
- 디캡슐레이션은 수신 측에서 하위 계층부터 헤더를 해석하고 제거해 상위 계층으로 데이터를 전달하는 과정이다.
    
    <img width="662" height="380" alt="image" src="https://github.com/user-attachments/assets/27d49ab4-c6f9-4fe2-8e2d-ea4f66f7bccc" />
    
- 계층별로 역할이 다르므로, 각 계층은 자신이 필요로 하는 제어 정보를 헤더로 추가해야 한다.
- 이 과정이 없으면 링크 전달(MAC), 네트워크 간 전달(IP), 애플리케이션 구분(Port) 같은 기능을 수행할 수 없다.
- 헤더에는 현재 계층에서 필요한 정보와 상위 계층으로 올려보낼 대상을 지정하는 정보가 포함된다.
- 2계층은 이더 타입, 3계층은 프로토콜 번호, 4계층은 포트 번호로 다음 상위 프로토콜을 지정한다.
    
    <img width="706" height="497" alt="image" src="https://github.com/user-attachments/assets/818325f9-aa81-496f-a3df-7e1632e5449b" />
    
- 상위 계층으로 올라갈수록 프로토콜 종류가 늘어나므로, 수신 측은 다음 처리를 결정하기 위한 상위 프로토콜 지시자가 필요하다.
    
    <img width="718" height="282" alt="image" src="https://github.com/user-attachments/assets/4d8ebaaf-6eb0-484c-b951-9ed3d7cab1ab" />
    
- 패킷 네트워크는 데이터를 작은 단위로 나눠 전송해 여러 사용자가 네트워크를 공유하도록 한다.
- MTU는 링크에서 한 번에 실을 수 있는 최대 크기 기준이고, MSS는 TCP에서 데이터 영역의 최대 크기 기준이다.
    
    <img width="394" height="285" alt="image" src="https://github.com/user-attachments/assets/dedd194f-06b8-4b2b-9b5c-877666a4978e" />
    
- MTU 한계를 넘는 데이터는 분할이 필요하며, TCP는 MSS로 한 번에 전송할 데이터 크기를 조절한다.
