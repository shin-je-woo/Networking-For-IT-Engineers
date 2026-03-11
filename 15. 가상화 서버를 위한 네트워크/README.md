# 15. 가상화 서버를 위한 네트워크

## **15.1 가상화 서버 구성 시의 네트워크 설정**

- 하나의 물리 서버(호스트) 안에서 여러 가상 머신이 서로 다른 네트워크와 서비스로 동작하므로 호스트 내부에서 L2 스위치 역할이 필요하다.
- 이 역할을 하이퍼바이저가 제공하는 가상 스위치가 수행한다.
- 가상 서버가 여러 네트워크에 속하거나 망 분리가 필요하면 VLAN으로 논리 분리를 구성한다.
- 물리 스위치와 연결되는 구간에서는 VLAN 태깅 정보가 일관되게 전달되어야 한다.
    - 가상 스위치 업링크와 물리 스위치 포트를 트렁크로 구성해 여러 VLAN을 운반한다.
- 가상 서버는 동일 호스트 내부 통신과 외부 네트워크 통신을 모두 수행하므로 가상 스위치와 물리 스위치 사이 연결 방식이 핵심이 된다.
    
    <img width="485" height="141" alt="image" src="https://github.com/user-attachments/assets/c490f9a6-49fe-445a-be56-d0ddd2358238" />
 
    <img width="554" height="266" alt="image" src="https://github.com/user-attachments/assets/2c862737-956d-42f9-b568-3cc940d8fd5c" />

    <img width="676" height="280" alt="image" src="https://github.com/user-attachments/assets/290bea79-0f4e-411f-8f5c-8e19828ecab8" />


## **15.2 VMware vSphere**

### **15.2.1 VMware 가상 스위치**

- ESXi는 호스트 내부 네트워크 구성을 위해 가상 스위치를 제공한다.
- 가상 스위치는 물리 스위치처럼 보이지만 동작 방식이 다르다.
    - 물리 스위치는 MAC 학습을 통해 포워딩 테이블을 만들고 포트 기반으로 프레임을 전달한다.
    - 가상 스위치는 하이퍼바이저가 가상 NIC와 포트 그룹 정보를 이미 알고 있어 MAC 학습 없이도 L2 포워딩을 수행할 수 있다.
- ESXi 호스트는 물리 스위치와 연결되더라도 호스트 내부는 가상 스위치에서 스패닝 트리 같은 제어 프로토콜이 동작하지 않는 구성이 된다.
    - 물리 스위치가 보내는 BPDU가 호스트 내부로 유입되는 것을 제어하기 위한 기능이 필요할 수 있다.
        
        <img width="637" height="311" alt="image" src="https://github.com/user-attachments/assets/f5a2ae31-e35c-4568-9db4-38775ee4f10c" />

        <img width="682" height="241" alt="image" src="https://github.com/user-attachments/assets/202aa42e-d968-4381-a374-e9690cf60d43" />


### **15.2.2 표준 스위치/분산 스위치**

- 표준 스위치(Standard Switch)는 ESXi 호스트 단위로 존재하는 가상 스위치다.
    - 각 호스트에서 독립적으로 설정을 관리한다.
    - 호스트별로 동일한 설정을 반복 적용해야 하는 운영 부담이 생길 수 있다.
- 분산 스위치(Distributed Switch)는 vCenter에서 중앙 관리하는 가상 스위치다.
    - 여러 ESXi 호스트가 동일한 스위치 구성과 정책을 공유한다.
    - 호스트 추가 시에도 동일 정책을 일관되게 적용하기 쉽다.
- 표준 스위치와 분산 스위치는 제공 기능에서 차이가 난다.
    
    <img width="645" height="266" alt="image" src="https://github.com/user-attachments/assets/124a19ff-701a-4681-86dd-3dbdd03cf97e" />

    <img width="642" height="391" alt="image" src="https://github.com/user-attachments/assets/c84e9fa7-cad8-40b9-b04a-04acb2e605b3" />

    <img width="635" height="329" alt="image" src="https://github.com/user-attachments/assets/cca3ae2a-9e3c-4035-8bae-a7a8fcdffeda" />


### **15.2.3 VMkernel 포트와 가상 시스템 포트 그룹**

- ESXi의 네트워크 어댑터는 크게 VMkernel 네트워크 어댑터와 가상 시스템 포트 그룹으로 나뉜다.
- VMkernel 네트워크 어댑터는 호스트 자체 기능이 사용하는 네트워크다.
    - 관리 트래픽, vMotion, iSCSI, NFS, FT, vSAN 등 호스트 기능 트래픽에 사용된다.
    - 일반적으로 서비스 트래픽과 분리해 구성하는 것이 권장된다.
- 가상 시스템 포트 그룹은 가상 머신이 사용하는 네트워크 포트 그룹이다.
    - 포트 그룹 단위로 VLAN ID 등을 적용해 망 분리를 구성한다.
        
        <img width="695" height="356" alt="image" src="https://github.com/user-attachments/assets/0759198b-9f92-453c-8775-8ec6a35bc36c" />


### **15.2.4 포트 그룹 관리**

- 포트 그룹은 가상 스위치에서 동일 속성을 공유하는 논리 단위다.
    - VLAN ID를 통해 네트워크를 분리하는 데 가장 많이 사용된다.
    - 보안, 트래픽 제어, 팀 구성 같은 정책도 포트 그룹 단위로 적용한다.
- 표준 스위치의 포트 그룹은 호스트 단위로 생성하고 관리한다.
- 분산 스위치의 포트 그룹은 vCenter에서 생성하고 여러 호스트에 일관되게 적용한다.

### **15.2.5 가상 스위치의 다양한 기능**

- 가상 스위치 설정은 스위치 전체에 적용되는 항목과 포트 그룹 단위로 적용되는 항목이 함께 존재한다.
- 표준 스위치에서는 스위치 단위 설정 메뉴를 통해 기본 속성을 변경한다.
- 트래픽 조절 기능(QoS, 트래픽 쉐이핑)을 통해 특정 서비스의 과도한 대역폭 점유를 완화할 수 있다.
- 팀 구성과 관련된 동작 방식, 페일오버 감지, 로드 밸런싱 방식, 페일오버 순서 같은 항목을 설정할 수 있다.
- 분산 스위치에서는 NetFlow, LACP 같은 고급 기능을 더 폭넓게 제공하고 vCenter에서 중앙 관리한다.
