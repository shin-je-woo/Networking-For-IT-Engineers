# 02. 네트워크 연결과 구성 요소

## **2.1 네트워크 연결 구분**

- 네트워크는 규모와 관리 범위에 따라 LAN, MAN, WAN으로 구분한다.
- 현재는 대부분 이더넷 기반으로 통합되어 전송 기술보다 관리 범위 기준이 더 중요하다.

<img width="500" height="211" alt="image" src="https://github.com/user-attachments/assets/1b6ae01e-cf12-4481-912e-c59d8a3c322d" />

### **2.1.1 LAN**

- 사용자 내부 네트워크(집, 사무실 등)
- 건물이나 대지 같은 제한된 범위를 직접 구축해 운영하는 네트워크
    
    <img width="619" height="436" alt="image" src="https://github.com/user-attachments/assets/4ce95363-f2ab-4366-9cbd-24fa3916ee28" />
    

### **2.1.2 WAN**

- 멀리 떨어진 네트워크 연결, 인터넷 접속에 사용하는 네트워크
- 통신사업자 회선을 임대해 쓰는 형태가 일반적이다.
    
    <img width="585" height="348" alt="image" src="https://github.com/user-attachments/assets/676a8b90-cfa9-484d-b5b2-14745e42fb4e" />
    

## **2.2 네트워크 회선**

- 원격지 네트워크 연결은 WAN을 사용한다.
- 회선은 공유형(일반 인터넷)과 보장형(전용 회선/인터넷 전용 회선), 가상화(VPN), 대용량 전송(DWDM)으로 나뉜다.

### **2.2.1 인터넷 회선**

- 인터넷 접속은 통신사업자가 제공하는 인터넷 접속 회선을 통해 이뤄진다.
- 가정용은 가입자 구간에서 선로를 공유하는 구조가 많아 사용량에 따라 속도 차이가 날 수 있다.
- 종류 예시
    - 광랜(이더넷), FTTH
    - 동축 케이블 인터넷
    - xDSL(ADSL, VDSL 등)

### **2.2.2 전용 회선**

- 가입자-통신사업자 구간에서 대역폭과 품질을 보장하는 회선
- 본사-지사 같은 기업 간 연결에 주로 사용한다.
    
    <img width="515" height="175" alt="image" src="https://github.com/user-attachments/assets/aa8e687e-ec07-43ab-a1ad-ad0cb0f27abf" />
    
- 전송 기술 기반으로 구분
    - 저속: 음성 전송 기술 기반(64Kbps 단위)
    - 고속: 메트로 이더넷 기반
- LLCF
    - 한쪽 링크 장애를 반대쪽에도 전달해 장애 인지가 되도록 하는 기능
        
        <img width="650" height="476" alt="image" src="https://github.com/user-attachments/assets/def79717-c471-44d5-ad80-b885dc1585cf" />
        

### **2.2.3 인터넷 전용 회선**

- 인터넷 접속 회선에 대해 통신 대역폭을 보장하는 상품
- 가입자가 통신사업자와 전용으로 연결되고, 그 연결이 인터넷으로 이어지는 형태
    
    <img width="508" height="227" alt="image" src="https://github.com/user-attachments/assets/535c5aca-1154-48c2-9f8d-eb858d36fb0c" />
    

### **2.2.4 VPN**

- 물리적으로 전용선이 아니지만 가상으로 직접 연결한 것 같은 효과를 만들어주는 네트워크 기술
- 장거리 전용선 비용을 줄이기 위한 목적에서 사용한다.

### **2.2.4.1 통신사업자 VPN**

- 공용망을 쓰되 가입자를 논리적으로 분리해 전용선처럼 제공
- 가장 대표적인 기술이 MPLS VPN이다.
- 이 기술을 이용하면 여러 가입자가 하나의 망에 접속해 통신하므로 공용 회선을 함께 이용하게 되어 비용이 낮아진다.
    
    <img width="701" height="304" alt="image" src="https://github.com/user-attachments/assets/32821786-b9b9-4bf8-b790-c6100800cf74" />
    

### **2.2.4.2 가입자 VPN**

- 일반 인터넷을 통해 사용자가 직접 가상 전용망을 구성하는 방식
    
    <img width="421" height="206" alt="image" src="https://github.com/user-attachments/assets/13eee08f-2250-4992-9772-684dec656fd1" />
    

### **2.2.5 DWDM**

- 하나의 광 케이블에 여러 파장(채널)을 실어 대용량 전송을 하는 기술
- 케이블을 추가로 포설하지 않고도 용량을 키우는 방식

## **2.3 네트워크 구성 요소**

- 구성 요소는 연결(물리)과 중계(스위칭/라우팅)로 나뉜다.
- 장비마다 동작 계층이 달라 확인하는 주소(MAC, IP, 포트)와 처리 방식이 달라진다.

### **2.3.1 네트워크 인터페이스 카드(NIC)**

- 컴퓨터를 네트워크에 연결하는 하드웨어
- PC/노트북은 온보드가 일반적, 서버는 고대역폭 요구로 별도 장착이 많다.
    
    <img width="704" height="265" alt="image" src="https://github.com/user-attachments/assets/4a0aa34f-a53f-41b8-8ac2-ba6786b52fcc" />
    
- 핵심 기능
    - 전기적 신호 ↔ 데이터 신호 변환(직렬화)
    - MAC 주소 기반 수신 처리
    - 흐름 제어(Flow Control): 데이터 유실 방지를 위해 데이터를 받지 못할 때는 통신 중지를 요청

### **2.3.2 케이블과 커넥터**

- 케이블은 거리/속도/환경 요구에 따라 선택한다.
- 종류: 트위스티드 페어, 동축, 광
    
    <img width="637" height="232" alt="image" src="https://github.com/user-attachments/assets/b08159f2-153d-49c8-ac1a-93ff74d15cbc" />
    

### **2.3.2.1 이더넷 네트워크 표준**

- 현장에서 가장 많이 쓰는 표준은 1G/10G, 상위는 40G/100G까지 확장된다.
    
    <img width="474" height="273" alt="image" src="https://github.com/user-attachments/assets/d620c45c-39db-425f-9415-3eb3f39d1000" />
    
- 표준 예시
    - 1000BASE-T, 10GBASE-T(구리)
    - 1000BASE-SX/10GBASE-SR(멀티모드)
    - 1000BASE-LX/10GBASE-LR(싱글모드)
        
        <img width="351" height="163" alt="image" src="https://github.com/user-attachments/assets/1b8d6fda-b376-491d-a62a-b3e687c43f50" />


### **2.3.2.2 케이블, 커넥터 구조**

- 케이블 구성 요소: 케이블 본체, 커넥터, 트랜시버
- 구리는 일체형이 많고, 광은 트랜시버 분리 구성이 많다.
    
    <img width="592" height="230" alt="image" src="https://github.com/user-attachments/assets/c69f79f2-7201-47d1-b60d-036d84dfbb83" />
    

### **2.3.2.3 트위스티드 페어 케이블**

- 가장 흔한 케이블
- 쉴드 장착 유무로 UTP, STP/FTP, S/FTP 등을 구분한다.
    
    <img width="406" height="203" alt="image" src="https://github.com/user-attachments/assets/15b8fa8d-0245-4396-b6f1-1b02545e38a9" />
    
    <img width="743" height="298" alt="image" src="https://github.com/user-attachments/assets/7a10f930-f2b1-450b-a347-ba5f9434977a" />


### **2.3.2.4 동축 케이블**

- 케이블 TV 계열에서 사용
- 고속 연결에서 트랜시버 일체형 DAC 케이블이 있다.

### **2.3.2.5 광케이블**

- 장거리/고대역폭에 사용
- 싱글모드/멀티모드로 구분

### **2.3.2.6 커넥터**

- 트위스티드 페어: RJ-45
- 광: ST, SC, FC, LC 등 다양한 형태
    
    <img width="520" height="226" alt="image" src="https://github.com/user-attachments/assets/98e7660a-5386-4a7c-b46e-9b5ebee18135" />


### **2.3.2.7 트랜시버**

- 외부 신호를 장비 내부 신호로 변환하는 모듈
- GBIC, SFP, SFP+ 같은 폼팩터가 있다.
    
    <img width="505" height="235" alt="image" src="https://github.com/user-attachments/assets/448f2f99-8f79-4846-bde9-ccd0f60a1142" />
    
- 참고
    - DAC/AOC: 데이터센터 고속 연결에 사용
    - 브레이크아웃 케이블: 40G/100G를 여러 회선으로 분할(그림 2-22, 그림 2-23)
    - MPO: 다심 광섬유를 단일 커넥터로 연결(그림 2-24)

### **2.3.3 허브**

- 1계층 장비
- 신호 재생(거리로 약해지는 신호 보완) 목적
- 모든 포트로 신호를 보내는 특성으로 성능/장애 측면에서 현재는 사용이 줄었다.

### **2.3.4 스위치**

- 2계층 장비
- MAC 주소 기반으로 목적지 포트로 전달한다.
    
    <img width="712" height="316" alt="image" src="https://github.com/user-attachments/assets/f3e69a24-1451-4a00-bd21-6124b8342476" />
    

### **2.3.5 라우터**

- 3계층 장비
- IP 기반으로 다른 네트워크로 전달하기 위한 경로를 선택한다.
    
    <img width="449" height="280" alt="image" src="https://github.com/user-attachments/assets/cb7564a1-29d8-4d0f-9281-c697dfd688de" />
    

### **2.3.6 로드 밸런서**

- 4계층에서 동작하는 형태가 일반적
- 4계층 정보(포트 등)와 IP를 기준으로 트래픽을 분산하는 개념

### **2.3.7 보안 장비(방화벽/IPS)**

- 보안 목적 장비는 정보 전달보다 통제와 방어에 초점이 있다.
- 방화벽은 4계층에서 동작하며 3, 4계층 정보를 정책과 비교해 허용/차단을 수행한다.

### **2.3.8 기타(모뎀/공유기 등)**

- 공유기: 2계층 스위치, 3계층 라우터, 4계층 NAT, 간단한 방화벽 기능을 한 장비에 통합한 형태
- 내부적으로는 스위치 부분, 무선 AP 부분, 라우터 부분 회로로 구성
- 모뎀: 짧은 거리 통신 기술과 먼 거리 통신 기술이 다를 때 변환을 담당하는 장비
- 접속 기술에 따라 모뎀 종류가 달라진다. (FTTH, 케이블 모뎀, ADSL/VDSL 등)
