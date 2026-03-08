# 13. 네트워크 디자인

## **13.1 2계층/3계층 네트워크**

### **13.1.1 2계층 네트워크**

- 호스트 간 통신이 L2(스위칭)로만 이뤄지는 구조다.
- 모든 장비가 동일 브로드캐스트 도메인에 묶인다. 게이트웨이를 거치지 않고 호스트 간 직접 통신이 가능한 구조이다.
- 루프 구조가 생기면 문제가 발생하므로 스패닝 트리 프로토콜(STP)을 사용해 문제를 해결한다. STP 사용으로 블록 포인트가 생기면서 인프라 대역폭을 충분히 사용하지 못하는 문제가 있다.
- 해결 방향은 루프 없는(non-blocking) 구조를 만들거나 다른 구조로 전환하는 것이다.
    
    <img width="485" height="287" alt="image" src="https://github.com/user-attachments/assets/4a7f738a-d3ef-4d24-9553-9b2e89e5fc4c" />

    <img width="620" height="227" alt="image" src="https://github.com/user-attachments/assets/9b55af44-201c-4933-9f2d-62231dc649d8" />


### **13.1.2 3계층 네트워크**

- L3(라우팅) 기반으로 네트워크를 분리해 확장성과 장애 격리를 얻는 구조다.
- 네트워크가 다르면 브로드캐스트로 직접 통신할 수 없어 라우팅이 필요하다.
- 라우팅을 통해 경로 제어와 장애 범위 축소가 가능하다.
- 단, 동일 네트워크를 유지하면서도 L3 장점을 얻고 싶다면 오버레이(예 VXLAN, GRE) 같은 기술이 필요하다.
    
    <img width="491" height="300" alt="image" src="https://github.com/user-attachments/assets/89afcbaf-85e0-4f91-84e0-eaf32795d45c" />

    <img width="487" height="321" alt="image" src="https://github.com/user-attachments/assets/96bfb765-e680-46fd-aa81-6fb84016fb4f" />


## **13.2 3-Tier 아키텍처**

- 전통적인 데이터센터 네트워크 설계 방식으로 코어-어그리게이션-액세스 3계층으로 구성한다.
- 상위(코어)로 갈수록 대역폭 요구가 커지고 병목이 생기기 쉽다.
- 업링크 비율(oversubscription ratio)을 설계 핵심으로 본다.
- 사용자 요청 응답 트래픽(North-South)이 중심이던 시기에 적합했다.
    
    <img width="653" height="308" alt="image" src="https://github.com/user-attachments/assets/e1e43305-58fc-425a-97f0-b4b8f774948f" />


## **13.3 2-Tier 아키텍처**

### **13.3.1 스파인-리프 구조**

- 스파인(상단)과 리프(하단) 2계층으로 단순화한 데이터센터 설계다.
- 리프에 서버가 붙고 리프-스파인 간 다중 링크로 대역폭을 확장한다.
- 링크를 막지 않고 모두 쓰는 방향을 지향한다.
- 트래픽이 North-South에서 East-West로 이동한 현재 환경에 유리하다.
- 가상화, 분산처리, 마이크로서비스 확산으로 서버 간 통신이 급증한 요즘 환경에 적합하다.
- 모든 호스트 간 통신 홉이 동일하다. 출발지 리프 스위치를 지나 스파인 스위치를 거쳐 목적지 스위치로 트래픽이 흘러 홉 수가 짧아지고 트래픽 흐름이 일정하다는 장점이 있다.

<img width="655" height="302" alt="image" src="https://github.com/user-attachments/assets/d9919340-fdfb-4d7b-a28f-83669f4c2bef" />

<img width="656" height="399" alt="image" src="https://github.com/user-attachments/assets/807f6cc5-d8a5-46c8-88b6-7365ecd9afb7" />

### **13.3.2 L2 패브릭**

- 스파인-리프를 L2로 구성해 패브릭처럼 동작시키는 방식이다.
- STP로는 모든 링크를 활성화하기 어려워 TRILL, SPB 같은 기술이 사용된다.
- 목표는 루프 없이 모든 경로를 쓰는 L2 기반 패브릭이다.
    
    <img width="669" height="317" alt="image" src="https://github.com/user-attachments/assets/a7541963-8f54-4815-855c-5cdb97a4dc92" />


### **13.3.3 L3 패브릭**

- 스파인-리프를 L3로 구성해 ECMP 등으로 다중 경로를 활용한다.
- 브로드캐스트 범위를 줄이고 장애 격리를 강화한다.
- 동일 네트워크가 필요하면 VXLAN 같은 오버레이로 L2 세그먼트를 얹는다.
    
    <img width="660" height="312" alt="image" src="https://github.com/user-attachments/assets/303f1e5b-7ae6-4bdb-8c38-36289dd655a1" />


## **13.4 데이터 센터 Zone/Pod 내부망/DMZ 망/인터넷망**

### **13.4.1 인터넷망**

- 외부 사용자가 접근하는 구간이며 ISP 연결을 통해 인터넷과 연동된다.
- 규모가 커지면 단일 회선이 아니라 이중화/다중화 구성이 필요해진다.

### **13.4.2 공인망(DMZ)**

- 외부에 제공하는 서비스 서버가 위치하는 구간이다.
- 언트러스트(인터넷)와 트러스트(내부망) 사이 완충 지대 성격이다.
- 외부 노출이므로 보안 장비와 정책 설계가 핵심이다.

### **13.4.3 내부망(사내망/사설망)**

- 데이터센터 내부에서만 접근하는 네트워크다.
- 외부에서 직접 접근을 막고 필요한 경우에만 통제된 경로를 둔다.
- 보안 및 운영 효율을 위해 망 분리가 중요하다.

### **13.4.4 데이터베이스망**

- DB는 중요 정보가 많아 별도 망으로 분리해 보호하는 경우도 있다.
- DB 접근은 방화벽 등으로 최소 허용 원칙으로 통제한다.
- 보안 강화 목적의 별도 접근 통제 시스템과 연동될 수 있다.

### **13.4.5 대외망**

- 회사 내부에서 외부와 연동이 필요한 경우 사용하는 구간이다.
- 인터넷망을 그대로 쓰기도 하지만 별도 전용선이나 VPN으로 분리 운용하기도 한다.

### **13.4.6 관리망/OoB(Out of Band)**

- 서버/네트워크 장비의 관리용 인터페이스를 분리해 운용하는 망이다.
- 장애 시에도 장비 관리가 가능하도록 별도 경로로 구성하는 것이 목적이다.
- 장비 벤더별 관리 포트/기능이 있다. (예 iLO, iDRAC, IMM)

## **13.5 케이블링과 네트워크**

### **13.5.1 ToR**

- 랙 상단(Top of Rack) 스위치에 서버를 수용하는 방식이다.
- 서버-스위치 케이블이 짧아 관리가 단순해진다.
- 다만 랙 단위로 스위치가 늘어나 관리 대상이 많아질 수 있다.
    
    <img width="482" height="180" alt="image" src="https://github.com/user-attachments/assets/8f29c4f5-f80f-4e0d-8671-cd46ea969bfd" />


### **13.5.2 EoR**

- 열(ROW) 끝(End of Row)에 스위치를 두고 여러 랙을 수용한다.
- 스위치 수를 줄여 관리 부담을 낮출 수 있다.
- 대신 케이블이 길어지고 복잡해져 증설/변경 시 비용이 커질 수 있다.
    
    <img width="492" height="207" alt="image" src="https://github.com/user-attachments/assets/096c599d-08e2-4c70-a47f-fd1333143f8c" />


### **13.5.3 MoR**

- 열 중간(Middle of Row)에 스위치를 두는 방식이다.
- EoR 대비 케이블 길이를 줄이려는 절충안 성격이다.
    
    <img width="489" height="208" alt="image" src="https://github.com/user-attachments/assets/d7d77896-85a6-4beb-9b16-29d379ce2363" />
