# Network And Socket
## summary
### TCP/IP 프로토콜 구조
**네트워크 접근 계층(Network Access Layer)**  
* 물리적 네트워크를 통한 실제 데이터 송수신 담당
* 구성 요소로 물리적 신호를 처리하여 데이터를 송수신하는 네트워크 하드웨어와 운영체제가 제공하는 장치 드라이버 존재
* 하드웨어적으로 정의된 물리적 주소(Physical Address) 사용

**인터넷 계층(Internet Layer)**  
* 네트워크 접근 계층의 도움을 받아 데이터를 목적지 호스트까지 전달하는 역할
* 소프트웨어적으로 정의된 논리적 주소인 IP 주소(Internet Protocol Address) 사용
  
> * IP 주소는 유일성 보장 --> Inter-network 구성 가능
> 
> **IPv4**  
> * 32bit, 8bit단위 점( . )으로 구분, 10진수 네 개로 표기
> * Dotted-decimal notation
> 
> **IPv6**  
> * 128bit, 16bit단위 콜론( : )으로 구분, 16진수 여덟 개로 표기
> * Colon-hexadecimal notation

> 실제 통신 시 전송 경로를 결정하고 그에 따라 데이터를 전달하는 작업인 라우팅이 필요
> * 라우팅(Routing) : 데이터를 목적지까지 전달하는 일련의 작업을 가리키는 용어로, 라우팅에 필요한 정보를 수집하는 작업과 라우팅 정보를 기초로 실제 데이터를 전달하는 작업을 포함
> * 라우터(Router) : 라우팅을 담당하는 전용 장비로 서로 다른 네트워크에 연결된 호스트가 통신이 가능하도록 함

**전송 계층(Transport Layer)**  
* 최종 통신 목적지를 지정하고 오류 없이 데이터를 전송하는 역할
* 프로세스를 지정하는 16비트 주소(Port Number) 사용
* 인터넷 계층에서의 데이터 손실 및 손상을 검출하여 잘못된 데이터가 목적지에 전달되지 않도록 함
* TCP(Transmission Control Protocol)와 UDP(User Datagram Protocol) 사용

| TCP                               | UDP                            |
| --------------------------------- | ------------------------------ |
| 연결형 프로토콜 : 연결 설정 후 통신 가능          | 비연결형 프로토콜 : 연결 설정 없이 통신 가능     |
| 신뢰성 있는 데이터 전송 : 필요 시 데이터 재전송      | 신뢰성 없는 데이터 전송 : 데이터 재전송 X      |
| 일대일 통신(Unicast)                   | 일대일 통신, 일대다 통신(Broadcast)      |
| 데이터 경계 구분 X : Byte-Stream Service | 데이터 경계 구분 O : Datagram Service |

> **Port Number**  
> * 통신의 종착점(하나 혹은 여러 프로세스)을 식별
> * unsigned 16bit integer
>  
> |범위|분류|
> |:-:|:-:|
> |0 ~ 1023|Well-known Ports|
> |1024 ~ 49151|Registered Ports|
> |49152 ~ 65535|Dynamic and/or Private Ports|

**응용 계층(Application Layer)**  
* 전송 계층을 기반으로 하는 다수의 프로토콜(Telnet, FTP, HTTP, SMTP, MIME, SNMP ...)과 이 프로토콜을 사용하는 응용 프로그램을 포괄

### 데이터 전송 원리
* 송수신 시 네트워크 계층을 거쳐가며 각 계층에 필요한 제어 정보를 제공 및 제거하고 최종적으로 수신 측 응용 프로그램에 데이터 전달
* IP 주소와 라우팅 기능을 통해 패킷 전송 경로를 결정
* 응용 계층과 전송 계층은 종단에서만(End-to-end)존재하며 작동
* 물리적 주소는 패킷이 라우터를 통과할 때마다 다음 지점의 물리적 주소로 계속 변경

**Packet**  
* 제어 정보 + 데이터
* 송수신 시 각 프로토콜에서 정의한 제어 정보(IP 주소, 포트 번호, 오류 체크 코드 등)가 필요
* 제어 정보는 위치에 따라 앞쪽에 붙는 Header와 뒤쪽에 붙는 Trailer로 구분

**루프백 주소(Loopback Address)**
* 시스템 자신을 나타내는 주소로 내부적으로만 사용
* IPv4에서는 `127.0.0.1`로 IPv6에서는 `0:0:0:0:0:0:0:1`(줄여서 ::1)로 정의
## example
### MAC 주소(Media Access Control Address)
* 네트워크 접근 계층(물리 계층)에서 사용하는 네트워크 인터페이스에 할당된 고유 식별 주소
* 이더넷 하드웨어 주소(Ethernet hardware address) 라고도 불림
* 6바이트(48비트)로 구성되며, 각 바이트는 16진수 2자리로 표현
* 전체 비트 배열을 Big-Endian으로 읽으며 최상위 비트(MSB, Most Significant Bit)기준으로 구분

**비트 구조**  
1. **최상위 비트(1~2번째 비트)**
* `U/L 비트(Unicast/Locally Administered Bit)`
  * 이 비트가 0이면 해당 주소는 글로벌 관리 주소로, 제조업체에서 할당한 주소
  * 이 비트가 1이면 로컬 관리 주소로, 사용자가 임의로 지정한 주소
* `I/G 비트(Individual/Group Bit)`
  * 이 비트가 0이면 개별 주소로, 특정 장치(노드)를 나타냄
  * 이 비트가 1이면 그룹 주소로, Multicast나 Broadcast를 나타냄
> **Unicast**  
> * 특정 장치 하나를 대상으로 데이터를 전송
> * `00:1A:2B:3C:4D:5E`
>
> **Multicast**  
> * 여러 장치를 대상으로 데이터를 전송
> * `01-00-5E`로 시작
> 
> **Broadcast**  
> * 네트워크의 모든 장치를 대상으로 데이터를 전송
> * `FF:FF:FF:FF:FF:FF`

2. **제조업체 식별(OUI, Organizationally Unique Identifier)**
* 첫 24비트(0~23번째 비트)는 IEEE가 제조업체에 할당한 고유한 값

3. **장치 고유 식별자**
* 마지막 24비트(24~47번째 비트)는 제조업체가 개별 장치에 고유하게 할당하는 번호

### OSI(Open Systems Interconnection) 7 계층 모델
* 네트워크 통신을 계층적으로 나누어 각 계층의 역할을 정의한 참조 모델
* 네트워크 통신 과정을 구조화하고, 표준화된 방식으로 네트워크 장비나 프로토콜이 상호작용하도록 함

> TCP/IP 모델은 실제 인터넷 통신을 기반으로 설계되어 OSI 모델보다 간단하며 실질적인 네트워크 프로토콜 체계에 가까움

**물리 계층(Physical Layer)**  
* 데이터를 물리적 신호(전기, 광 등)로 변환하여 전송
* 케이블, 스위치, 허브와 같은 장치에서 데이터 전송
* 데이터 전송의 전기적, 기계적, 절차적 특성을 정의

> **주요 기능**  
> * 비트 전송(0과 1로 이루어진 신호 전송)
> * 매체 종류(UTP, 광섬유 등)와 신호 특성(아날로그, 디지털)을 다룸

> TCP/IP 모델에는 별도의 물리 계층 정의가 없으며, 데이터 링크 계층과 함께 하드웨어 구현에 포함

**데이터 링크 계층(Data Link Layer)**  
* 물리 계층으로부터 받은 데이터를 신뢰성 있는 프레임으로 만들어 상위 계층으로 전달
* 프레임의 오류 검출 및 재전송 처리
* MAC Address를 기반으로 Local Network 내 통신 수행

> **주요 기능**  
> * 데이터 프레임 생성 및 전송
> * 흐름 제어 및 오류 제어
> * 물리 주소를 통한 장치 간 통신
> 
> **주요 프로토콜**  
> * Ethernet, PPP(Point-to-Point Protocol), HDLC 등

> TCP/IP의 네트워크 접근 계층(Network Access Layer)에 해당

**네트워크 계층(Network Layer)**  
* 데이터 전송 경로(Route)를 결정
* IP 주소를 사용하여 네트워크 간 데이터 패킷 전송
* 라우팅, 패킷 분할 및 재조립 처리

> **주요 기능**  
> * 라우터를 통해 최적 경로 선택
> * 패킷 전달 및 주소 지정(IP 주소 기반)
>  
> **주요 프로토콜**  
> * IPv4, IPv6, ICMP, OSPF, BGP 등

> TCP/IP의 인터넷 계층(Internet Layer)에 해당

**전송 계층(Transport Layer)**  
* 애플리케이션 계층과 네트워크 계층 간 데이터 전달 담당
* 데이터 신뢰성 보장 및 연결 관리
* 포트 번호를 사용해 통신 프로세스 식별

> **주요 기능**  
> * 데이터 세그먼트 분할 및 재조립
> * 오류 제어, 흐름 제어, 혼잡 제어
> * 연결 설정 및 종료
> 
> **주요 프로토콜**  
> * TCP, UDP

> TCP/IP의 전송 계층(Transport Layer)에 해당

**세션 계층(Session Layer)**  
* 애플리케이션 간 세션(대화)의 설정, 관리, 종료 담당
* 데이터 교환 동기화 및 체크포인트 제공

> **주요 기능**  
> * 세션 설정, 유지, 동기화 및 복구
> * 애플리케이션 간 데이터 교환 논리적 흐름 관리

> TCP/IP 모델에는 세션 계층이 명시적으로 존재하지 않으며, 주로 애플리케이션 계층에 통합

**표현 계층(Presentation Layer)**  
* 데이터의 표현 형식 변환(인코딩, 디코딩, 암호화, 압축)
* 서로 다른 시스템 간 데이터 표현 방식 통합

> **주요 기능**  
> * 데이터 변환(ASCII <--> Unicode)
> * 암호화/복호화, 압축/해제

> TCP/IP 모델에는 표현 계층이 명시적으로 존재하지 않으며, 애플리케이션 계층에서 수행

**응용 계층(Application Layer)**  
* 사용자와 네트워크 간 인터페이스 제공
* 네트워크 서비스(웹, 이메일, 파일 전송 등)를 위한 애플리케이션 프로토콜 정의

> **주요 기능**  
> * 사용자가 필요로 하는 네트워크 서비스 제공
> * 데이터 형식 정의(웹 페이지, 이메일, 파일 등)
> 
> **주요 프로토콜**  
> * HTTP(S), FTP, SMTP, POP3, DNS, SSH 등
> 
> TCP/IP의 애플리케이션 계층(Application Layer)에 해당

### 네트워크 연결 장치의 기능
**리피터(Repeater)**  
* 물리 계층(OSI 1 계층) 장치
* 약해진 신호를 증폭하거나 재생성하여 더 먼 거리로 전송

> **특징**  
> * 데이터의 의미를 해석하지 않으며 단순히 신호 강도를 증폭
> * 네트워크 확장을 위해 사용
> 
> **주요 용도**  
> * 장거리 네트워크 연결, 예: 광섬유 신호 증폭

**허브(Hub)**  
* 물리 계층(OSI 1계층) 장치
* 데이터를 네트워크에 연결된 모든 장치로 브로드캐스트(전송)

> **특징**  
> * 데이터 충돌 발생 가능성이 높음(CSMA/CD 방식 사용)
> * 트래픽 관리 기능 없음
>
> **주요 용도**  
> * 소규모 네트워크에서 저렴하게 다수의 장치를 연결

**브리지(Bridge)**  
* 데이터 링크 계층(OSI 2계층) 장치
* 두 개의 LAN(Local Area Network)을 연결하여 하나의 네트워크처럼 동작
* MAC 주소를 기반으로 데이터 프레임 전달

> **특징**  
> * 네트워크 세그먼트를 나누고 트래픽을 분산
> * 충돌 도메인 감소
>
> **주요 용도**  
> * 네트워크 구간 분리 및 연결, 작은 규모의 LAN 네트워크 확장

**라우터(Router)**  
* 네트워크 계층(OSI 3계층) 장치
* 서로 다른 네트워크를 연결하고, IP 주소를 기반으로 데이터 전송 경로 결정(라우팅)

> **특징**  
> * 최적 경로 선택 및 패킷 전달
> * NAT(Network Address Translation)를 통해 사설 IP와 공인 IP 간 변환 가능
> 
> **주요 용도**  
> * 인터넷 연결, 네트워크 경로 선택 및 보안 설정

**IP 공유기(IP Router, Gateway)**  
* 라우터의 기능에 스위치와 무선 AP(Access Point) 기능을 포함한 장치
* 가정이나 소규모 사업장에서 인터넷 연결 공유

> **특징**  
> * NAT, DHCP 서버, 방화벽 등의 기능 포함
> * 다수의 디바이스가 인터넷을 동시에 사용할 수 있도록 지원
> 
> **주요 용도**  
> * 가정 및 사무실 네트워크에서 인터넷 공유 및 연결 관리
> * 추가적으로 알아두면 좋은 연결 장치

**스위치(Switch)**  
* 데이터 링크 계층(OSI 2계층) 또는 네트워크 계층(OSI 3계층) 장치
* MAC 주소를 기반으로 데이터를 특정 장치로 전달

> **특징**  
> * 충돌 도메인을 분리하여 네트워크 성능 향상
> * VLAN(가상 LAN)을 지원하는 스위치는 네트워크 세분화 가능
> 
> **주요 용도**  
> * 기업 네트워크의 트래픽 관리 및 확장

**게이트웨이(Gateway)**  
* OSI 4~7 계층 장치
* 서로 다른 프로토콜이나 네트워크 구조 간 데이터 변환 및 연결

> **특징**  
> * 데이터를 다른 네트워크로 변환(예: TCP/IP ↔ X.25)
> * 복잡한 데이터 변환과 필터링 기능
> 
> **주요 용도**  
> * 이기종 네트워크 연결, VoIP 게이트웨이 등

**모뎀(Modem)**  
* 물리 계층 장치
* 디지털 데이터를 아날로그 신호로 변환(변조), 또는 아날로그 신호를 디지털 데이터로 변환(복조)

> **특징**  
> * 전화선, 케이블 네트워크 등 아날로그 기반 매체를 통한 데이터 전송
> 
> **주요 용도**  
> * 인터넷 서비스 제공자(ISP)와의 연결

**무선 액세스 포인트(Wireless Access Point, AP)**  
* 네트워크 계층 장치
* 무선 장치가 유선 네트워크에 연결할 수 있도록 지원

> **특징**  
> * Wi-Fi 네트워크 생성
> * 네트워크 커버리지 확장
> 
> **주요 용도**  
> * 가정이나 공공 장소에서 무선 네트워크 제공

**NIC(Network Interface Card)**  
* 물리 계층 및 데이터 링크 계층 장치
* 컴퓨터와 네트워크 간 데이터 전송 지원

> **특징**  
> * 유선 또는 무선 연결 가능
> * MAC 주소를 포함하여 데이터 송수신
> 
> **주요 용도**  
> * 컴퓨터와 네트워크 연결

**방화벽(Firewall)**  
* 네트워크 계층(OSI 3계층) 및 전송 계층(OSI 4계층) 장치
* 네트워크 트래픽 필터링 및 보안 제공

> **특징**  
> * ACL(Access Control List) 기반 트래픽 제어
> * 하드웨어 또는 소프트웨어로 구현 가능
> 
> **주요 용도**  
> * 네트워크 보안, 내부 네트워크 보호

**로드 밸런서(Load Balancer)**  
* 응용 계층(OSI 7계층) 장치
* 서버 간 트래픽 분산 및 부하 분산 관리

> **특징**  
> * 클라우드 서비스 및 대규모 웹 애플리케이션에 사용
> 
> **주요 용도**  
> * 고가용성(HA) 및 확장성을 위한 네트워크 트래픽 관리

### RFC(Request For Comments)
* IETF(Internet Engineering Task Force)에서 제공하는 인터넷을 개발하고 사용하기 위해 필요한 절차나 핵심 기술을 정의한 문서

### IPv4와 IPv6의 역할과 비트 단위 구조

**IPv4(Internet Protocol version 4)**  
* 네트워크에서 장치(노드) 간의 통신을 위한 주소 체계를 제공

	**역할**  
	* 장치 간의 식별자(주소) 역할
	* 데이터 전송 경로 지정
	* 라우팅에 사용

	**비트 구조**  
	* 32비트
	* 4옥텟(octet)으로 구성되며, 각 옥텟은 8비트(1바이트)
	* 점-십진수 표기법
	* 192.168.0.1 (10진수로 표기된 4개의 옥텟)  

| 클래스 | 주소                                          |
| :-: | ------------------------------------------- |
|  A  | 0.0.0.0 ~ 127.255.255.255 (주로 대규모 네트워크에 사용) |
|  B  | 128.0.0.0 ~ 191.255.255.255 (중간 규모 네트워크)    |
|  C  | 192.0.0.0 ~ 223.255.255.255 (소규모 네트워크)      |
|  D  | 224.0.0.0 ~ 239.255.255.255 (멀티캐스트)         |
|  E  | 240.0.0.0 ~ 255.255.255.255 (연구용)           |

**IPv6(Internet Protocol version 6)**  
* IPv4의 주소 고갈 문제를 해결하고, 더 많은 기능성과 보안을 제공하기 위해 설계

	**역할**  
	* IPv4와 동일하나, **확장된 주소 공간**과 더 나은 네트워크 기능 제공

	**비트 구조**  
	* 128비트
	* 8개의 16비트 블록으로 구성되며, 각 블록은 16진수로 표현
	*  콜론-16진수 표기법
	*  `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

| **구분**     | **IPv4**                                 | **IPv6**                                    | **사용 예**         |
| ---------- | ---------------------------------------- | ------------------------------------------- | ---------------- |
| **주소 크기**  | 32비트                                     | 128비트                                       | -                |
| **표기법**    | 점-십진수 (`192.168.0.1`)                    | 콜론-16진수 (`2001:0db8:85a3::8a2e:0370:7334`)  | -                |
| **유니캐스트**  | 일반 A, B, C 클래스 주소 사용                     | 유니캐스트 주소 (Global, Link-local, Unique-local) | 웹 브라우징, 이메일 전송   |
| **멀티캐스트**  | D 클래스 주소 (`224.0.0.0 ~ 239.255.255.255`) | FF00::/8 (멀티캐스트 전용 범위)                      | 스트리밍 서비스, 비디오 회의 |
| **브로드캐스트** | 255.255.255.255 (모든 호스트 대상)              | 멀티캐스트로 대체 (예: FF02::1, 모든 노드 대상)            | ARP 요청           |
| **애니캐스트**  | 지원되지 않음                                  | 유니캐스트 주소를 사용한 특정 노드로의 가장 가까운 전송 방식          | DNS 요청           |
### TCP/IP 주요 프로토콜
1. **네트워크 접근 계층**   
	
	**ARP(Address Resolution Protocol)**  
	* IP 주소를 사용해 해당 IP에 대응하는 MAC 주소(물리 주소)를 조회
	* 로컬 네트워크에서만 동작, 라우터를 넘어서는 동작을 수행하지 않음
	* ARP 캐시를 사용해 이전 조회 결과를 저장해 성능을 향상
	* ARP 스푸핑 공격과 같은 보안 취약점 존재

	**Ethernet, Wi-Fi**  
	* 네트워크 접근 계층 표준

	**PPP(Point-to-Point Protocol)**  
	* 점대점 연결을 위한 프로토콜

2. **인터넷 계층**
	
	**IP(Internet Protocol)**  
	* 데이터의 목적지로 이동을 책임지는 핵심 프로토콜
	* IPv4, IPv6

	**ICMP(Internet Control Message Protocol)**  
	* 네트워크 오류 보고 및 진단
	* 비연결형 프로토콜로, 데이터를 직접 전송하거나 수신하는 데 사용되지 않음
	* DoS 공격(Ping Flood)과 같은 악용 사례 존재
	> **주요 메시지 유형**  
	> * Echo Request/Reply : 흔히 핑 명령어로 사용. 연결 상태를 확인
	> * Destination Unreachable : 대상에 도달할 수 없을 때 발생
	> * Time Exceeded : 패킷이 네트워크에서 유효기간(TTL)을 초과했을 때 발생

	**IGMP(Internet Group Management Protocol)**  
	* 멀티캐스트 그룹 관리(설정 및 유지)
	* 네트워크 리소스를 절약하며, 멀티캐스트를 지원하는 네트워크에서만 동작
	* 스트리밍 서비스, 게임 등 여러 사용자에게 동일한 데이터를 동시에 전송할 때 유용

	**RARP(Reverse Address Resolution Protocol)**  
	* ARP의 반대 역할로, MAC 주소로 IP 주소를 찾음(거의 사용되지 않음)

3. **전송 계층**  

	**TCP(Transmission Control Protocol)**  
	* 연결 지향적, 신뢰성 보장
	* 데이터 재전송, 오류 복구, 데이터 순서 보장
	> **3-way handshake로 연결 설정**  
	> 1. SYN(Synchronize) : 클라이언트가 서버에 연결 요청
	> 2. SYN-ACK : 서버가 요청을 수락
	> 3. ACK(Acknowledge) : 클라이언트가 응답

	**UDP(User Datagram Protocol)**  
	* 비연결형, 신뢰성은 낮지만 속도가 빠름

4. **애플리케이션 계층**  

	**HTTP/HTTPS**  
	* HTTP : 웹 페이지 요청 및 전송
	* HTTPS : HTTP 보안 프로토콜(TLS/SSL) 추가

	**FTP(File Transfer Protocol)**  
	* 파일 전송 프로토콜
	* 암호화 되지 않아 SFTP(SSH 기반), FTPS(SSL 기반)을 대안으로 사용

	**DNS(Domain Name System)**  
	* 사람이 읽을 수 있는 도메인 이름을 IP 주소로 변환
	> **작동 원리**  
	> 1. 클라이언트가 로컬 DNS 서버에 쿼리
	> 2. 로컬 DNS 서버가 캐시에 없는 경우 상위 DNS 서버로 전달
	> 3. 최종적으로 IP 주소를 반환

	**SMTP(Simple Mail Transfer Protocol)**  
	* 이메일 송신 프로토콜

	**POP3/IMAP**  
	* POP3 : 이메일 다운로드 후 로컬 저장
	* IMAP : 이메일을 서버에 남겨두고 동기화

	**DHCP(Dynamic Host Configuration Protocol)**  
	* IP 주소를 자동으로 할당
	* 네트워크에 새로운 장치가 연결될 때 설정을 간소화
	> **임대 기간(Lease Time)**  
	> * 할당된 IP 주소가 특정 시간 동안만 유효
	> * 만료 시 새 주소 요청

	**SNMP(Simple Network Management Protocol)**  
	* 네트워크 장치(라우터, 스위치, 서버 등)의 상태 모니터링 및 관리
	> **구성 요소**  
	> * SNMP Manager : 네트워크 관리자가 사용하는 소프트웨어
	> * SNMP Agent : 장치에 설치된 소프트웨어
	> * MIB(Management Information Base) : 관리 가능한 데이터베이스