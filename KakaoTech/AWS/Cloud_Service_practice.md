
#### AWS, Google Cloud, Azure 중 하나를 선택하여 계정을 생성합니다.
#### 선택한 클라우드 서비스 제공자에서 가상 머신(VM)을 생성하고 설정합니다.
1. AWS 서비스 중 EC2 검색
2. 리전 선택 - 오른쪽 위 사용자 이름 옆에 해당 메뉴가 있다.
3. 인스턴스 시작하기
	- 이름: Kakao-Cloud-practice
	- 애플리케이션 및 OS 이미지
		- Amazon Linux (CentOS 기반):  `yum` 패키지 명령어, 무료 
		- Ubuntu(Debian 기반): `apt` 패키지 명령어, 무료
		- Window Server(Window 기반): Linux기반 OS와 달리 인스턴스 사용 시간에 따라 라이선스 비용을 추가로 청구
		- Red Hat Enterprise Linux : `yum`, `dnf` 패키지 명령어, 보안 또는 상용지원의 장점, 인스턴스 사용 시간에 따른 비용 청구
	- 인스턴스 유형
		- 프리티어 - t2.micro
		- 범용 인스턴스: 균형잡힌 컴퓨팅 - t3, m5 시리즈
		- 컴퓨팅 최적화: 높은 CPU 성능 - c5 시리즈
		- 메모리 최적화: 가용성 높은 메모리 - r5 시리즈
		- 가속화 컴퓨팅: GPU 사용 - p3, g4 시리즈
		- 스토리지 최적화: 대용량, 고속 스토리지 처리 - i3, d2 시리즈
	- 키페어(로그인) - 외부에서 ssh로 접근하기 위해 필요한 보안키
		- SSH 접속을 위해 키페어 지정을 추천
	- 네트워크 설정
		- 보안 그룹 생성 
			- 방화벽에서 SSH, HTTP 트래픽 허용
		- 보안 그룹 편집을 통해 개별적으로 생성한 VPC에 연결하여 사용 할 수 도 있다.
	- 스토리지 구성
		- 균형잡힌 성능과 비용  - gp3, gp2  (gp3가 종합적인 측면에서 좋다)
		- 높은 IOPS (기존 SSD보다 빠른 성능을 필요로 할때) - io1, io2
 4. 인스턴스 생성

#### VM에 SSH를 사용하여 접속하고, 기본적인 명령어를 실행해봅니다.
1. 생성된 해당 인스턴스 클릭
2. 퍼블릭 IPv4 주소 복사
3. 터미널에서 Download가 된 보안키(.pem)의 경로로 이동
4. `chmod 400 new_key.pem`
5.  `ssh -i ec2-user@아이피 입력`
6. Are you sure you want to continue connecting (yes/no/[fingerprint])? - Yes
7. 다양한 명령어 입력

#### 스토리지 서비스를 설정하고, 샘플 파일을 업로드합니다.

**Elastic Block Store (EBS)**

- 특징
	- 고성능 블록 스토리지 서비스
	- EC2 인스턴스의 부트 볼륨
	- 데이터베이스 스토리지
- 설명
	- 데이터를 일정한 크기의 '블록'으로 나누어 저장하는 방식
	- 나뉜 블록은 각각 고유한 주소를 가지고 있고, 이 블록을 재구성하여 데이터를 가지고 올 수 있다.
		-  == 컴퓨터 드라이브 파티션
	- 블록 스토리지마다 고유한 주소가 있기때문에 **다양한 접근 경로를 가질 수 있어 신속한 검색 가능**
	- 파티션 분할 가능 > 서로 다른 OS에서 액세스 가능ㅡ
- 사용 방법
	- 사용할 인스턴스 선택 > 스토리지 탭 > 해당 스토리지 클릭(볼륨)
	- 해당 볼륨 작업 > 볼륨 수정 > 크기 늘리기 > 수정
	- 스토리지는 처음 마운트 된 기준으로 작동하기 때문에 변경된 용량에 대해 재 마운트 해줘야 함 
		- `sudo growpart /dev/xvda 1`
		- `sudo xfs_growfs /dev/xvda1`
		- `lsblk` 로 정상 마운트 확인


**Elastic File System(EFS)

- 특징
	- 여러 EC2 인스턴스에서 공유 할 수 있는 파일 스토리지 서비스
- 설명
	- 폴더와 파일로 이뤄진 계층 구조
		- == 윈도우 탐색기
		- 폴더 안에 폴더, 파일 존재
	- 파일을 찾으려면 파일 경로를 알아야 한다
		- 파일이 적다면 분류, 정리하는데 큰 문제는 없다.
		- 파일이 많다면 찾기 힘들 수 있음 
- 사용방법
	1. EFS 검색 > 파일 시스템 생성
		- 이름 
		- 인스턴스와 동일한 VPC 선택
		- 생성
	2. 네트워크 탭
		- 생성되고 나면 보안 그룹이 임의로 지정되는데
		- 관리 버튼
		    - 보안그룹을 아까 생성한 그거로 연결
		    - 저장
	3. 연결 버튼
	    - IP를 통한 탑재
	    - NFS 클라이언트 사용 명령어 복사
	    - `mkdir efs`
    4. 터미널에서 붙여넣기 : 만들어둔 폴더가 EFS에 마운트 됨
        - 쓰기가 진짜 느린편 > 한번 해놓고 여러 인스턴스에서 참조하는 경우로 쓰임

**Simple Storage Service(S3)

- 특징
	- 확장성이 뛰어난 오브젝트 스토리지 서비스
	- 웹 콘텐츠 저장소
	- 백업 저장소
- 설명
	- 논리적인 스토리지
	- 블록스토리지, 파일스토리지는 OS 단에서 동작하는 반면, 오브젝트 스토리지는 애플리케이션 단에서 동작하기때문에 **물리적 제약이 없어 확장에 용이**하다.
	- 데이터 조각을 객체 형태로 지정하고 개별 데이터 단위로 저장
	- 객체는 사진, 비디오와 같은 비정형 데이터 / 기계 학습(ML) / 센서 데이터 등 모든 데이터 포괄
	- 계층 구조 없는 평면 구조
		- 쉽고 빠르며 확장성 높음

- 사용 방법
	1. 버킷 생성
		- 이름
		- 퍼블릭 액세스 차단 체크 해제
		- 생성
	2. 생성한 버킷
		- 권한 > 버킷 정책 > 편집
	3. 파일 업로드
	4. 업로드한 파일 클릭하고 URL 로 접속하여 정상 업로드 확인



#### 클라우드 데이터베이스 서비스를 설정하고, 간단한 테이블을 생성합니다.

종류

| Type                  | Description                                                                               | Service                              |
| --------------------- | ----------------------------------------------------------------------------------------- | ------------------------------------ |
| Relational<br>관계형     | 관계형 데이터베이스는 미리 정의된 스키마와 이들 간 관계를 통해 데이터를 저장                                               | Amazon Aurora,  RDS, RedShift        |
| key - value<br>키 - 값  | 보통 많은 데이터를 저장하고 검색하기 위한 일반적인 액세스 패턴에 최적화되었습니다. 이러한 데이터베이스는 동시 요청이 매우 많은 경우에도 빠른 응답 시간을 제공 | Amazon DynamoDB                      |
| In-memory<br>인 메모리    | 실시간으로 액세스해야 하는 애플리케이션에 사용                                                                 | Amazon ElasticCache, MemoryDB        |
| Document<br>문서        | 반정형 데이터를 JSON과 비슷한 문서로 저장                                                                 | Amazon DocumentDB(MongoDB 호환)        |
| Graph<br>그래프          | 상호 연결성이 높은 그래프 데이터세트 간에 수백만 건의 관계를 밀리초의 지연 시간 내에 대규모로 쿼리 및 탐색해야 하는 애플리케이션에 사용             | Amazon Neptune                       |
| Wide column<br>와이드 컬럼 | NoSQL, 테이블, 행 및 열을 사용하지만 관계형 데이터베이스와는 달리 열의 이름과 형식은 동일한 테이블에서 행마다 다를 수 있습니다.              | Amazon Keyspaces                     |
| Time Series<br>시계열    | 정해진 시간 간격으로 쿼리를 수행하여 시간에 따라 변화하는 데이터를 효율적으로 수집 및 동기화하고 여기에서 인사이트를 도출                      | Amazon TimeStream                    |
| Ledger<br>원장          | 모든 애플리케이션에 대해 확장 가능하고 변경 불가능하며 암호로 검증 가능한 트랜잭션 레코드를 유지 관리하기 위해 신뢰할 수 있는 중앙 위치(권한)를 제공     | Amazon Ledger DataBase Service(QLDB) |
- 관계형
	- Amazon Aurora: MySQL, PostgreSQL / 1/10의 비용으로 상용 데이터베이스 수준의 성능 이용
	- Amazon RDS: 클릭 몇 번으로 클라우드에서 관계형 데이터베이스 설정, 운영 및 조정
	- Amazon RedShift: 데이터 웨어하우스 / 가장 빠르고 가장 널리 사용되는 클라우드 데이터 웨어하우스에서 모든 데이터 분석
- 키-값
	- Amazon DynamoDB: 모든 규모의 키-값 및 문서 워크로드를 지원할 수 있는 빠르고 유연한 서버리스 NoSQL 데이터베이스
- 인메모리
	- Amazon ElasticCache: Redis OSS 또는 Memcached와 호환되며 대기 시간이 밀리초 단위인 확장 가능한 캐싱 서비스입니다. 
	- Amazon MemoryDB: Redis OSS 호환성 및 내구성을 바탕으로 초고속 성능을 제공하는 인 메모리 데이터베이스 서비스입니다.
- 문서
	- Amazon DocumentDB(MongoDB 호환): JSON 워크로드의 크기를 손쉽게 조정
- 그래프
	- Amazon Neptune : 고도로 연결된 데이터 집합에서 작동하는 애플리케이션을 구축 가능
- 와이드 컬럼
	- Amazon Keyspaces: Apache Cassandra 워크로드를 실행할 수 있는 고가용성의 확장 가능한 관리형 와이드 컬럼 데이터베이스 서비스
- 시계열
	- Amazon TimeStream: 매일 수조 개의 이벤트를 저장하고 분석할 수 있는 빠르고 확장 가능한 서버리스 시계열 데이터베이스 서비스
- 원장
	- Amazon Ledger DataBase Service(QLDB): 투명하고 변경 불가능하며 암호로 검증 가능한 트랜잭션 로그를 제공하는 완전관리형 원장 데이터베이스


사용방법(RDS 사용)
1. 데이터 베이스 생성
	- 데이터베이스 생성 방식 - 표준 생성
	- 엔진 옵션 - MySQL
	- 템플릿 - 프리티어
	- 설정
		- DB 인스턴스 식별자: kakao-database
		- 마스터 사용자 이름: admin
		- 자격 증명 관리 - 자체 관리
		- 마스터 암호
	- 인스턴스 구성 - 버스터블 클래스 db.t3.micro
	- 스토리지
		- 범용 SSD - gp2
		- 용량 - 20
		- 스토리지 자동 조절 비활성화
	- 연결
		- 컴퓨팅 리소스 - EC2 컴퓨팅 리소스에 연결하지 않기: 나중에 수동으로 연결 할 수 있음
		- VPC 정보 - 기본
	- 추가 연결 구성
		- 퍼블릭 액세스 - 예
		- VPC 보안 그룹 - 새 보안그룹 생성 : 현재 사용하고 있는 IP 주소에서 생성된 데이터베이스로 연결할 수 있는 보안 그룹이 생성됨
		- RDS 프록시: 애플리케이션이 데이터베이스 연결을 풀링하고 공유하도록 허용하여 확장 능력을 개선 - 선택x
		- 포트 - 3306
	- 데이터베이스 인증 - 암호 인증
	- 모니터링 - 비활성화
	- 추가 구성
		- 데이터베이스 이름: 데이터 베이스 이름 지정
		- DB 파라미터 그룹 
		- 옵션그룹 
2. SQL 클라이언트 다운로드 및 설치
3. MySQL 데이터베이스 연결
	- 보안 그룹 규칙에서 해당 보안그룹의 인바운드 규칙 - MYSQL(3306) - 0.0.0.0/0 을 허용해줘야지만 외부에서 접근 할 수 있다. 
		- 보안 그룹 적용 후 혹시 모르니 데이터베이스 재부팅
	- MySQLWorkBench 실행 > Database 탭 > Connect to DataBase
	- aws에서 생성한 데이터 베이스
		- 호스트 이름 - 생성한 데이터베이스의 엔드포인트 값
		- 포트 - 3306
		- 사용자명 - admin
		- 비밀번호 - 아까 생성한 비밀번호 입력
	- 확인
4. 연결한 데이터베이스에서 테이블 생성
	- Schema 탭 선택
	- 초기에 생성해둔 스키마 선택
	- Tables 우클릭 > Create Table
		- Table Name
		- Column: 컬럼 이름
		- Data Type: 데이터 형식 지정
		- PK: primary key
		- NN: not null, 값에 null 값 금지
		- UQ: unique, 고유한 값을 가져야함
		- BIN: binary, 이진 형식의 값
		- UN: unsigned, 숫자 데이터 타입에서 음수 금지
		- ZF: zero fill, 숫자 값을 0으로 채워 일정한 길이로 표시되게
			- ex. ZF가 5이고 데이터가 1, 3일때 -> 00001, 00003
		- FK: foreign key, 다른 테이블의 primary key와 연결되게
	- apply > apply 하면 테이블 생성
	- 생성한 테이블 우클릭 > Select row:  해당 테이블의 데이터 확인 




[AWS RDS 생성하기 출처](https://aws.amazon.com/ko/getting-started/hands-on/create-mysql-db/)
[MySQLWorkbench 스키마, 테이블, 데이터 추가 출처](https://pinetreeday.tistory.com/145)