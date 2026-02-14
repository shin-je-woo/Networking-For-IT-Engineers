# 07. 통신을 도와주는 네트워크 주요 기술

## 7.1 NAT/PAT

- NAT(Network Address Translation)
    - IP 주소를 다른 IP 주소로 변환해서 라우팅과 통신을 가능하게 하는 기술
    - 가정용 공유기, 통신사망(LTE, 5G), 회사 네트워크 등에서 매우 흔하게 사용
    - 라우터, L3 스위치 같은 3계층 장비에서도 쓰이지만, 방화벽이나 L4 이상 세션 장비에서 특히 자주 등장
- PAT(Port Address Translation)
    - 엄밀히는 NAPT(Network Address Port Translation, RFC2663) 개념에 해당
    - 실무에서는 PAT라는 용어를 더 많이 사용해서 책에서도 PAT로 표기

### 7.1.1 NAT/PAT의 용도와 필요성

- IPv4 주소 고갈 대응
    - 공인 IP가 부족하므로 내부 단말은 사설 IP를 쓰고, 외부 통신 구간에서 공인 IP로 변환해 사용
    - 결과적으로 공인 IP를 꼭 써야 하는 구간과 사설 IP로 충분한 구간을 분리해 운영 가능
- 보안 강화(은닉 효과)
    - 외부와 통신할 때 내부 IP를 공인 IP로 치환해 내부 주소 체계를 직접 노출하지 않음
    - NAT의 성질을 이용하면 기본적으로 내부에서 시작한 통신은 허용하고, 외부에서 시작한 내부 유입 통신은 제어하기 쉬움(상태 기반 정책과 결합 시 효과가 커짐)
        
        <img width="529" height="244" alt="image" src="https://github.com/user-attachments/assets/4e0f9d2f-8903-4367-8aa8-2029e8273faf" />
        
- 서로 다른 네트워크 간 중복 IP 문제 완화
    - 서로 통신하려면 식별 가능한 유일한 IP가 필요
    - 두 조직이 같은 사설 대역을 쓰는 경우 직접 연결이 어려운데, NAT로 한쪽 주소 체계를 다른 대역으로 변환해 연결 가능(더블 NAT 같은 방식)
    - 대외계 연결, 금융 전용 회선 등에서 자주 발생하는 상황
        
        <img width="554" height="239" alt="image" src="https://github.com/user-attachments/assets/a4c5dadc-e4c0-4b66-961d-557466e5ba29" />
        
- 인프라 변경 비용 감소
    - 회선 사업자나 IDC 변경 시 공인 IP가 바뀌는 경우가 많음
    - 내부를 NAT 기반으로 구성하면 내부 서버와 PC의 IP 변경을 최소화하고, 외부 공개 지점(공인 IP, DNS, NAT 장비 설정) 중심으로 변경 범위를 줄일 수 있음
- NAT의 한계도 존재
    - 운영자 입장: IP가 변환되면 장애 분석이 어려워짐
    - 개발자 입장: 종단 간 직접 연결이 약해지고 NAT 환경을 고려한 설계가 필요, 이를 보완하려고 홀 펀칭 같은 기법이 등장

### 7.1.2 NAT 동작 방식

- 기본 개념
    - 출발지 사용자(사설 IP)가 목적지 서버(공인 IP)로 요청
    - NAT 장비가 출발지 IP를 공인 IP로 변환해 외부로 전달
    - 응답이 돌아오면 NAT 테이블을 조회해 원래 사설 IP로 역변환 후 사용자에게 전달
- 예시 흐름
    
    <img width="611" height="452" alt="image" src="https://github.com/user-attachments/assets/410ea0a2-79ac-4682-8c12-92bc53f25e83" />
    
    1. 사용자가 웹 서버로 패킷 전송
        - 출발지 IP 10.10.10.10
        - 목적지 IP 20.20.20.20
        - 목적지 포트 80
        - 출발지 포트는 임의(예시에서는 2000)
    2. NAT 장비가 정책에 따라 출발지 IP를 공인 IP(예시 11.11.11.11)로 변환하고 NAT 테이블에 기록
    3. 변환된 출발지 IP로 웹 서버에 전달
    4. 웹 서버의 응답은 목적지 IP가 NAT 공인 IP로 향함
    5. NAT 장비가 NAT 테이블에서 원래 출발지(사설 IP)를 찾아 역변환
    6. 원래 사용자에게 응답 전달

### 7.1.3 PAT 동작 방식

- NAT와 차이점
    - NAT는 보통 IP 주소만 바꾸는 관점이 중심
    - PAT는 IP 주소뿐 아니라 포트까지 함께 바꿔서 다수의 내부 사용자가 하나(또는 소수)의 공인 IP를 공유 가능
- 예시 흐름
    
    <img width="609" height="452" alt="image" src="https://github.com/user-attachments/assets/588a6320-b1e8-4d4c-999f-b3d357ecfdeb" />
    
    1. 사용자가 웹 서버로 요청 전송(출발지 포트 예시 2000)
    2. NAT 장비가 출발지 공인 IP로 변환하면서 출발지 포트도 다른 값(예시 3000)으로 변환, NAT 테이블에 IP와 포트 매핑을 함께 기록
    3. 변환된 IP와 포트로 웹 서버에 전달
    4. 웹 서버 응답은 목적지 IP가 NAT 공인 IP, 목적지 포트가 변환된 포트(예시 3000)로 도착
    5. NAT 장비가 테이블을 보고 원래 사설 IP와 원래 포트(예시 2000)로 역변환
    6. 사용자에게 전달
- 운영상 포인트
    - 서비스 포트는 개수가 제한적이라 포트가 모두 소진되면 PAT가 정상 동작하지 않을 수 있음
    - 동시 사용자가 많으면 공인 IP를 1개가 아니라 여러 개(IP 풀)로 구성하는 방식이 필요
    - PAT는 동작 특성상 SNAT 중심으로 적용되는 경우가 일반적이며, DNAT에는 그대로 대응되지 않는 제약이 있음

### 7.1.4 SNAT와 DNAT

- 구분 기준
    - 트래픽이 시작되는 지점(요청의 출발 기준)에서 어떤 주소를 바꾸는지로 구분
        
        <img width="633" height="248" alt="image" src="https://github.com/user-attachments/assets/2a53a857-3b14-4e81-9e44-8ad79d3bfff0" />
        
- SNAT(Source NAT)
    - 출발지 주소를 변경하는 NAT
    - 대표 사용 사례
        - 사설에서 공인으로 나갈 때 출발지 IP를 공인 IP로 변환(PAT 포함)
        - 대외 통신에서 내부 IP를 숨기기 위한 보안 목적
        - 내부 IP가 상대측 내부 IP와 중복되는 경우, 출발지 주소를 다른 대역으로 바꿔 충돌 회피
        - 로드밸런서 구성에 따라 응답 트래픽을 다시 로드밸런서로 유도하기 위해 SNAT을 쓰는 경우도 있음
            
            <img width="634" height="167" alt="image" src="https://github.com/user-attachments/assets/856ea63e-55f2-444b-a91e-e2337406240e" />
            
- DNAT(Destination NAT)
    - 목적지 주소를 변경하는 NAT
    - 대표 사용 사례
        - 로드밸런서: 사용자는 서비스 VIP로 요청하고, 로드밸런서는 실제 서버 IP로 목적지를 바꿔 전달
        - 대외계 연동: 사내에서 관리되는 내부 주소 체계를 유지하면서, 외부 파트너별로 목적지 IP를 특정 대역으로 변환해 라우팅 설정을 단순화
            
            <img width="626" height="264" alt="image" src="https://github.com/user-attachments/assets/bd5609e8-aa02-4d4b-86f2-e2738d668592" />
            
- 역 NAT 개념
    - 요청 시 NAT을 수행하면 응답이 올 때는 반대 방향 변환이 필요
    - NAT 장비는 첫 통과 시 NAT 테이블을 만들고, 응답이 들어오면 테이블을 참조해 역변환 수행

### 7.1.5 동적 NAT와 정적 NAT

- 정적 NAT(Static NAT)
    - 출발지와 목적지 매핑이 특정 IP 쌍으로 사전에 고정
    - 1:1 NAT라고도 부름
    - NAT 테이블은 사전 생성, 타임아웃 개념이 없거나 의미가 작음
    - 매핑이 고정이라 서비스 방향성에 대한 고려가 상대적으로 적음
- 동적 NAT(Dynamic NAT)
    - 고정 매핑이 아니라 IP 풀에서 필요 시점에 매핑을 할당
    - 1:N, N:1, N:M 형태 가능
    - NAT 수행 시점에 NAT 테이블이 생성되고, 일정 시간 통신이 없으면 테이블이 사라질 수 있음(타임아웃)
    - 설정 시 서비스 흐름과 세션 유지 시간 특성을 고려해야 함

## 7.2 DNS

- 네트워크 프로토콜은 실제 데이터가 오가는 데이터 프로토콜과, 통신 관계를 맺고 유지하도록 돕는 컨트롤 프로토콜로 나눌 수 있다.
- DNS는 사람이 쓰는 도메인 이름을 네트워크가 쓰는 IP 주소로 변환한다.
- 클라우드 환경에서는 인프라 변경이 잦고, MSA 환경에서는 서비스 간 호출이 많아져 DNS 중요성이 더 커진다.

### 7.2.1 DNS 소개

- 사용자는 보통 IP를 직접 입력하지 않고 링크, 북마크, 검색 결과로 접속한다.
- 실제 통신은 결국 IP로 이뤄진다.
    
    <img width="638" height="338" alt="image" src="https://github.com/user-attachments/assets/e11d31db-6cdd-4bfa-b03e-1b6dad10fdd5" />
    

### 7.2.2 DNS 구조와 명명 규칙

- 도메인 계층
    - 도메인은 계층 구조다.
    - 점 구분자 기준으로 오른쪽이 상위다.
    
    <img width="544" height="272" alt="image" src="https://github.com/user-attachments/assets/04d9ec77-e8c3-419d-bf63-b53a88554d6e" />
    
- 루트 도메인과 루트 DNS
    - 루트 도메인은 최상위 영역
    - 로컬 DNS는 자신이 모르는 도메인이면 루트 DNS로부터 시작해 위임 정보를 따라가며 찾는다.
    - 전 세계 루트 DNS는 13개로 운영되며, DNS 서버 설치 시 루트 힌트 정보가 기본 포함되는 형태로 안내된다.
- TLD 분류
    - TLD는 IANA 기준으로 6가지 유형이 있다.
    - Generic(gTLD)
    - country code(ccTLD)
    - sponsored(sTLD)
    - infrastructure
    - generic-restricted(grTLD)
    - test(tTLD)
    - gTLD 예시로 초기 대표 도메인 묶음을 제시했다. ex) com, edu, gov, int, mil, net, org
    - ccTLD는 ISO 3166 국가 코드를 사용한다. ex) 한국은 kr

## 7.2.3 DNS 동작 방식

- 로컬에서 도메인을 IP로 바로 매핑할 수 있다.
    - hosts 파일에 도메인과 IP를 직접 넣는 방식
    - 이 경우 로컬 캐시에 고정적으로 남는 성격이 된다.
- 일반적인 DNS 쿼리 흐름은 다음 순서로 진행된다.
    - 로컬 캐시 확인
    - 없으면 DNS 서버로 질의
    - 응답을 받으면 캐시에 저장하고 이후 재사용
        
        <img width="517" height="305" alt="image" src="https://github.com/user-attachments/assets/1c1942eb-c874-4387-ba95-14a8cdf932e3" />
        

### 재귀적 질의와 반복적 질의

- 재귀적 질의(Recursive Query)
    - 클라이언트가 로컬 DNS에 요청하면 로컬 DNS가 최종 결과를 만들어서 반환하는 방식
    - 클라이언트는 한 번 요청하고 기다리는 형태가 된다.
- 반복적 질의(Iterative Query)
    - 로컬 DNS가 상위 DNS로부터 다음 단계 정보를 받아가며 단계적으로 찾아가는 방식
    - 보통 로컬 DNS와 상위 DNS 사이에서 이 방식으로 진행된다고 표현한다.
- 예시 흐름
    
    <img width="614" height="321" alt="image" src="https://github.com/user-attachments/assets/24fea843-b61a-44cb-b7d7-5cd59dabb589" />
    
    - 호스트는 로컬 캐시 확인
    - 없으면 로컬 DNS에 요청
    - 로컬 DNS는 루트 NS, TLD NS, 대상 NS를 순서대로 조회해 최종 IP를 얻는다.
    - 응답은 캐시에 저장되고 호스트에 반환된다.

### 7.2.4 마스터와 슬레이브

- DNS 서버는 역할로 마스터(Primary)와 슬레이브(Secondary)로 나뉠 수 있다.
- 우선순위에 따라 구분 하지 않고 두 서버 모두 도메인 쿼리에 응답한다.
- 구분 기준은 존 파일을 직접 관리하느냐 복제하느냐
    - 마스터: 존 파일을 생성하고 레코드를 직접 관리
    - 슬레이브: 마스터 존 파일을 복제
- 복제 과정은 Zone Transfer라고 한다.
    
    <img width="587" height="222" alt="image" src="https://github.com/user-attachments/assets/255ff143-fb00-4dde-8350-c32cc96dc915" />
    
- 보안 관점에서 마스터 입장에서 복제 가능한 슬레이브를 지정하는 설정이 중요하다.
- 참고로 고가용성 개념으로 액티브-스탠바이, 액티브-액티브 형태로 구성하지 않는다.
    - 마스터 장애 후 일정 시간이 지나 슬레이브도 응답 불가가 될 수 있고, 이 시간은 SOA의 만료 시간 Expire에 의해 좌우된다.

### 7.2.5 DNS 주요 레코드

<img width="481" height="381" alt="image" src="https://github.com/user-attachments/assets/8279c763-b705-429a-b103-503a628c7aa4" />

- A 레코드
    - 기본 레코드로 도메인 주소를 IP 주소로 변환하는 레코드
    - 하나의 도메인에 여러 A 레코드를 둘 수 있다.
    - 하나의 IP에 여러 도메인을 매핑할 수도 있다.
- AAAA 레코드
    - A 레코드와 동일한 역할을 IPv6에서 수행
- CNAME 레코드
    - IP가 아닌 도메인 이름을 가리키는 별칭
    - 별칭을 쓰면 IP 변경 시 관리 지점이 줄어든다.
        
        <img width="629" height="323" alt="image" src="https://github.com/user-attachments/assets/2d1911ea-8f60-426a-812f-f98fc4023d3b" />
        
- SOA 레코드
    - 도메인 영역에 대한 권한을 나타내는 레코드
    - 현재 네임 서버가 이 도메인 영역에 대한 관리 주체임을 의미한다.
    - 존이 정상 동작하려면 필수로 존재해야 한다.
    - 그 밖에 현재 도메인 관리에 필요한 속성값을 설정한다.
        - refresh: 보조 네임서버가 갱신을 시도하는 주기
        - retry: 접근 실패 시 재시도 간격
        - expire: 이 시간이 지나도록 갱신 못 하면 존 정보를 폐기하는 기준
        - minimum 또는 default TTL: 기본 TTL
- NS 레코드
    - 해당 도메인 영역을 담당하는 네임서버를 지정
    - 상위 영역에서 하위 영역으로 권한을 위임하는 데도 사용된다.
- MX 레코드
    - 메일 서버를 구성할 때 사용되는 레코드
    - 우선순위 값이 낮을수록 우선 처리 대상으로 동작
    - 1순위 실패 시 다음 순서의 MX 레코드의 메일 서버에서 처리한다.
- PTR 레코드
    - A레코드와 반대 방향의 레코드
    - IP에 대한 질의를 도메인 이름으로 응답
    - PTR레코드는 주로 화이트 도메인 구성용으로 사용된다.
- TXT 레코드
    - 도메인에 텍스트를 넣을 수 있는 레코드
    - 특정 기능으로 사용할 수도 있는데, 주로 화이트 도메인을 위한 SPF 레코드이다.
    - 공백 포함 가능, 대소문자 구분, 최대 255자까지 입력 가능하다.

### 7.2.6 DNS에서 알아두면 좋은 내용

### 7.2.6.1 도메인 위임 DNS Delegation

- 한 도메인 내 모든 레코드를 한 네임서버가 관리하지 않고, 일부 하위 영역을 다른 네임서버에 맡기는 방식
- 계층 구조에서 특정 하위 도메인 영역을 분리 운영할 때 유용
- CDN, GSLB 같은 구성과 연결해 사용할 수 있다.
- 예시 개념
    - zigispace.net은 A DNS가 관리
    - 그 하위의 post 영역만 별도 시스템이 관리하도록 위임해 관리 주체를 분리
        
        <img width="600" height="277" alt="image" src="https://github.com/user-attachments/assets/31402c7f-9f93-42f2-86ee-67cdd4ac867d" />
        

### 7.2.6.2 TTL

- TTL은 DNS 응답 결과를 캐시에 유지하는 시간
- TTL이 크면
    - 캐시 히트가 늘어 DNS 질의량과 응답 지연이 줄 수 있다.
    - 대신 IP 변경이 반영되기까지 시간이 길어질 수 있다.
- TTL이 작으면
    - 변경 반영은 빨라진다.
    - 대신 DNS 질의량이 늘어 서버 부하가 증가할 수 있다.
- 상황별 운영 팁 관점
    - 평소에는 TTL을 길게 두어 안정성과 부하를 관리
    - 이전, 장애 대응, IP 변경 예정 시점에는 TTL을 미리 낮춰 전환을 빠르게 유도

### 7.2.6.3 화이트 도메인

- 정상 발송 메일이 RBL 등으로 차단되는 것을 줄이기 위한 제도적 운영 관점
- KISA에서 정상적인 도메인을 인증, 관리하는 제도가 화이트 도메인이다.
- 화이트 도메인 등록 시 SPF 레코드 설정이 필요하다.

### 7.2.6.4 한글 도메인

- 도메인 주소는 영문뿐만 아니라 한글로 주소를 만들 수 있다.
- DNS에 저장하고 조회하기 위해서는 퓨니코드로 변환되어야 한다.
- 퓨니코드는 xn-- 로 시작하는 문자열로 변환한다.

## 7.3 GSLB

- DNS는 같은 이름의 레코드에 여러 IP를 넣어두고, 질의가 오면 IP를 나눠서 응답하는 방식으로 로드밸런싱처럼 보이게 할 수 있다.
- 문제는 DNS 자체는 서비스가 살아있는지 확인하지 않는다.
    - 특정 서버가 장애여도 DNS는 그 IP를 계속 응답할 수 있다.
    - 결과적으로 사용자는 장애 서버로 접속을 시도하게 되고, 접속 실패가 발생한다.
- GSLB(Global Server/Service Load Balancing)는 DNS의 한계를 극복한다.
    - 도메인 질의에 응답하는 역할은 DNS와 유사
    - 다만 서버들에 대해 헬스 체크를 수행하고, 정상 상태인 IP만 응답 후보로 사용한다.
- 이런 성격 때문에 GSLB를 인텔리전스 DNS라고 부르기도 한다.
    
    <img width="634" height="356" alt="image" src="https://github.com/user-attachments/assets/50391b9f-97d6-4a57-b738-94dc0d4d418d" />
    

### 7.3.1 GSLB 동작 방식

<img width="632" height="429" alt="image" src="https://github.com/user-attachments/assets/c9be6d8d-abec-47bd-a49b-90d29fd4c9aa" />

- 사용자가 web.zigispace.net 접속을 위해 DNS 질의를 보냄
- LDNS(Local DNS)가 root부터 순서대로 따라가며 web.zigispace.net을 관리하는 NS를 찾음
- 해당 도메인에서 GSLB를 사용하도록 위임되어 있으면, LDNS는 GSLB 서버를 NS로 안내받음
- LDNS가 GSLB에 다시 질의함
- GSLB는 설정된 분산 정책과 헬스 체크 결과를 기반으로 특정 서비스 IP를 선택해 응답함
    - 예시로 서울 센터, 부산 센터 중 정상이고 정책에 맞는 곳의 IP를 선택
- LDNS는 그 결과를 사용자에게 최종 응답함
- 사용자는 받은 IP로 서비스에 접속함
- 즉, GSLB는 IP 주소 정보를 단순히 갖고 있다가 응답해주는 것이 아니라 헬스 체크를 통해 정상인 IP만 응답한다는 점이다.

### 7.3.2 GSLB 구성 방식

- GSLB를 사용한 도메인 설정 방법은 두 가지가 있다.
    - 도메인 자체를 GSLB로 사용
    - 도메인 내의 특정 레코드만 GSLB를 사용
- 도메인 자체를 GSLB로 사용하는 경우
    - 도메인의 NS 자체를 GSLB 장비로 지정하는 방식
    - 해당 도메인에 속한 레코드 질의가 전반적으로 GSLB를 통해 처리된다.
    - 한 곳에서 일괄 관리가 가능하지만, 모든 질의가 GSLB로 몰리므로 GSLB 부하가 커질 수 있다.
- 도메인 내 특정 레코드만 GSLB 적용
    - 회사 대표 도메인 전체를 다 GSLB로 돌릴 필요가 없을 때 자주 쓰는 방식이다.
    - CNAME(별칭, Alias) 방식
        - web.zigispace.net 같은 실제 서비스 이름은 CNAME으로 GSLB가 관리하는 다른 FQDN을 가리키게 함
        - LDNS는 먼저 원래 이름을 조회한 다음 CNAME을 따라가며 GSLB 쪽으로 다시 조회를 진행
        - CDN이나 외부 GSLB 사업자를 붙일 때도 이런 형태가 잘 맞다.
            
            <img width="726" height="420" alt="image" src="https://github.com/user-attachments/assets/2a62ba8e-f060-4ae6-84ec-6ab62aa033c7" />
            
    - NS 위임(Delegation) 방식
        - 특정 FQDN에 대해 NS 레코드를 GSLB로 지정해서 그 영역을 위임한다.
        - LDNS는 해당 FQDN 질의에서 GSLB를 권한 서버처럼 찾아가게 된다.
        - 한 FQDN을 위임하면 그 하위 도메인들도 계층적으로 함께 위임 처리되는 효과가 있다.
        - 그래서 web 아래에 shopping.web, portal.web 같은 하위가 많을 때 DNS 설정을 최소화할 수 있다.
            
            <img width="712" height="413" alt="image" src="https://github.com/user-attachments/assets/8c081d36-fb9f-4c19-8b1e-ed6e7f66b138" />
            

### 7.3.3 GSLB 분산 방식

- GSLB를 이용해 서비스를 분산하면 다음과 같은 목적을 달성할 수 있다.
    - 서비스 제공 가능 여부를 확인한 뒤 트래픽을 분산
    - 지리적으로 떨어진 다른 데이터 센터로 트래픽을 분산
    - 사용자와 더 가까운 쪽, 더 빠른 쪽으로 유도해 체감 성능을 개선
- 헬스 체크와 분산에서 자주 쓰는 판단 요소
    - RTT, Latency 같은 응답 시간 정보
    - IP에 대한 지리(Geography) 정보
- RTT나 Geo 판단은 사용자의 실제 위치 기준이 아니라, 보통 사용자가 바라보는 LDNS(Local DNS)와 GSLB 사이의 관측값을 기반으로 결정됨
- 그래서 국내 사용자라도 해외 DNS를 LDNS로 쓰면, 오히려 더 먼 센터로 유도되어 접속 시간이 늘어날 수 있다.

## 7.4 DHCP

- 호스트가 네트워크 통신을 하려면 다음 정보가 필요하다.
    - IP 주소
    - 서브넷 마스크
    - 기본 게이트웨이
    - DNS 서버 주소
- DHCP(Dynamic Host Configuration Protocol)의 목적은 사용자가 직접 입력해야 하는 네트워크 설정을 자동으로 할당해준다.
- DHCP 장점
    - 별도 IP 설정 작업이 줄어 사용자와 관리자 모두 편해진다.
    - 미사용 IP를 회수해 필요한 경우 재할당 가능하다.
    - 수동 입력으로 생길 수 있는 오류, 중복 IP 할당 문제를 예방한다.

### 7.4.1 DHCP 프로토콜

- DHCP는 BOOTP 기반 프로토콜이다.
    - BOOTP와 유사하게 동작
    - BOOTP에 없는 기능을 추가한 확장 형태
- DHCP와 BOOTP는 호환성이 있다.
    - BOOTP 클라이언트가 DHCP 서버를 쓰거나
    - DHCP 클라이언트가 BOOTP 서버로부터 정보를 받을 수도 있다.
- DHCP는 서버와 클라이언트 구조로 동작한다.
- DHCP 포트
    - 클라이언트 서비스 포트: 68(bootpc)
    - 서버 서비스 포트: 67(bootps)

### 7.4.2 DHCP 동작 방식

- DHCP로 신규 IP를 자동 할당받는 과정은 4단계로 진행된다.
    
    <img width="842" height="570" alt="image" src="https://github.com/user-attachments/assets/c57280d0-15c5-4770-a196-9158447c0487" />
    
- 1. DHCP Discover
    - DHCP 클라이언트가 DHCP 서버를 찾기 위해 브로드캐스트 전송
    - 클라이언트는 아직 IP가 없으므로 다음처럼 전송
        - 출발지 IP: 0.0.0.0
        - 목적지 IP: 255.255.255.255
    - 사용 포트
        - 출발지 UDP 68
        - 목적지 UDP 67
    - 할당 과정이므로 TCP가 아니라 UDP 사용
- 2. DHCP Offer
    - DHCP 서버가 Discover를 수신하면 할당할 IP를 선택
    - IP 선택 기준
        - DHCP IP Pool에서 임의로 선택 가능
        - 또는 특정 MAC 주소에 대해 특정 IP를 사전 정의해 고정 IP처럼 할당 가능
    - Offer에 포함되는 정보
        - 할당 IP
        - 서브넷 마스크
        - 게이트웨이
        - DNS 정보
        - 임대 시간(Lease Time)
        - DHCP Server Identifier(서버 식별자)
- 3. DHCP Request
    - 클라이언트가 서버로부터 제안받은 설정값을 요청
    - 포함되는 핵심 정보
        - Requested IP
        - DHCP Server Identifier
- 4. DHCP Acknowledgement(ACK)
    - DHCP 서버가 요청을 승인한다는 응답
    - 서버는 다음을 기록하고 전송
        - 해당 IP를 어떤 클라이언트가 언제부터 사용할지
        - Request를 정상 수신했다는 확인
    - 클라이언트는 ACK 수신 후 IP를 로컬에 설정하고 사용 시작

### 임대(Lease)와 갱신(Renewal)

- DHCP는 IP를 소유권이 아니라 임대 형태로 제공한다.
- DHCP 서버는 할당한 IP와 임대 시간을 함께 전달한다.
- 임대 시간이 만료되면 서버는 해당 IP를 IP Pool로 회수한다.
- 임대 만료 이후 동작
    - 이론적으로는 다시 Discover부터 새로 할당을 시작해야 한다.
    - 실제로는 갱신(Renewal) 절차로 기존 IP를 유지하는 경우가 많다.
- 갱신 시점과 흐름
    - 임대 시간의 50퍼센트가 지나면 갱신 절차를 수행한다.
    - 초기 할당과 달리 다음 단계가 생략된다.
        - Discover
        - Offer
    - 갱신 절차는 Request와 ACK 중심으로 진행된다.
    - 초기 할당은 브로드캐스트 중심이지만, 갱신은 브로드캐스트가 아니라 유니캐스트로 진행되어 불필요한 브로드캐스트를 줄인다.
- 임대 시간 설정 관점 팁
    - 클라이언트가 어느 정도 고정적이고 IP 범위가 넓으면 임대 시간을 길게 설정한다.
    - 클라이언트가 불특정이고 자주 바뀌면 임대 시간을 짧게 해서 IP가 빨리 회수되도록 설정한다.

### 참고: DHCP Starvation 공격

- DHCP 서버는 가용 IP 리스트(IP 풀)를 관리한다.
    - 전체 IP 범위
    - 이미 임대된 IP와 임대 시간
    - 아직 임대되지 않은 가용 IP
- 공격 개념
    - 가용 IP가 모두 소진된 상태에서 새로운 클라이언트가 Discover를 보내면 IP를 할당할 수 없다.
    - 이 점을 악용해 DHCP 서버의 가용 IP를 가짜로 대량 할당받아 정상 클라이언트가 IP를 못 받게 만드는 공격이 DHCP Starvation(기아 상태) 공격이다.

### 7.4.3 DHCP 서버 구성

- DHCP 서버 구성 시 주로 설정하는 값
    - IP 주소 풀(IP 범위)
        - 클라이언트에 할당할 IP 주소 범위
    - 예외 IP 주소 풀(예외 IP 범위)
        - 선언된 범위 중 예외적으로 할당하지 않을 대역
    - 임대 시간
        - 클라이언트에 할당할 IP의 기본 임대 시간
    - 서브넷 마스크
        - 클라이언트에 할당할 IP에 대한 서브넷 마스크 정보
    - 게이트웨이(라우터)
        - 클라이언트에 할당할 기본 게이트웨이 정보
    - DNS
        - 클라이언트에 할당할 DNS 주소
- DHCP 서버 구현 위치
    - 윈도우 서버의 DHCP 서비스
    - 리눅스의 DHCP 데몬
    - 스위치, 라우터, 방화벽, VPN 같은 네트워크 장비

### 7.4.4 DHCP 릴레이

- DHCP 패킷은 기본적으로 브로드캐스트로 전송된다.
- 브로드캐스트는 동일 네트워크에서만 전달되므로 다음과 같은 문제가 있다.
    - 네트워크가 여러 개로 분리된 환경에서는 DHCP 메시지가 다른 네트워크의 DHCP 서버까지 도달하지 못한다.
    - 따라서 네트워크마다 DHCP 서버를 따로 둬야 하는 상황이 발생한다.
        
        <img width="828" height="432" alt="image" src="https://github.com/user-attachments/assets/14be497a-0ceb-4f4d-bfab-9b38079eafdf" />
        
- DHCP 릴레이 에이전트(Relay Agent) 기능
    - DHCP 클라이언트와 DHCP 서버가 서로 다른 네트워크 대역에 있을 때 DHCP 패킷을 중간에서 릴레이해 DHCP 서버까지 전달한다.
    - 브로드캐스트로 들어온 DHCP 패킷을 서버로 갈 수 있도록 유니캐스트로 변환해 전달하는 역할
- 릴레이를 쓰면 네트워크마다 DHCP 서버를 두지 않고 중앙 DHCP 서버만으로 여러 네트워크의 IP 풀을 관리할 수 있다.
    
    <img width="828" height="492" alt="image" src="https://github.com/user-attachments/assets/2df198ad-3d01-4851-83d5-6a77aada986f" />
    
- 릴레이 동작 조건
    - 릴레이 에이전트는 DHCP 클라이언트와 같은 L2 네트워크 내에 존재해야 한다.
    - 릴레이 에이전트는 DHCP 서버로 유니캐스트 전달을 위해 DHCP 서버 IP를 알고 있어야 한다.
