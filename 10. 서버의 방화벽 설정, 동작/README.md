# 10. 서버의 방화벽 설정/동작

- 서버를 새로 구축하면 ping으로 네트워크 연결을 점검하는 경우가 많다.
- 서버 내부 서비스가 LISTENING 상태여도 외부에서 접속이 안 되는 경우가 있다.
- 이때 서버 운영체제의 기본 호스트 방화벽이 원인인 경우가 많다.
- ping 자체가 막히는 경우도 있고 ping은 되지만 특정 서비스 포트만 막히는 경우도 있다.
    
    <img width="591" height="214" alt="image" src="https://github.com/user-attachments/assets/3cbd6697-e45d-44b7-bcdc-d85026481241" />
    
    <img width="493" height="172" alt="image" src="https://github.com/user-attachments/assets/e0e2283d-c95c-4738-b787-ef2f05b38eab" />
    
- 대부분의 서버 운영체제에는 기본 호스트 방화벽이 포함되어 있다.
- 호스트 방화벽은 필요한 서비스만 열고 나머지는 차단하는 정책을 기본으로 사용한다.
- 접근 통제 솔루션이 별도로 존재하는 환경에서는 호스트 방화벽이 특정 포트만 보조적으로 차단하는 형태로도 운용할 수 있다.

## 10.1 리눅스 서버의 방화벽 확인 및 관리

- 리눅스 호스트 방화벽은 전통적으로 iptables를 많이 사용해 왔다.
- CentOS 7 이후 계열은 기본적으로 firewalld를 사용하고 iptables는 별도 설치가 필요한 경우가 많다.
- Ubuntu 계열은 UFW를 사용하는 구성이 흔하다.
- iptables, firewalld, UFW는 커널의 netfilter 기능을 제어하기 위한 사용자 공간 도구라는 관점에서 이해하면 정리가 쉽다.
- iptables는 필터링 자체를 수행하는 엔진이 아니라 netfilter 규칙을 다루는 인터페이스로 이해해야 한다.

### 10.1.1 iptables 이해하기

- iptables 정책은 규칙들의 집합으로 구성되며 규칙은 체인과 테이블 구조로 정리된다.
- 트래픽 흐름 기준으로 INPUT, OUTPUT, FORWARD 같은 체인이 핵심이 된다.
- INPUT은 외부에서 호스트로 들어오는 트래픽을 다루는 체인이다.
- OUTPUT은 호스트에서 외부로 나가는 트래픽을 다루는 체인이다.
- FORWARD는 호스트를 통과하는 트래픽을 다루는 체인이다.
    
    <img width="442" height="259" alt="image" src="https://github.com/user-attachments/assets/a7dee0a2-342f-4874-b951-8f9541abab88" />
    
    <img width="442" height="258" alt="image" src="https://github.com/user-attachments/assets/588db97b-be5c-48bb-b7b7-971fe9fbec4f" />
    
- iptables에는 filter, nat, mangle, raw, security 테이블이 있다.
- 실무의 기본 방화벽 필터링은 주로 filter 테이블에서 수행한다.
- filter 테이블에는 INPUT, OUTPUT, FORWARD 체인이 있고 여기에 허용과 차단 규칙을 쌓는다.
- 정책은 보통 Match와 Target으로 구성된다.
- Match는 어떤 패킷에 규칙을 적용할지 결정하는 조건이다.
- Target은 조건에 일치한 패킷을 ACCEPT, REJECT, DROP 같은 방식으로 처리하는 동작이다.
    
    <img width="451" height="187" alt="image" src="https://github.com/user-attachments/assets/f1dfa405-b77e-4d0d-a38b-dd3969f03699" />
    

### 10.1.2 리눅스 방화벽 활성화/비활성화

- CentOS 7 계열에서는 firewalld가 기본 활성화인 경우가 많다.
- iptables 기반으로 관리하려면 firewalld를 비활성화하고 iptables 서비스를 설치, 활성화하는 경우도 있다.

### 10.1.3 리눅스 방화벽 정책 확인

- 현재 규칙을 확인할 때는 iptables 목록 출력 옵션을 사용한다.
- 체인별 기본 정책과 규칙 순서를 함께 확인해야 한다.
- ICMP 허용 규칙이 있으면 ping 같은 점검이 가능해진다.
- 신규 세션 NEW 상태에서 SSH 같은 관리 포트만 허용하는 형태가 자주 사용된다.
- 마지막에 매칭되지 않은 트래픽을 REJECT 또는 DROP으로 처리해 기본 차단 정책을 완성한다.
- REJECT는 차단 사실을 ICMP 등으로 알려주고 DROP은 조용히 버리는 성격이 있다.

### 10.1.4 리눅스 방화벽 정책 관리

- 규칙 추가는 보통 append 방식으로 체인 마지막에 붙는다.
- 정책은 위에서 아래로 순서대로 평가되므로 규칙의 위치가 매우 중요하다.
    
    <img width="715" height="175" alt="image" src="https://github.com/user-attachments/assets/2bb19d65-086f-41d5-94b3-64e041211aa5" />
    
- 특정 정책이 존재하는지 확인하려면 check 성격의 옵션을 사용할 수 있다.
- 규칙을 삭제할 때는 delete 옵션을 사용하며 규칙이 많으면 전체 목록보다 일치 여부 확인이 효율적일 수 있다.
- 서비스 포트는 단일 포트뿐 아니라 범위, 여러 포트 묶음, IP 대역 범위 같은 조건으로 제어할 수 있다.
- 전체 규칙을 초기화하려면 flush를 사용한다.
- 기본 정책 자체를 DROP으로 바꾸는 경우도 있지만 운영 영향이 크므로 의도를 명확히 해야 한다.

### 10.1.5 리눅스 방화벽 로그 확인

- iptables 동작을 검증하려면 로그를 남겨 허용과 차단을 확인하는 방식이 사용된다.
- 기본 메시지 로그에는 다양한 로그가 섞이므로 iptables 로그만 분리한다.
- rsyslog 설정으로 커널 로그를 별도 파일로 분리하고 서비스 재시작으로 반영한다.
- LOG 타깃과 log prefix를 사용하면 특정 규칙에서 찍히는 로그를 식별하기 쉬워진다.
- 바이트와 패킷 카운터를 함께 보면 실제로 어떤 규칙이 매칭되고 있는지 확인할 수 있다.
- iptables 추가 정리
    - 테이블 관점에서는 filter, nat, mangle, raw, security 테이블을 구분해 이해해야 한다.
    - nat 테이블은 주소 변환과 관련된 규칙을 다루며 PREROUTING, POSTROUTING 같은 체인이 중요하다.
    - mangle 테이블은 헤더 값 조정 같은 특수 목적에 쓰이며 일반 방화벽 필터링과는 목적이 다르다.
    - raw 테이블은 연결 추적과의 상호작용을 조정하는 용도로 사용된다.
    - security 테이블은 MAC과 SELinux 같은 보안 문맥과 연결되는 경우가 있다.
    - 타깃은 ACCEPT, REJECT, DROP 외에도 LOG, SNAT, DNAT 등 확장 타깃이 존재한다.
    - iptables 실행 옵션은 append, delete, check, replace, insert, list, flush, zero, new chain, delete chain, policy 변경 등이 있다.
    - 정책 옵션은 source, destination, protocol, in interface, out interface, state, string 같은 매치 조건이 있다.

## 10.2 윈도 서버의 방화벽 확인 및 관리

- 윈도 서버 방화벽은 일반 윈도 환경과 유사한 GUI를 통해 접근할 수 있다.
- 기본적으로 방화벽이 활성화되어 있으며 필요에 따라 정책을 추가해 운영하는 것이 권장된다.
- 대규모 환경에서는 AD 같은 중앙 정책으로 방화벽 구성을 일괄 배포하는 방식도 가능하다.

### 10.2.1 윈도 방화벽 활성화, 비활성화

- Windows 방화벽 관리 화면으로 진입해 현재 프로파일별 상태를 확인한다.

### 10.2.2 윈도 방화벽 정책 확인

- 고급 보안이 포함된 Windows 방화벽에서 인바운드, 아웃바운드 규칙을 확인한다.
- 윈도 정책도 트래픽 방향이 중요하므로 인바운드 규칙과 아웃바운드 규칙을 구분해 관리한다.

### 10.2.3 윈도 방화벽 정책 관리

- 인바운드 규칙 목록에서 기본으로 존재하는 다양한 규칙을 확인할 수 있다.
- 새 규칙 생성은 마법사를 따라 프로그램, 포트, 미리 정의됨, 사용자 지정 등 규칙 종류를 선택한다.
- 모든 프로그램 또는 특정 프로그램에 적용할지 결정할 수 있다.
- 프로토콜과 포트를 선택하며 ICMP 같은 경우 세부 유형까지 정책으로 지정할 수 있다.
- 적용 범위에서 로컬 IP와 원격 IP를 지정해 특정 주소만 허용하거나 차단할 수 있다.
- 작업 단계에서 허용, 차단, 보안 연결 허용 같은 동작을 결정한다.
- 프로파일 단계에서 도메인, 개인, 공용 네트워크 중 어느 프로파일에 적용할지 결정한다.
- 마지막으로 규칙 이름을 지정해 관리 가능하게 만든다.

### 10.2.4 윈도 방화벽 로그 확인

- 윈도 방화벽도 허용과 차단 로그를 확인할 수 있다.
- 다만 기본값에서 로그 수집이 꺼져 있을 수 있어 로그 수집을 활성화해야 한다.
- 방화벽 속성에서 프로파일별 로깅을 설정한다.
- 손실된 패킷 로그와 성공한 연결 로그를 켜면 방화벽 동작 분석에 도움이 된다.
