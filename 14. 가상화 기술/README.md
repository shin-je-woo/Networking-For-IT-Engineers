# 14. 가상화 기술

## 14.1 장비 가상화 기술이란?

- 가상화는 컴퓨터 자원과 기능을 추상화해 실제 물리 자원처럼 보이거나 동작하게 만드는 기술이다.
- 네트워크 장비 관점의 가상화는 여러 개의 물리 장비를 하나의 논리 장비로 다루거나 하나의 물리 장비를 논리적으로 분할해 운영 목적을 달성하는 기술이다.
- 장비 가상화는 크게 두 범주로 나뉜다.
    - 여러 대의 물리 장비를 하나의 논리 장비로 합치는 가상화이다.
    - 하나의 물리 장비를 여러 개의 논리 장비로 나누는 가상화이다.
        
        <img width="453" height="107" alt="image" src="https://github.com/user-attachments/assets/7d676fe0-f4f1-4413-a14b-4b79887d1582" />
        

### 14.1.1 여러 대 물리 장비를 하나의 논리 장비로 만드는 목적

- 관리 포인트를 줄이기 위한 목적이 있다.
    - 여러 장비를 하나처럼 다루면 설정과 운영의 단위가 줄어 관리 부담이 감소한다.
- 이중화 경로를 효율적으로 사용하기 위한 목적이 있다.
    - 2계층 이중화 경로는 루프 위험 때문에 STP로 한 경로가 차단되는 구성이 흔하다.
    - 이 경우 이중화가 있어도 실제 사용 경로가 줄어 대역폭 활용이 떨어진다.
        
        <img width="286" height="162" alt="image" src="https://github.com/user-attachments/assets/3e73ddbf-d121-4422-a128-b500eb041a27" />

- 장비를 논리 장비로 묶으면 2계층에서 루프 제약을 줄이며 경로를 더 적극적으로 쓰는 설계를 만들 수 있다.
- 이 범주에는 링크 이중화 관점의 LACP와 MC-LAG도 포함된다.

### 14.1.2 여러 대를 하나로 묶는 대표 구성 사례

- 두 대의 스위치를 논리 장비로 묶고 상호 연결을 만드는 구성이 있다.
    
    <img width="418" height="198" alt="image" src="https://github.com/user-attachments/assets/5cee4f83-47e2-4347-8cc3-46ca60f774dc" />

- 스위치 여러 대를 아예 하나의 가상 스위치처럼 구성하는 구성이 있다.
    
    <img width="417" height="198" alt="image" src="https://github.com/user-attachments/assets/50f737bb-4de8-4f62-a722-0d9345b42572" />


### 14.1.3 하나의 물리 장비를 여러 논리 장비로 나누는 개념

- 하나의 물리 장비 자원을 논리 단위로 나누어 서로 다른 역할을 독립적으로 수행하게 하는 방식이다.
- 논리 장비 간 자원 간섭을 줄이기 위해 자원을 독립적으로 할당하는 모델이 중요하다.
    
    <img width="368" height="165" alt="image" src="https://github.com/user-attachments/assets/b85ae696-63fe-4265-9437-8697b5cb1daa" />

- 데이터 센터에서 영역을 나누어 구성할 때 공통으로 필요한 장비를 논리적으로 쪼개 영역별로 배치하는 발상이 가능하다.
    
    <img width="509" height="308" alt="image" src="https://github.com/user-attachments/assets/cacbf1e6-ca08-4a8a-81ee-cc3cd987d144" />
 

### 14.1.4 장비 가상화의 기대 효과와 주의점

- 운영 시 관리 포인트가 감소한다.
- 자원 활용률이 증가한다.
    - 한 장비의 유휴 자원을 다른 영역이 활용하는 설계를 만들 수 있다.
- 도입비용과 운영비용이 절감될 수 있다.
    - CAPEX는 장비 도입비용이다.
    - OPEX는 운영비용이다.
- 장비 가상화가 항상 정답은 아니다.
    - 적재적소에 쓰지 않으면 인프라 구성에 불리할 수 있다.
    - 가상화로 나눈 각 영역이 요구하는 성능을 물리 장비가 제공할 수 있어야 한다.
    - 성능이 단순 비율로 정확히 나뉘지 않거나 오버헤드가 생길 수 있어 용량 산정이 중요하다.
    - 물리 장비의 인터페이스 수가 적으면 영역 분리 자체가 어려울 수 있다.
    - 물리 장비의 기능이 논리로 나뉘어도 동일하게 제공되는지 확인이 필요하다.
- 무작정 줄이는 설계는 문제를 만든다.
    - 비용을 줄이려다 장비가 복잡해지고 손실비용이 커질 수 있다.
- 논리 장비는 물리 자원을 공유하므로 동일 역할을 논리로 나눠도 물리 단일 장애점이 남을 수 있다.
    
    <img width="533" height="208" alt="image" src="https://github.com/user-attachments/assets/b860e669-bf51-413d-af4c-bca58fbbf2f6" />

    <img width="556" height="217" alt="image" src="https://github.com/user-attachments/assets/2adc130c-d193-4df3-90cd-cc86a6d6882f" />
 

## 14.2 벤더별 장비 가상화 기술: 하나의 논리 장비로 만드는 가상화

### 14.2.1 시스코 시스템즈

- VSS
    - VSS(Virtual Switching System)는 VSL(Virtual Switching Link)로 두 장비를 연결해 하나의 가상 스위치처럼 만든다.
    - 일부 Catalyst 계열에서 지원하며 최대 두 대를 한 논리 장비로 구성한다.
        
        <img width="418" height="186" alt="image" src="https://github.com/user-attachments/assets/cd062da6-e747-4a44-9c94-6cda3f56d662" />

- StackWise와 FlexStack
    - 모델에 따라 여러 대 스위치를 스택으로 구성해 한 가상 스위치처럼 운영한다.
    - 역할이 Active, Hot Standby, Member로 나뉜다.
    - 데이지 체인 형태의 케이블 구성이 사용된다.
        
        <img width="482" height="291" alt="image" src="https://github.com/user-attachments/assets/4af3009f-d8f6-49a8-be89-df42ceacabab" /> <br>

        <img width="243" height="238" alt="image" src="https://github.com/user-attachments/assets/44291393-5930-4970-bd49-a6c6c118e18b" />

- FEX
    - 여러 장비를 하나로 묶는 것처럼 보이지만, 구조적으로는 상단 스위치의 확장 모듈처럼 동작하는 방식이다.
    - FEX 자체는 별도 운영체제가 없고 관리가 상단 스위치에 집중된다.
        
        <img width="415" height="188" alt="image" src="https://github.com/user-attachments/assets/c43fa1a1-e1ed-4c49-8ec3-9726f2bcc715" />


### 14.2.2 주니퍼

- 가상 섀시
    - 여러 스위치를 링 형태로 연결해 하나의 장비처럼 운영하는 방식이다.
    - 모델에 따라 전용 포트가 있거나 업링크 포트를 활용한다.
- VCF
    - 스파인-리프 형태의 패브릭으로 더 큰 규모까지 확장하는 개념이다.
    - 최대 Pod 규모에 적합한 형태이다.
- 주노스 퓨전
    - VCF보다 더 큰 규모에 적용할 수 있는 개념이다.
    - Provider Edge, Data Center, Enterprise로 아키텍처가 나뉜다.

### 14.2.3 익스트림

- VCS
    - 스위치 간 ISL만 구성하면 패브릭으로 자동 묶이는 개념이다.
    - 스케일 아웃에 유리한 형태이다.
        
        <img width="552" height="324" alt="image" src="https://github.com/user-attachments/assets/2b79cd46-857b-4706-bbda-fcf8537e8aa3" />
 

### 14.2.4 HP 엔터프라이즈

- IRF
    - 여러 대 스위치를 하나의 가상 스위치로 구성하는 방식이다.
    - 박스형 장비는 더 많은 대수까지, 섀시형은 더 적은 대수까지 구성할 수 있다.
        
        <img width="574" height="259" alt="image" src="https://github.com/user-attachments/assets/fecf1199-4af1-411c-8189-9385a058b4e5" />


## 14.3 벤더별 장비 가상화 기술: 여러 개의 논리 장비로 만드는 가상화

### 14.3.1 시스코 시스템즈

- VDC
    - Nexus 계열에서 하나의 물리 스위치를 여러 VDC로 분할해 사용한다.
    - VDC별로 독립적인 설정과 프로세스가 동작한다.
    - VDC별 권한 분리와 전체 관리용 Admin VDC 개념이 있다.
    - VDC 간 데이터 트래픽은 완전히 분리되므로 VDC 간 통신은 물리 연결과 별도 구성이 필요하다.
        
        <img width="426" height="354" alt="image" src="https://github.com/user-attachments/assets/4f3d32d1-8642-424b-82b3-1f36e28ec9e8" />


### 14.3.2 시트릭스

- NetScaler 계열에서 MPX와 VPX가 있다.
    - MPX는 하드웨어 장비로 제공되는 형태이다.
    - VPX는 소프트웨어 인스턴스 형태이다.
- SDX
    - 하이퍼바이저 기반 하드웨어 박스 위에 여러 VPX 인스턴스를 올려 논리 L4, L7 장비를 분리 운영한다.
    - 인스턴스별로 CPU, 메모리, SSL 등을 독립적으로 운영할 수 있다.
    - 전체를 관리하는 SVM 개념이 있다.
        
        <img width="296" height="187" alt="image" src="https://github.com/user-attachments/assets/81f7616c-c3ff-411b-8eb5-4dbd97075b0a" />


### 14.3.3 F5

- vCMP가 있다.
    - 하나의 물리 장비를 여러 Guest로 나누어 동작시키는 방식이다.
    - Guest별로 CPU 코어, 메모리 등을 할당해 독립 운영한다.
    - 서로 다른 운영체제 버전으로 기능 테스트나 검증에 활용할 수 있다는 맥락이 포함된다.
        
        <img width="539" height="274" alt="image" src="https://github.com/user-attachments/assets/fa0f7168-07d5-4734-8ec0-f32f9453d3f2" />
 

### 14.3.4 포티넷

- VDOM
    - 방화벽 기능을 라우팅만 나누는 수준을 넘어 정책 단위까지 분리해 논리 방화벽처럼 운영하는 개념이다.
    - 라우팅뿐만 아니라 정책 단위로 시스템 전체를 가상화한다.
        
        <img width="227" height="213" alt="image" src="https://github.com/user-attachments/assets/61fc5a6b-2784-4705-bd1d-ac8cc753fb69" />
