# 11. 이중화

## 11.1 이중화 기술 개요

### 11.1.1 SPoF

- SPoF(Single Point of Failure)는 시스템 구성 요소 중 하나가 동작하지 않으면 전체 서비스가 중단되는 요소를 말한다.
- 예로 이더넷 케이블, 전원, 허브(HUB), 접속 단말의 NIC(Network Interface Card) 등이 단일 장애점이 될 수 있다.
- 고가용성을 목표로 하는 네트워크, 소프트웨어 애플리케이션, 상용 시스템에서 단일 장애점을 두는 것은 바람직하지 않다.
- 인프라 설계 시 SPoF를 최소화하는 수준이 아니라 애초에 만들지 않도록 설계해야 한다.
    
    <img width="347" height="458" alt="image" src="https://github.com/user-attachments/assets/f8540bef-93c0-443d-b9bf-4530a52e5d85" />
    

### 11.1.2 이중화의 목적

- 이중화는 서비스의 가용성, 연속성, 안정성을 높이기 위한 필수 요소다.
- 서비스는 출발지부터 끝 지점까지(End to End) 속하는 모든 인프라에서 이중화를 고려해야 한다.
- 서버, 스토리지, 네트워크 장비, 네트워크 인터페이스, 스위치(L4 포함), 방화벽, 인터넷 게이트웨이, 인터넷 회선 등 전 구간에 이중화가 필요하다.
- 이중화된 구성 요소가 동시에 운영되는지(Active-Active) 또는 하나는 대기 상태인지(Active-Standby)에 따라 설계와 용량 산정이 달라진다.
    
    <img width="630" height="464" alt="image" src="https://github.com/user-attachments/assets/2b5336f8-4bfa-4654-ae5a-9990be0a734c" />
    
- 이중화를 도입하면 장애 시에도 서비스가 지속되며, 이를 장애 허용(Fault Tolerance)이 보장된다고 말한다.
- 이중화를 Active-Active로 구성하면 처리 가능한 전체 용량이 증가하지만, 장애 시 용량이 절반으로 떨어질 수 있으므로 평상시 용량 산정이 중요하다.
- 데이터 센터 전체 장애에 대비하는 재해복구(DR) 센터 구성이나 액티브-액티브 데이터 센터 구성도 이중화의 연장선이다.

## 11.2 LACP

- LACP(Link Aggregation Control Protocol)는 여러 물리 인터페이스를 하나의 논리 인터페이스로 묶어 대역폭을 확장하고 장애 복원력을 높이는 기술이다.
- IEEE 802.1AX 계열 표준으로 정리되었으며, 벤더 종속 구성을 줄이기 위한 표준화 흐름 속에서 발전했다.
- 링크 애그리게이션의 목적은 링크 사용 향상과 장애 복원 향상이다.
- LACP를 사용하면 일부 물리 링크에 문제가 발생해도 나머지 링크로 서비스를 유지할 수 있다.
- LACP는 액티브-스탠바이 구성이 아니라 액티브-액티브 상태로 묶인 링크들을 동시에 사용한다.
- LACP 구성 시 서비스가 요구하는 트래픽을 기준으로 장애 시에도 감당 가능한 대역폭이 남도록 산정해야 한다.
- LACP 논리 인터페이스에 포함되는 물리 인터페이스 속도는 동일해야 한다. 예를 들어 1G와 10G를 섞어 하나의 논리 인터페이스로 만들 수 없다.

### 11.2.1 LACP 동작 방식

- LACP는 장비 간 논리 인터페이스 구성을 위해 LACPDU 프레임을 사용한다.
- LACPDU는 멀티캐스트를 사용하며, 목적지 주소 범위가 정해져 있다.
- LACP는 양쪽 장비 모두에 설정이 있어야 구성된다. 한쪽만 설정된 경우 정상적인 LACPDU 교환이 이루어지지 않아 구성되지 않는다.
    
    <img width="419" height="219" alt="image" src="https://github.com/user-attachments/assets/fb372d95-352f-4011-b257-5329720bd30c" />
    

### 11.2.2 LACP와 PXE

- PXE(Pre boot eXecution Environment)는 운영체제 설치 전 단계에서 동작하므로, 서버가 LACPDU를 송신할 수 없는 상태가 될 수 있다.
- 이때 스위치는 서버로부터 LACPDU를 받지 못해 링크를 정상적으로 묶지 못하고, 제한된 인터페이스만 활성화된 상태로 PXE 부트를 진행하는 상황이 발생할 수 있다.
- 운영체제 설치가 완료되고 서버가 LACPDU를 주고받을 수 있게 되면 LACP 구성이 정상화될 수 있다.
- 이를 보완하기 위해 벤더별로 LACP 인터페이스에서 PXE를 허용하는 기능 명칭과 구현이 존재한다.

## 11.3 서버의 네트워크 이중화 설정(Windows, Linux)

- 서버에서 네트워크 인터페이스 이중화를 구성하면 스위치와 유사하게 논리 인터페이스가 생성된다.
- 운영체제별로 기술 명칭이 다르다.
    - 윈도는 팀(Team), 티밍(Teaming)으로 부른다.
    - 리눅스는 본딩(Bonding), 본드(Bond)로 부른다.
- 서버 측 이중화는 액티브-액티브로 사용할지, 액티브-스탠바이로 사용할지를 중심으로 동작 모드를 고려해야 한다.

### 11.3.1 리눅스 본딩 모드

- 리눅스 본딩 모드는 0부터 4까지가 있으며, 실무에서는 주로 모드 1과 모드 4를 사용한다.
- 모드 1: 액티브-스탠바이
    - 한 인터페이스만 트래픽을 전달하고, 액티브가 죽으면 스탠바이가 자동 활성화되어 패킷을 전송한다.
    - 액티브가 복구되면 설정에 따라 자동으로 다시 액티브로 전환되거나, 수동 전환 전까지 스탠바이가 유지된다.
        
        <img width="603" height="153" alt="image" src="https://github.com/user-attachments/assets/4f691f39-1cd6-4740-a4b9-9644f38413f4" />
        
- 모드 4: LACP
    - 표준 프로토콜 LACP를 이용해 인터페이스를 액티브-액티브로 사용하려면 모드 4로 설정한다.
        
        <img width="600" height="185" alt="image" src="https://github.com/user-attachments/assets/91d8f99b-1add-40b9-bc65-eeed4882f159" />
        

### 11.3.3 리눅스 본드 설정 및 확인

- CentOS에서 본드 설정 및 확인
    - 네트워크 설정 파일 디렉터리에서 bond 인터페이스 파일을 만들고, 각 물리 인터페이스를 bond의 slave로 설정한다.
    - bonding.conf에서 모드와 miimon(링크 상태 점검 주기) 같은 옵션을 설정한다.
    - 모드 1을 사용할 때는 primary 인터페이스 지정이 추가로 필요할 수 있다.
    - 본드 모듈을 적재하고 네트워크를 재시작하여 적용한다.
    - 구성 확인은 bonding 상태 파일을 확인하여 현재 활성 slave, MII 상태, 링크 실패 횟수 등을 확인한다.
    - 본드 인터페이스가 정상 활성화되지 않으면 NetworkManager 중지 등 환경 요인을 점검할 수 있다.
- 우분투에서 본드 설정 및 확인
    - ifenslave 패키지를 설치하고 bonding 모듈이 적재되도록 설정한다.
    - /etc/network/interfaces에서 eth0, eth1을 bond master로 지정하고 bond0에 IP 주소를 부여한다.

## 11.4 MC-LAG

- LACP는 기본적으로 LACPDU를 주고받는 장비가 1대 대 1대로 구성되어야 한다는 제약이 있다.
- 서버가 서로 다른 스위치에 이중화로 연결된 경우, 각 스위치의 MAC 주소가 다르므로 단일 스위치처럼 LACP를 구성하기 어렵다.
- 이 문제를 해결하기 위해 서로 다른 스위치를 하나의 논리 장비처럼 보이게 하여 LACP를 구성하는 기술이 MC-LAG(Multi Chassis Link Aggregation Group)이다.
    
    <img width="469" height="234" alt="image" src="https://github.com/user-attachments/assets/e9c61905-5bca-4065-9947-7afea86f42e4" />
    
- 벤더마다 명칭이 다르며, 동일 개념을 다른 이름으로 제공한다.

### 11.4.1 MC-LAG 동작 방식

- MC-LAG 구성 요소는 다음과 같다.
    - 피어(Peer) 장비는 MC-LAG를 구성하는 두 스위치 장비다.
    - MC-LAG 도메인(Domain)은 두 피어를 하나의 논리 장비로 묶기 위한 영역 ID다.
    - 피어 링크(Peer-Link)는 피어 간 데이터 트래픽을 전달하는 인터링크다.
        
        <img width="529" height="162" alt="image" src="https://github.com/user-attachments/assets/5d11cff3-6202-4b58-89a1-6150ad9d2d41" />
        
- 피어 링크를 VLAN 인터페이스로 구성해 IP로 통신하게 하거나, 별도 인터페이스를 L3로 구성해 제어 패킷을 전달하는 방식 등으로 구현될 수 있다.
- MC-LAG 설정을 마치면 피어 장비는 제어 패킷을 주고받고, 협상이 정상 완료되면 하나의 MC-LAG 도메인으로 동작할 가상 MAC을 생성한다.
- 이후 LACP를 구성할 때 장비 개별 MAC이 아니라 가상 MAC으로 LACPDU를 송신하여, 상대 장비가 두 스위치를 동일한 피어로 인식하게 한다.
- MC-LAG 도메인과 도메인 ID 같은 용어는 이해를 돕기 위한 표현이며, 벤더별 용어와 구현은 다를 수 있다.

### 11.4.2 MC-LAG를 이용한 디자인

- MC-LAG를 사용하면 서로 다른 스위치를 하나의 스위치처럼 인식시켜 서버를 액티브-액티브로 연결할 수 있다.
    
    <img width="479" height="240" alt="image" src="https://github.com/user-attachments/assets/cf6927be-3c0f-477b-80d1-cba643617ef6" />
    
- 루프 구조가 사라져 STP에 의한 차단 없이 포트를 활용할 수 있는 구성을 설계할 수 있다.
    
    <img width="559" height="240" alt="image" src="https://github.com/user-attachments/assets/e5cd5426-2199-499b-9b03-d033df712fc9" />
    
- 스위치 간 MC-LAG를 구성하는 방식에 따라 상단과 하단을 모두 MC-LAG로 구성해 논리적으로 단순화한 구조도 만들 수 있다.
    
    <img width="484" height="238" alt="image" src="https://github.com/user-attachments/assets/49231db0-9140-42b3-8016-5cbfd2a17666" />
    

## 11.5 게이트웨이 이중화

### 11.5.1 게이트웨이 이중화란?

- 동일 서브넷 내부 통신은 ARP를 브로드캐스트해 목적지와 직접 통신하며, 실무에서는 이를 L2 통신이라고 부르기도 한다.
- 목적지가 다른 네트워크인 경우 게이트웨이를 통해 통신하며, 이를 L3 통신이라고 한다.
- 게이트웨이에 장애가 발생하거나, 게이트웨이와 연결된 인터페이스, SPoF 요소, 경로상 하단 장비 등에 문제가 발생하면 외부 네트워크 통신이 끊긴다.
    
    <img width="644" height="480" alt="image" src="https://github.com/user-attachments/assets/7ecc2f47-8dc1-4b95-9fca-2e4ca643d507" />
    
- 이를 해결하기 위해 게이트웨이를 두 대 이상으로 구성해 장애 시에도 동일한 게이트웨이 주소로 통신을 지속하는 프로토콜이 사용되며, 이를 FHRP(First Hop Redundancy Protocol)라고 한다.
    
    <img width="285" height="401" alt="image" src="https://github.com/user-attachments/assets/b8d79267-2482-4e40-9bc0-738fddb39878" />
    

### 11.5.2 FHRP

- FHRP는 게이트웨이 장비를 두 대 이상으로 구성해, FHRP 그룹 내 장비가 동일한 가상 IP를 갖도록 하고, 우선순위로 액티브 역할을 결정하는 프로토콜이다.
- 그룹 내 장비는 물리적으로 다르지만 가상 IP와 가상 IP의 MAC 주소를 동일하게 유지한다.
- 하단 호스트가 게이트웨이 주소(가상 IP)로 ARP 요청을 보내면, 액티브 장비가 응답한다.
    
    <img width="515" height="419" alt="image" src="https://github.com/user-attachments/assets/f401d139-3913-482c-8d5e-edab8f2d6dc0" />
    
- 액티브 장비 장애 시 스탠바이가 액티브 역할을 가져오고, MAC 테이블 갱신 등으로 절체가 자동으로 이루어진다.
    
    <img width="520" height="423" alt="image" src="https://github.com/user-attachments/assets/0608fd6d-14b4-48f9-9443-633ca3740ecb" />
    
    <img width="528" height="411" alt="image" src="https://github.com/user-attachments/assets/0e00a508-a39b-4918-b4f0-43abf9beef56" />
    
- FHRP 표준 프로토콜은 VRRP(Virtual Router Redundancy Protocol)다.
- 벤더 고유 구현으로 HSRP 같은 프로토콜도 존재하며, 리눅스에서는 keepalived 같은 패키지로 VRRP를 구현할 수 있다.

### VRRP 동작과 설정 요소

- VRRP 그룹을 구분하는 값으로 VRID를 사용한다.
- VRRP 장비 간 Hello 패킷을 주고받아 액티브 스탠바이를 선출한다.
- Hello는 멀티캐스트 주소를 사용한다.
- 우선순위 값으로 마스터(액티브)를 결정하며, 상대의 Hello를 일정 횟수 이상 수신하지 못하면 장애로 판단해 선출이 바뀐다.
    
    <img width="587" height="319" alt="image" src="https://github.com/user-attachments/assets/a3697e52-8f8f-4236-8524-90465aa2e698" />
    
    <img width="593" height="319" alt="image" src="https://github.com/user-attachments/assets/885b9b09-2ec1-442e-8ae2-c56ffa2c607f" />
    
- VRRP에서 가상 MAC은 VRID를 기반으로 생성되며, VRID가 중복되면 가상 MAC이 충돌할 수 있다.
- 한 장비가 여러 VRRP 그룹에 속하도록 구성하는 멀티 VRRP도 가능하다.

### VRRP 예시 설정 흐름

- 장비의 실제(Real) IP를 설정한다.
- VRID를 지정해 VRRP 그룹을 정의한다.
- 가상 IP(VIP)를 지정한다.
- 우선순위(Priority)로 액티브 스탠바이를 결정한다.
- track로 특정 조건 실패 시 우선순위를 낮추어 역할 전환을 유도할 수 있다.
- preempt로 스탠바이가 다시 액티브를 가져오는 동작을 제어하며, 플래핑을 막기 위한 지연 설정이 필요할 수 있다.

### 11.5.3 올 액티브 게이트웨이 이중화

- 기본 게이트웨이 이중화는 가상 IP 기준으로 액티브-스탠바이로 동작한다.
- 올 액티브 게이트웨이는 게이트웨이를 액티브-액티브 형태로 동작시키려는 구성으로, 트래픽을 더 효율적으로 처리하도록 설계한다.
- 조건에 따라 외부로 나가는 경로 이중화가 가능하지만, 조건이 맞지 않으면 트래픽이 불필요하게 우회해 비효율이 발생할 수 있다.
    
    <img width="420" height="337" alt="image" src="https://github.com/user-attachments/assets/e400e753-4e22-491c-9b97-c623376ee377" />

    <img width="430" height="329" alt="image" src="https://github.com/user-attachments/assets/059d554e-3b71-4ffa-b6f0-1d6a4220fb77" /> <br>
    
    <img width="434" height="330" alt="image" src="https://github.com/user-attachments/assets/80eef55c-84f4-45dd-b8db-9db5b7e434da" />
    

### 11.5.4 애니캐스트 게이트웨이

- 애니캐스트 게이트웨이는 같은 네트워크가 여러 위치에 존재하는 환경에서, 여러 게이트웨이가 동일한 주소를 가지고 동작하는 방식이다.
- 각 위치에 동일 주소의 게이트웨이가 존재하지만, 일반적으로 가장 가까운 위치의 게이트웨이가 서비스를 제공한다.
- 한 위치의 게이트웨이에 문제가 생겨도 다른 위치는 영향을 받지 않아, 장애 범위를 국소화할 수 있다.
- 데이터 센터의 ToR 스위치가 게이트웨이 역할을 수행하는 설계나, 가상화 네트워크에서 게이트웨이 역할을 분산하는 설계와 함께 고려된다.
