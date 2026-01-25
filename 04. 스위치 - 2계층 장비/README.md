# 04. 스위치: 2계층 장비

## **4.1 스위치 장비 동작**

- 스위치는 네트워크에서 단말 간 통신을 중재하는 장비다.
- 여러 단말이 동시에 통신하더라도 충돌과 대기 문제를 줄여 전체 효율이 좋아진다.
    
    <img width="466" height="231" alt="image" src="https://github.com/user-attachments/assets/f6897179-5c5b-486e-91e8-e923bc10c626" />
    
- 스위치는 2계층에서 동작하고 MAC 주소 기반으로 프레임을 전달한다.
- 스위치가 특정 단말 위치를 찾는 핵심은 MAC 주소 테이블이다.
    - MAC 주소와 연결 포트를 매핑해 저장한다.
        
        <img width="463" height="174" alt="image" src="https://github.com/user-attachments/assets/7802229c-6558-4348-ae9b-69ffa2471c52" />
        
    - 목적지 MAC 주소가 테이블에 있으면 해당 포트로만 보낸다.
        
        <img width="643" height="266" alt="image" src="https://github.com/user-attachments/assets/eb25e3fc-d02d-47e3-8892-c7d88ff23d0c" />
        

- 스위치 동작은 크게 3가지로 구분된다.
    - 플러딩
    - 어드레스 러닝
    - 포워딩과 필터링

### **4.1.1 플러딩**

- 스위치를 처음 붙이면 MAC 주소 테이블이 비어 있다.
- 목적지 MAC 주소를 모르면 들어온 포트를 제외한 모든 포트로 프레임을 뿌린다.
    
    <img width="666" height="236" alt="image" src="https://github.com/user-attachments/assets/aa2dc9b5-1d8b-42dc-aa40-9d844f29a7cf" />
    
    <img width="671" height="301" alt="image" src="https://github.com/user-attachments/assets/2de93dd0-4b2c-4cbb-a4cb-152fca338c28" />
    
- 이런 플러딩이 계속 많아지면 스위치가 제역할을 하지 못한다. 그래서 패킷이 스위치에 들어오면 MAC 주소 테이블을 만들어 학습한다.

### **4.1.2 어드레스 러닝**

- 스위치는 들어오는 프레임의 출발지 MAC 주소를 보고 테이블을 채운다.
- 어느 포트에서 어떤 MAC 주소가 들어왔는지 저장해서 다음부터 목적지 포트를 찾는다.
    
    <img width="659" height="268" alt="image" src="https://github.com/user-attachments/assets/478514d6-b499-4fe9-8747-da8c87205234" />
    
- 브로드캐스트나 멀티캐스트는 출발지 MAC 학습이 의미가 없어서 테이블에 반영되지 않는다.
- 스위치 내부적으로 미리 정의된 MAC 주소도 있고 이런 항목은 CPU나 관리 모듈 같은 내부 처리를 가리키기도 한다.

### **4.1.3 포워딩/필터링**

- 목적지 MAC 주소가 테이블에 있으면 그 포트로만 프레임을 보낸다.
- 목적지와 무관한 포트로는 보내지 않아 트래픽이 줄어든다.
    
    <img width="632" height="330" alt="image" src="https://github.com/user-attachments/assets/de02f486-3afa-4e51-b5f7-3e744dd4da7f" />
    
- 스위치는 일반적으로 유니캐스트에 대해서만 포워딩과 필터링을 수행한다.
- 브로드캐스트, 언노운 유니캐스트, 멀티캐스트는 테이블 기준으로 다르게 동작하고 기본은 플러딩 성격이 강하다.
- LAN에서 ARP와 스위치 동작이 자연스럽게 이어진다.
    - 통신 시작 전 ARP 브로드캐스트가 먼저 흐른다.
    - 이 과정에서 스위치가 출발지 MAC 을 학습하고 이후 유니캐스트가 시작되면 포워딩과 필터링이 안정적으로 돈다.
        
        <img width="703" height="518" alt="image" src="https://github.com/user-attachments/assets/8b749cff-2fc3-4fb5-90ca-cf746d3678e1" />
        
    - MAC 테이블은 시간이 지나면 지워지는데 이 시간을 에이징 타임이라 한다.
    - 일반적으로 ARP 캐시보다 MAC 테이블 에이징이 더 길게 잡혀 효율적이다.

## **4.2 VLAN**

### **4.2.1 VLAN이란?**

- VLAN은 물리 배치와 상관없이 하나의 스위치 또는 스위치 묶음을 논리적으로 여러 네트워크로 나누는 기술이다.
- 같은 스위치에 붙어 있어도 VLAN이 다르면 같은 네트워크가 아니다.
    
    <img width="539" height="232" alt="image" src="https://github.com/user-attachments/assets/72fe7b28-6266-4514-a8c2-3fdac106ef79" />
    
- 서로 다른 VLAN 간 통신은 3계층 장비가 필요하다. VLAN은 기본적으로 2계층 분리라서 VLAN 간 라우팅이 따로 필요하다.
    
    <img width="478" height="297" alt="image" src="https://github.com/user-attachments/assets/58d7945a-fdeb-4171-b266-a934eb089126" />
    
- 다른 층에 있어도 같은 VLAN이면 같은 네트워크처럼 묶을 수 있다.
    
    <img width="529" height="359" alt="image" src="https://github.com/user-attachments/assets/54bbb856-f78f-4977-b6b1-23627240ae80" />
    
- VLAN은 브로드캐스트 도메인을 분리한다.
    - 브로드캐스트가 VLAN 밖으로 넘어가지 않는다.
    - 보안과 성능에 도움이 된다.

### **4.2.2 VLAN의 종류와 특징**

- 포트 기반 VLAN이 일반적이다.
    - 스위치 포트에 VLAN을 할당한다.
    - 단말이 어느 포트에 꽂히는지가 기준이 된다.
- MAC 기반 VLAN도 있다.
    - 단말 MAC 주소를 기준으로 VLAN을 할당한다.
    - 사용자가 이동해도 같은 VLAN을 유지하기 쉬워 다이내믹 VLAN이라고도 부른다.
        
        <img width="628" height="335" alt="image" src="https://github.com/user-attachments/assets/fb45fc80-f425-43b0-9f00-e53aea060024" />
        
        <img width="629" height="336" alt="image" src="https://github.com/user-attachments/assets/aa6f802f-1443-4587-b823-019f3469d3d8" />
        
- 데이터 센터에서는 보통 포트 기반 VLAN 구성이 일반적이다.
    - 사무 환경에서 이동성이 크면 MAC 기반이 쓰이기도 한다.

### **4.2.3 VLAN 모드(Trunk/Access) 동작 방식**

- 한 대의 스위치에서 VLAN을 여러 개 쓰는 건 쉬운데 스위치끼리 VLAN을 확장하려면 문제가 생긴다.
    - VLAN 10 트래픽은 VLAN 10끼리만 이어져야 한다.
    - VLAN 개수만큼 링크를 따로 연결하면 포트가 너무 많이 든다.
        
        <img width="691" height="519" alt="image" src="https://github.com/user-attachments/assets/439d314d-2fdb-4db8-a717-bafbbe491520" />
        
        <img width="681" height="369" alt="image" src="https://github.com/user-attachments/assets/b2eaeb1e-4d96-4565-8bc5-0bd5cfb8a0cb" />
        
- 이 문제를 푸는 방식이 태그 기반 전달이다.
    - 한 포트로 여러 VLAN 트래픽을 함께 실어 보낸다.
    - 프레임에 VLAN ID 정보를 붙여서 구분한다.
        
        <img width="608" height="360" alt="image" src="https://github.com/user-attachments/assets/c1a5701d-6970-4f38-953e-f23d59f477c6" />
        
    - 태그를 붙여 보내는 포트를 태그(Tagged) 포트 또는 트렁크(Trunk) 포트라고 부른다.
    - 단말이 붙는 일반 포트는 보통 언태그 상태로 한 VLAN만 처리하고 액세스 포트라고 부른다.
- 일반 이더넷 프레임과 태그 프레임 구조가 다르다.
    - 태그 프레임에는 VLAN ID 관련 필드가 추가된다.
        
        <img width="529" height="391" alt="image" src="https://github.com/user-attachments/assets/ce402c4e-ecdb-4673-8fd8-d0da29fb9b4e" />
        
- 태그 포트로 들어온 프레임은 VLAN ID 기준으로 적절한 VLAN으로 분기된다.
    
    <img width="524" height="286" alt="image" src="https://github.com/user-attachments/assets/dd260c44-a8ae-41da-bae8-ff81d13e7ebc" />
    
    <img width="700" height="403" alt="image" src="https://github.com/user-attachments/assets/36128bd7-9977-4b21-bc81-8eb40d206f58" />
    
- VLAN이 있으면 MAC 주소 테이블도 VLAN 단위로 분리되는 효과가 난다.
    - 같은 MAC 이라도 VLAN이 다르면 별개 엔트리처럼 관리한다.
- 가상화 환경에서는 서버 내부에 가상 스위치가 있고 물리 스위치와 트렁크로 묶이는 구성이 흔하다.
    
    <img width="500" height="315" alt="image" src="https://github.com/user-attachments/assets/675b40f7-424b-4c6f-87a3-a14d6af0e7c8" />
    

## **4.3 STP**

- 이중화는 장애를 줄이지만 스위치를 이중 연결하면 루프가 생길 수 있다.
- 루프가 생기면 프레임이 계속 돌면서 네트워크를 마비시킨다.

### **4.3.1 루프란?**

- 루프는 네트워크가 고리 형태로 연결돼 프레임이 순환하는 상태다.
    
    <img width="694" height="289" alt="image" src="https://github.com/user-attachments/assets/5d778e64-1a9a-434d-ad6a-aeccd31c1987" />
    

### **4.3.1.1 브로드캐스트 스톰**

- 단말에서 브로드캐스트를 발생시키면 스위치는 이 패킷을 유입된 포트를 제외한 모든 포트로 플러딩한다.
- 루프가 있으면 브로드캐스트가 스위치 사이를 계속 순환한다.
    
    <img width="452" height="239" alt="image" src="https://github.com/user-attachments/assets/6b0752d3-62fc-4919-9631-3f349a407d43" />
    
- 증상으로는 다음을 체감한다.
    - 단말 접속이 느려진다.
    - 거의 통신이 안 되는 수준이 된다.
    - 스위치 LED가 동시에 빠르게 깜빡이는 상황이 나온다.

### **4.3.1.2 스위치 MAC 러닝 중복 문제**

- 루프 구조 상태에서는 브로드캐스트뿐만 아니라 유니캐스트도 문제를 일으킨다.
- 루프가 있으면 같은 출발지 MAC 이 여러 포트에서 번갈아 들어온다.
- 스위치가 MAC 테이블을 계속 갱신하면서 어느 포트가 맞는지 흔들린다.
- 이 현상을 MAC 어드레스 플래핑이라고 부른다.
    
    <img width="549" height="446" alt="image" src="https://github.com/user-attachments/assets/8fb14e6c-1daa-4fa8-80f1-7744226d416c" />
    
- 플래핑이 심하면 포워딩이 깨지고 플러딩이 늘어 네트워크가 더 불안정해진다.
- 루프를 막기 위해 포트 하나를 수동으로 차단해 고리를 끊을 수 있다.
    
    <img width="475" height="252" alt="image" src="https://github.com/user-attachments/assets/61e6ee55-7594-4a0b-b0c7-d9d4b191b39b" />
    
- 다만 수동 대응은 찾기도 어렵고 장애 시 다시 손으로 복구해야 해서 운영에 불리하다.
- 그래서 자동으로 루프를 감지하고 차단하며 장애 시 우회 경로를 여는 프로토콜이 필요하다.
    
    <img width="566" height="261" alt="image" src="https://github.com/user-attachments/assets/01e6df81-4772-4e49-871f-b0f1aa788c86" />
    

### **4.3.2 STP란?**

- STP는 루프가 생기지 않도록 전체 스위치 토폴로지를 트리 구조로 만드는 프로토콜이다.
- 스위치끼리 BPDU를 주고받아 토폴로지 정보를 공유한다.
    
    <img width="435" height="295" alt="image" src="https://github.com/user-attachments/assets/f3caf09d-8b6e-4ba5-9fd4-4039c069575f" />
    
- BPDU에는 스위치 ID 같은 정보가 들어가고 이를 기준으로 루트와 경로가 결정된다.
    
    <img width="444" height="400" alt="image" src="https://github.com/user-attachments/assets/4070a48c-6b23-43e1-95ef-b9e28d6eeea9" />
    

### **4.3.2.1 스위치 포트의 상태 및 변경 과정**

- STP 동작 중 포트는 4가지 상태로 움직인다.
    - Blocking
    - Listening
    - Learning
    - Forwarding
        
        <img width="607" height="95" alt="image" src="https://github.com/user-attachments/assets/4c7fb03a-679e-423f-81e5-62066367fb86" />
        
- 이 때문에 부팅이 빠른 단말이 DHCP를 빨리 받고 싶어도 포트가 아직 포워딩이 아니라서 지연이 생길 수 있다.

### **4.3.2.2 STP 동작 방식**

- STP는 루트 스위치를 하나 정한다.
- 각 스위치는 루트로 가는 최적 경로 포트를 루트 포트로 둔다.
- 각 세그먼트마다 지정 포트를 하나 정해 그쪽만 포워딩하게 둔다.
- 나머지 중복 경로는 차단돼 루프가 끊긴다.
    
    <img width="603" height="192" alt="image" src="https://github.com/user-attachments/assets/19c20756-c22e-4e7d-828f-d3b0ac9ee4a9" />
    
- 빠른 단말 연결을 위해 PortFast 같은 기능을 쓰기도 한다.
    - 단말 연결 포트는 곧바로 포워딩으로 올려 초기 지연을 줄인다.
    - 대신 그 포트에 스위치가 연결되면 루프 위험이 커진다.
    - 그래서 BPDU Guard 같은 보호 기능을 같이 둔다. BPDU가 들어오면 해당 포트를 차단해 루프를 막는다.

### **4.3.3 향상된 STP(RSTP, MST)**

### **4.3.3.1 RSTP**

- STP는 토폴로지 변경 시 복구가 느릴 수 있다.
- RSTP는 복구 시간을 짧게 가져가는 개선 프로토콜이다.
- BPDU 플래그를 더 활용해 스위치 간 상태 합의를 빠르게 만든다.
    
    <img width="388" height="326" alt="image" src="https://github.com/user-attachments/assets/ec0662ff-bb65-4ca5-960f-a419951265a3" />
    
- 토폴로지 변경 전달 방식도 단순 보고 체계보다 직접 전파에 가깝게 움직여 확산이 빨라진다.

### **4.3.3.2 MST**

- 기본 스패닝 트리는 CST로 보고 VLAN과 무관하게 하나만 도는 형태가 된다.
- VLAN이 많아도 스패닝 트리 인스턴스는 하나라 관리 부담은 줄지만 VLAN별 최적 경로를 쓰기 어렵다.
- VLAN마다 별도 트리를 두는 방식이 PVST 계열이다.
    - VLAN별로 로드 셰어링이 가능해진다.
    - 대신 VLAN 수만큼 프로세스 부담이 늘어난다.
- MST는 여러 VLAN을 그룹으로 묶어 그룹 단위로 스패닝 트리를 만든다.
    - PVST보다 부담이 줄고 CST보다 유연해진다.
    - 리전 개념으로 VLAN 묶음을 정의해 운용한다.
- STP 대안 성격의 기능이나 벤더별 프로토콜도 있다.
    - Loop Guard 같은 루프 보호 기능이 있다.
    - BPDU Guard 같은 보호 기능이 있다.
    - SLPP 같은 벤더 루프 방지 프로토콜이 있다.
    - Extreme STP 같은 벤더 프로토콜이 있다.
