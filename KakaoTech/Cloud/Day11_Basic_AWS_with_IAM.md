#### AWS IAM이란? Identity and Access Management
:  AWS 리소스에 대해서 권한 관련된 내용을 통틀어 부름
- 사용자와 그룹을 생성하고, 리소스에 대한 권한을 관리 > 오브젝트 단위로 권한을 조정 할 수 있다.

중요성
- 보안 유지 
	- 리소스에 대한 권한을 세부적으로 조정할 수 있다.
- 비용 관리
	- 권한을 적절히 사용하여 불필요한 리소스를 줄여 비용을 절감 > ex. rds 굳이 쓸 이유가 없다면 ec2에서 db를 설치하는게 낫기 때문에 rds생성 권한을 제한하게끔

IAM 모범 사용
- 최소 권한 원칙 : 요청이 들어오는 권한만 허용하고 그 이외는 막게끔 > 보안 유지든 비용 관리든
- IAM 정책 관리 : 주기적으로 사용자가 필요로 하는 권한을 정책단위로 관리하기 위함
- MFA사용: 멀티 팩터 인증 == "OTP"와 같은걸로 2차 인증 사용을 통해 보안을 강화
- IAM 사용자 및 역할 모니터링 : CloudTrail(로그 모니터링) 사용하여 IAM 이벤트를 로깅하고 모니터링 


#### IAM 유저란? 
- AWS 리소스에 접근하기 위한 개별 엔티티
: ~ > 사실상 aws에 로그인 할 수 있는 유저를 발급해준다  > 이러한 유저는 aws 는 고유 id를 통해서 관리
#### IAM 그룹이란?
- 여러 사용자에게 공통적인 권한 부여
: ~ > 리눅스처럼 권한 묶어서 통제하기 위해 > 실제로 전 직장은 유저 단위로 관리했고, 현 직장에서는 역할 단위로 관리함 
#### IAM 정책이란? 
- AWS 리소스에 대한 접근 권한을 정의하는 JSON

보통 정책단위로 권한 관리를 하는데
- 관리형 정책
	- 실습때 policy 를 보며 확인하면 좋음
	- 기본적으로 생성된 aws 정책  > 보통 만들어진거 부여하는 형식
	- 고객 관리형 정책 > 만들어둔거 빼는식으로 관리함
- 인라인 정책
	- 사용자, 그룹에 귀속되는 정책
	- 더 세밀하게 권한을 조정하는 정책
	- 요청이 들어올때마다 조금씩 준다 > 이때 인라인을 통해 권한을 부여하는 정책을 주로씀

#### 정책의 구성 요소
총 다섯가지의 구성
- statement : 기본적으로 하나의 정책에 하나의 statement
	- effect : 접근을 통제하는 식으로 권한을 관리하기도 함 - 허용 or 비허용
	- action: s3에 들어가는 다양한 액션 권한을 통제
	- resource: 권한을 통해서 통제할 대상을 지정 > 특정 오브젝트, ec2등 
	- condition: 필수는 아님 있으나 없으나 상관없음
- + 하나 더 있긴함

- 정책 예시
```json
version #알아서 박힘
statement // 알아서 
	efftect // 특정 허용하겠다 라는 뜻
	action // s3에 모든 버킷 리스트를 보여지는것을 허용하겠다. 
	resource // 못들음 > gpt에게 물어보기 
			 // arn:aws:s3:ap-northeast-2:587169513591:asdfasdf > 이렇게끔 사용됨
			 //   arn:  ?  :오브젝트종류:리전:유저아이디:인스턴스아이디
```

#### IAM 역할(Role)
- 역할(Role): 특정 AWS 서비스(인스턴스)나 다른 계정의 사용자가 AWS 리소스에 접근 할 수 있도록 임시 보안 자격 증명 제공
- 정책(Policy) : 역할에 연결된 권한 정의 JSON
- 신뢰 정책(Trust Policy): 역할을 맡는 엔티티 정의 > 잘 안씀 > 이 역할을 수행할 수 있도록 누가 영향력을 행사할 것이냐 
- 역할 전환(Role Assumption): 정책에 부여된 권한을 role을 통해 할 수 있다.

- 역할이라고 하면 부여받을 수 있는 대상이 한정적인 것이 아니라 유저부터 인스턴스까지 다양하게 
- 여러가지 정책을 부여 할 수 있는 역할이라고 생각하면 되고  

사용 방식 
	- AWS 서비스 간의 권한 부여: ec2 인스턴스가 s3 버킷에 접근 할 수 있도록 역할 사용
	- 계정 간 권한 부여: AWS 사용자가 다른 AWS 계정 리소스에 접근 허용
	- 단기 자격 증명 : 특정 기간만 접근 권한 부여
