# 라우터/L3 스위치: 3계층 장비

## **5.1 라우터의 동작 방식과 역할**

- 라우터는 3계층에서 목적지 IP를 보고 다음 홉을 결정해 패킷을 전달하는 장비다.
- 스위치는 2계층 장비지만 3계층 기능을 수행하는 L3 스위치도 많이 쓴다.
- 하드웨어 스위칭 성능이 좋아지면서 라우터와 L3 스위치를 기능만으로 구분하기가 점점 애매해진다.

### **5.1.1 경로 지정**

- 라우터의 핵심은 경로 정보로 라우팅 테이블을 만들고 목적지 IP를 기준으로 포워딩하는 일이다.
- 라우터가 하는 일은 크게 둘로 나뉜다.
    - 경로 정보를 얻어 라우팅 테이블을 만든다.
    - 라우팅 테이블로 패킷을 포워딩한다.
- 경로 정보는 다음 방식으로 얻는다.
    - 인터페이스에 IP를 넣으며 인접 네트워크를 자연스럽게 학습한다.
    - 관리자가 직접 경로를 넣는다.
    - 라우터끼리 프로토콜로 경로를 교환한다.
        
        <img width="582" height="405" alt="image" src="https://github.com/user-attachments/assets/c8e50950-810f-4a2f-b0ed-01306714ee5c" />
        

### **5.1.2 브로드캐스트 컨트롤(Broadcast Control)**

- 스위치는 목적지 MAC을 모르면 플러딩으로 전체 포트에 뿌린다.
- LAN에서는 규모가 작거나 NIC에서 걸러지기도 해서 플러딩 영향이 제한적일 수 있다.
- 라우터는 원격지로 보내는 것이 목적이라 브로드캐스트를 다른 네트워크로 전달하지 않는 것이 기본 동작이다.
- 그래서 라우터를 경계로 브로드캐스트 도메인이 분리되고 전체 네트워크 부하를 줄일 수 있다.

### **5.1.3 프로토콜 변환**

- 라우터는 3계층 장비라서 들어온 패킷의 2계층 헤더를 벗기고 3계층을 확인한 뒤 나가는 구간에 맞게 2계층 헤더를 새로 씌운다.
- LAN 구간과 WAN 구간의 2계층이 다르면 여기서 프레임 형식이 바뀐다.
    
    <img width="685" height="348" alt="image" src="https://github.com/user-attachments/assets/97290eae-9ecc-4357-bce0-00231ea86423" />
    

## **5.2 경로 지정 - 라우팅/스위칭**

### **5.2.1 라우팅 동작과 라우팅 테이블**

- 인터넷은 출발지부터 목적지까지 전체 경로를 한 장비가 책임지지 않는다.
- 인접 라우터까지의 다음 홉만 정하고 넘기는 홉-바이-홉 방식으로 동작한다.
    
    <img width="605" height="224" alt="image" src="https://github.com/user-attachments/assets/b48efe56-a240-4aa0-9ccc-a62326dddeab" />
    
- 넥스트 홉을 지정하는 방법이 몇 가지 있다.
    - 다음 라우터의 IP를 지정한다. (넥스트 홉 IP 주소)
    - 나가는 인터페이스를 지정한다.
    - 둘을 같이 지정한다.
- WAN 전용선처럼 PPP나 HDLC를 쓰는 구간은 MAC이 의미 없어서 넥스트 홉을 IP로만 두는 방식이 일반적이다.
    
    <img width="655" height="369" alt="image" src="https://github.com/user-attachments/assets/b4345867-7cf6-487d-8604-20769f571dc9" />
    

### **루프와 TTL**

- 라우팅이 잘못되면 두 라우터 사이에서 패킷이 핑퐁처럼 도는 루프가 생길 수 있다.
- 3계층 IP 헤더에는 TTL이 있어 라우터를 한 번 지날 때마다 1씩 줄고 0이 되면 버린다.
- TTL은 시간 개념이 아니라 홉 수 제한에 가깝다.
    
    <img width="613" height="294" alt="image" src="https://github.com/user-attachments/assets/bb8a4d6f-a955-4923-9ac7-663544dd3fff" />
    
    <img width="706" height="289" alt="image" src="https://github.com/user-attachments/assets/0c0a5b33-67b2-4e4b-a101-972dc31bd647" />
    

### **5.2.2 라우팅 (라우터가 경로 정보를 얻는 방법)**

- 라우터가 경로를 얻는 방식은 크게 3가지다.
    - 다이렉트 커넥티드
    - 스태틱 라우팅
    - 다이나믹 라우팅

### **다이렉트 커넥티드**

- 라우터나 PC가 자신의 IP와 서브넷 마스크로 같은 네트워크 주소를 계산하고 해당 네트워크에 대한 경로가 자동으로 생긴다.
- 이 경로는 수동으로 값을 바꾸기 어렵고 인터페이스 비활성화나 설정 삭제 시 사라진다.
    
    <img width="635" height="340" alt="image" src="https://github.com/user-attachments/assets/a5125133-ff93-4afc-af53-3ec26d08f14b" />
    

### **스태틱 라우팅**

- 목적지와 넥스트 홉을 직접 넣어 경로를 만든다.
- 연결된 인터페이스가 내려가면 관련 스태틱 경로가 지워질 수 있다.
- 물리 인터페이스가 내려가도 논리 인터페이스는 남는 경우가 있어 라우팅 테이블에서 완전히 사라지지 않을 수도 있다.
    
    <img width="643" height="419" alt="image" src="https://github.com/user-attachments/assets/9ad107ef-d451-45bc-b669-511e79240679" />
    

### **다이나믹 라우팅**

- 스태틱 라우팅만으로는 회선 단절이나 라우터 장애 시 대체 경로 반영이 어렵다.
- 라우터가 주기적으로 정보를 교환해 토폴로지 변화를 감지하고 우회 경로를 선택한다.
    
    <img width="709" height="457" alt="image" src="https://github.com/user-attachments/assets/deffc3bc-f6a2-4368-b2ec-55bb7616b32f" />
    
    <img width="634" height="447" alt="image" src="https://github.com/user-attachments/assets/f318ecbd-276a-4cda-9543-130ba51ebb93" />
    

### **5.2.3 스위칭 (라우터가 경로를 지정하는 방법)**

- 라우터가 패킷을 받으면 라우팅 테이블에서 목적지로 가는 최적의 경로로 포워딩한다.
- 이 과정을 스위칭이라고 한다.
- 목적지와 완전히 일치하는 경로가 없을 때 가장 많이 쓰는 기준이 롱기스트 프리픽스 매치다.
    
    <img width="612" height="459" alt="image" src="https://github.com/user-attachments/assets/db19f318-bbdc-4d48-adac-b4ab80a68955" />
    

### **롱기스트 프리픽스 매치**

- 여러 경로가 부분적으로 매치되면 더 구체적인 경로를 우선한다.
- 예로 10.0.0.0/8, 10.1.0.0/16, 10.1.1.0/24가 있으면 10.1.1.9는 10.1.1.0/24가 선택된다
- 이 작업은 부하가 될 수 있어 장비가 반복 작업을 줄이기 위한 최적화를 한다.

### **캐시로 부하 줄이기**

- 한 번 스위칭한 결과를 캐시에 저장해 다음 패킷은 라우팅 테이블을 매번 풀스캔하지 않게 한다.
- 단순 목적지 IP만 캐시하는 방식부터 출발지 목적지 포트까지 포함하는 방식까지 다양하다.
- 이런 캐시 개념은 메모리 캐시를 쓰는 방식과 유사하게 이해할 수 있다.

### **5.2.4 라우팅, 스위칭 우선순위**

- 라우팅 테이블에서 최종 경로를 고를 때 기준이 단계적으로 적용된다.
    
    <img width="359" height="380" alt="image" src="https://github.com/user-attachments/assets/108b2bbf-e83b-4b87-b24a-9193ed6db681" />
    

## **5.3 라우팅 설정 방법**

### **5.3.1 다이렉트 커넥티드**

- IP와 서브넷 마스크를 인터페이스에 설정하면 다이렉트 커넥티드 경로가 자동으로 생긴다.
- 이 경로는 로컬 네트워크 통신의 기본이 된다.
- 외부 네트워크로 나가려면 다이렉트 커넥티드만으로는 부족하고 스태틱이나 다이나믹으로 원격 경로가 필요하다.
    
    <img width="560" height="341" alt="image" src="https://github.com/user-attachments/assets/af89c9fd-9e47-4a43-a751-471412b2e28a" />
    

### **5.3.2 스태틱 라우팅**

- 원격지 네트워크로 가려면 목적지 네트워크와 넥스트 홉을 수동으로 넣는다.
- 기본 문법은 장비마다 차이가 있지만 핵심은 목적지와 넥스트 홉 조합이다.
    
    <img width="491" height="272" alt="image" src="https://github.com/user-attachments/assets/4a5cfb99-2173-4a2b-a327-08f4b19084f1" />
    
    <img width="630" height="276" alt="image" src="https://github.com/user-attachments/assets/e5d8b626-9278-47d5-9137-9fc3cfce92a2" />
    

### **디폴트 라우팅**

- 모든 인터넷 경로를 스태틱으로 넣으려면 항목 수가 너무 커진다.
    
    <img width="653" height="389" alt="image" src="https://github.com/user-attachments/assets/9a8529aa-7294-4e29-873a-a1d2a26c8bed" />
    
- 목적지 프리픽스가 0인 경로를 하나 두고 경로가 없는 패킷을 마지막 대안으로 그쪽으로 보낸다.
- 0.0.0.0/0은 서브넷 마스크가 전부 0이라 모든 목적지에 매치된다는 뜻이다.
    
    <img width="660" height="320" alt="image" src="https://github.com/user-attachments/assets/b7827589-73ec-4de1-a647-f64cca5ec657" />
    

### **디폴트 게이트웨이**

- 디폴트 라우팅과 디폴트 게이트웨이는 같은 의미로 많이 쓴다.
- 라우팅 기능이 있는 장비는 디폴트 라우팅이 맞고 라우팅 기능이 없는 장비는 디폴트 게이트웨이 설정이라는 표현을 쓴다.
- PC나 서버에서 기본 게이트웨이를 설정하면 그 장비의 라우팅 테이블에 디폴트 경로가 생긴다.

### **5.3.3 다이나믹 라우팅**

- 네트워크가 커지거나 경로가 여러 개면 스태틱만으로 운영이 힘들다.
- 단일 장애 지점이 생기고 장애 시 관리자가 즉시 수동으로 경로를 바꿔야 한다.
- 다이나믹 라우팅은 라우터끼리 경로 정보를 교환해 자동으로 경로를 갱신한다.
    
    <img width="530" height="328" alt="image" src="https://github.com/user-attachments/assets/82851965-8239-497d-936d-82882656015b" />
    

### **5.3.3.1 역할에 따른 분류**

- 인터넷에는 AS(Autonomous System)라는 자율 시스템이 존재한다.
- SKT, KT, LGU+ 같은 인터넷 사업자가 한 개 이상의 AS를 운영한다.
- AS 내부에서 쓰는 프로토콜을 IGP라 하고 AS 간을 연결하는 프로토콜을 EGP라 한다.
- BGP는 대표적인 EGP다.
    
    <img width="644" height="276" alt="image" src="https://github.com/user-attachments/assets/16f9ae84-75dd-42b3-821b-72099764fbcf" />
    
    <img width="642" height="276" alt="image" src="https://github.com/user-attachments/assets/778da085-323a-485b-b0e3-160ec51250b0" />
    
    <img width="703" height="378" alt="image" src="https://github.com/user-attachments/assets/bcb169a0-e0fa-4c96-b153-2699925e54f6" />
    

### **5.3.3.2 동작 원리에 따른 분류**

- IGP는 동작 방식으로 디스턴스 벡터와 링크 스테이트로 나뉜다.
- 디스턴스 벡터는 인접 라우터가 준 정보로 경로를 배운다.
    - 멀리 있는 정보는 여러 라우터를 거쳐 전달돼 수렴이 느릴 수 있다.
        
        <img width="629" height="220" alt="image" src="https://github.com/user-attachments/assets/5d735461-8c19-41f2-9d93-a342a0d48c7d" />
        
- 링크 스테이트는 링크 상태를 공유해 토폴로지 데이터베이스를 만들고 SPF로 최단 경로 트리를 만든다.
    - 전체 맵을 그리는 작업이라 CPU 메모리 부담이 커질 수 있다.
        
        <img width="694" height="224" alt="image" src="https://github.com/user-attachments/assets/57cf10d0-371c-45b2-871e-5ba298bd2c95" />
        

### **OSPF 에어리어 개념**

- 링크 스테이트 부담을 줄이기 위해 네트워크를 에어리어 단위로 나눠서 운영한다.
- 에어리어 내부는 링크 상태를 공유하고 에어리어 밖으로는 요약된 라우팅 정보 형태로 전달한다.
- 백본 에어리어인 Area 0을 중심으로 다른 에어리어가 연결된다.
- ABR은 Area 0과 다른 에어리어를 연결하는 경계 라우터다.
- ASBR은 OSPF 도메인 밖의 경로를 OSPF로 주입하는 라우터다.
    
    <img width="573" height="253" alt="image" src="https://github.com/user-attachments/assets/7c3cb797-6143-4645-bec0-7204b6b4cced" />
    

### **라우팅 프로토콜 분류**

<img width="708" height="341" alt="image" src="https://github.com/user-attachments/assets/7ef29305-a3f5-4f3b-a8a1-7b6455373613" />
