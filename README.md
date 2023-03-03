# Amazon RDS

## Relational Database Service(RDS)
### 개요
> Amazon Web Service 에서 관리해주는 Relational Database 서비스

### Endpoint
> Aurora 는 3가지 앤드포인트를 제공한다.
>
> 1.Cluster endpoint: 클러스터 앤드포인트는 primary instance 와 연결되며,
> failover 에 따라 primary instance 가 기존의 인스턴스에서 다른 인스턴스로 변경되어도 애플리케이션에서 cluster endpoint 로 연결되어 있다면
> 변경된 primary instance 의 주소로 변경할 필요가 없다.
>
> 2.Reader endpoint: Aurora Replica 중 하나와 연결(부하 분산으로 연결됨). 만약 primary instance 가 drop 되어서 Aurora Replica 중 하나의
> 인스턴스가 primary instance 로 변경되게 되면, 변경되는 그 시간 동안 모든 읽기 요청이 drop 됨.
>
> 3.Instance endpoint: DB 인스턴스에 직접 연결. 권장하진 않음.

### Minor version update
> RDS 는 클러스터 설정에서 지정된 시간에 마이너 업데이트를 자동으로 진행하도록 스케줄링할 수 있음.  
> 마이너 업데이트는 해당 클러스터의 인스턴스 모두가 한꺼번에 업데이트가 진행되기 때문에 약 20~30분의 다운타임이 발생됨.  
> 마이너 버전 자동 업데이트를 on 한 상태에서 스케줄링을 따로 지정하지 않으면 랜덤한 시간에 마이너 업데이트를 자동으로 진행할 수 있음.  
> 
> 무중단 업데이트  
> 현재 RDS 의 Minor version update 는 무중단 업데이트가 불가능하다.  

### 스냅샷
> RDS 의 스냅샷은 클러스터에서 설정하며 인스턴스에서는 설정하지 못함.  
> 특정 시점의 DB 클러스터 상태 그대로를 스냅샷으로 저장할 수 있음.  
> 하나의 DB 인스턴스만 사용하는 경우 스냅샷 생성 과정에서 몇 초에서 몇 분 동안 DB I/O 가 지연될 수 있음.  
> 
> 스냅샷 생성  
> 기본적으로 클러스터 생성 시 백업 보존 기관 설정을 통해서 스냅샷을 몇일분을 생성해둘지 지정할 수 있다.    
> 메뉴얼 스냅샷 생성을 원하는 경우 RDS 콘솔에서 클러스터 선택 후 작업에서 `스냅샷 생성` 항목을 클릭하여 생성.  
> 하나의 DB 인스턴스만 사용하는 경우 스냅샷 생성 과정에서 몇 초에서 몇 분 동안 DB I/O 가 지연될 수 있음.
> 
> 스냅샷 확인  
> RDS 콘솔에서 클러스터 선택 후 유지 관리 및 백업 탭에서 백업 관련 정보와 아래 스냅샷 목록에서 스냅샷을 확인할 수 있음.  
> 
> 스냅샷으로 복원  
> 스냅샷으로 복원을 원하는 경우 스냅샷을 통해서 신규 인스턴스를 생성하는 방식으로 동작한다. 주의할 점은 기존에 실행되고 있는 인스턴스를 스냅샷의 상태로 복원하는 방식이 아니다.  
> 스냅샷으로부터 생성된 인스턴스의 parameter group 및 security group 이 default 설정으로 지정되어 있으며, 이는 인스턴스 생성 완료 후에 변경할 수 있다.

### 블루/그린 배포
> 서비스의 무중단 배포 방식 중 하나.  
> 기존 배포분이 Blue group, 신규 배포분이 Green group 으로 나누어서 구분.
>
> 배포 프로세스  
> 1.서비스 앞에 로드밸런서를 두고 기존 배포분인 Blue group 을 유지한 상태에서 신규 배포분인 Green group 를 신규로 추가.  
> 2.로드밸런서에 기존에 연결된 Blue group 을 그대로 유지하고 Green group 을 새로 연결.   
> 3.Green group 이 로드밸런서에 연결이 완료되면 Blue group 을 로드밸런서에서 제거.   
> 4.Blue group 을 제거.
>
> 참조사이트: [[AWS] 블루/그린(Blue/Green) 배포 방식이란?](https://devlog-wjdrbs96.tistory.com/300)
>
> Amazon RDS Blue/Green Deployments  
> MySQL 호환성, Amazon RDS for MySQL 및 Amazon RDS for MariaDB와 함께 데이터베이스를 더 안전하고 더 간단하고 더 빠르게 업데이트할 수 있는
> 새로운 Amazon Aurora 기능인 Amazon RDS Blue/Green Deployments의 정식 출시하였음.
>
> Blue/Green Deployments를 사용하여 프로덕션 환경(Blue)을 미러링하는 별도의 동기화된 완전관리형 스테이징 환경(Green)을 생성할 수 있습니다.
> 스테이징 환경(Green)에서는 프로덕션 환경(Blue)의 프라이머리 데이터베이스와 리전 내 읽기 전용 복제본을 복제합니다.
> Blue/Green Deployments에서는 논리적 복제를 사용하여 이 두 가지 환경을 동기화된 상태로 유지합니다.
>
> 1분이면 데이터 손실 없이 스테이징 환경을 새로운 프로덕션 환경으로 활성화할 수 있습니다.
> 전환 중에 그린이 블루를 따라가도록 블루 환경과 그린 환경에서의 쓰기를 `Blue/Green Deployments`에서 차단하여 데이터 손실이 없도록 합니다.
> 그런 다음에 애플리케이션 코드 변경 없이 `Blue/Green Deployments`에서 프로덕션 트래픽을 새로 활성화된 스테이징 환경으로 리디렉션합니다.

### CloudWatch
> 알람 설정  
> 평균 CPU 점유율 등의 임계 값 지표를 설정하여 임계 값을 넘으면 이메일/SMS 로 알람을 보내주는 기능을 제공한다.  
> 클러스터로는 알람 설정이 적용되지 않으며 인스턴스 별로 알람 설정을 적용한다.  
> 인스턴스 클릭 후 로그 및 이벤트 탭에서 CloudWatch 경보에서 경보 생성 버튼을 클릭하여 경보를 생성한다.

---

## Aurora
### 개요
> Aurora 는 컴퓨팅 영역(mysql 프로세스) 과 Storage 영역(하드 디스크) 이 분리되어 각 영역의 가용성 및 확장성을 확보한다.    
> Storage 용량은 Aurora 사용 시 따로 지정하지 않으며 128TB 까지 용량이 자동으로 늘어난다.

### Cluster
> RDS 의 DB 는 클러스터로 구성하며, 클러스터는 2개 타입의 인스턴스로 구성된다.  
> 첫번째는 Primary instance 이며, 두번째는 Aurora Replica 이다.   
> Primary instance: 읽기와 쓰기가 모두 가능한 인스턴스 이며 클러스터에서는 하나의 primary instance 만 갖는다.    
> Aurora Replica: 쓰기만 가능한 인스턴스. 하나의 Aurora DB Cluster 에는 최대 15개의 Aurora Replica 와 1개의 Primary instance 를 지원한다.

### 고가용성
> Aurora DB 는 다음 3가지를 통해서 고가용성을 유지한다.
> 
> 1.Storage auto-repair: 3개의 AZ 에 데이터를 copy 함으로써 disk failure 를 최소화 및 fix(고침) 처리하며,
> 데이터 유실(data loss)를 피한다.
> 
> 2.Survivable cache warming: DB 가 crash 되어서 재실행 되었을 때 자주 실행한 쿼리들을 버퍼 풀에 저장시켜 데이터베이스의
> 성능을 높인다.
> 
> 3.Crash recovery: Aurora DB 는 페러렐 쓰레드를 통하여 비동기를 사용하여 Crash 로 부터 즉시 복구한다.  

### Failover
> Aurora 에서는 Primary instance 에 crash 가 발생하여 비정상 종료되면 Aurora Replicas 중 하나를 자동으로 Primary instance 로 승격시킨다.  
> Aurora 에서는 Aurora Replicas 에서 Primary instance 로 승격시키는 동안의 데이터 유실(data loss)이 없다.  
> 
> RDS 콘솔 창에서 Primary instance 선택 후 작업에서 장애 조치(Failover) 항목을 클릭해서 수동으로 장애조치를 할 수 있다.  
> 해당 장애 조치는 기존 Primary instance 를 Aurora Replicas 로 내리고, Aurora Replicas 의 인스턴스를 Primary instance 로 승격시키는 작업이다.  
> 그렇기 때문에 싱글 인스턴스를 운영하는 경우에는 사용할 수 없다.  
> 
> 싱글 인스턴스인 경우 장애조치  
> 일반적으로 Failover 자동 처리를 위해서는 최소 Primary instance 1개와 Aurora Replicas 인스턴스 1개를 운영하는 것이 좋다. 
> 하지만 부득이하게 싱글 인스턴스를 사용하는 경우에는 자동 Failover 가 없음으로 RDS 콘솔에서 Primary instance 를 선택 후 작업에서 재부팅을 통해서 
> 장애 조치를 진행한다.

### Aurora Replica 추가
> RDS 콘솔에서 Aurora DB 클러스터를 선택한 후에 작업에서 `읽기 추가` 버튼을 클릭하여 Aurora Replica 를 추가할 수 있다.  

---

## DB instance
### 설명
> 격리된 데이터베이스 환경. RDS 에서 가장 기반이 되는 블록이 DB instance.

### DB instance class
> DB instance 가 사용할 컴퓨팅 스펙. ex) db.t3.medium, db.r5.large, 등등 

### DB instance storage
> DB instance 에서 사용할 storage. General Purpose(SSD), Provisioned IOPS(PIOPS), Magnetic 이 있음.

### 인스턴스 타입 변경
> scale up / scale down  
> RDS 콘솔에서 타입을 수정하려는 DB 인스턴스를 클릭 후 수정 버튼을 클릭한다.    
> 수정 화면의 인스턴스 구성 섹션에서 인스턴스 타입을 변경한다.  
> 인스턴스 타입 및 다른 설정 등을 수정 후 즉시 적용과 다가오는 유지보수에 적용할지 선택할 수 있다.  
> 즉시 적용 시 DB 를 재구동해야하는 경우가 존재할 수 있기 때문에 테스트 환경에서 먼저 적용하여 테스트 후 적용을 권장한다.

---

## Web Application - SpringBoot
### Writer / Reader 인스턴스 부하 분산
> `@Transactional(readOnly=true)` 인 서비스 메서드 관하여 Reader 인스턴스를 호출하도록 한다.  
> 
> 조건
> 1.JDBC Driver 로 AWS JDBC Driver for MySQL 을 사용한다.
> 
> 2.application.yml 에 Datasource 연결 시 url 에 `jdbc:mysql:aws://<클러스터 엔드포인트 주소>` 를 입력한다.  
> 주소는 라이터 인스턴스의 주소가 아닌 리전 클러스터의 엔드포인트 중에서 유형이 라이터 인스턴스인 엔드포인트 주소를 입력한다.
>
> 동작 원리  
> AWS JDBC Driver for MySQL 의 db connection 에서 Datasource url 에 aws 가 붙어있으면 reader 인스턴스의 주소와 writer 인스턴스의 주소를 
> 필요에 맞게 얻어와서 사용한다. 
> 
> AWS JDBC Driver for MySQL 의 db connection 이 알아서 동작하기 때문에 application.yml 에서 Datasource url 로 reader 및 writer 
> 주소를 각각 입력하고 reader Datasource 와 writer Datasource 를 각각 만드는 수고로움이 필요없다.  

### 참조사이트
> [JPA에서 Reader DB 사용하기 (feat. AWS Aurora)](https://jojoldu.tistory.com/515)  
> [[입 개발] MariaDB Connector 와 AWS Aurora](https://charsyam.wordpress.com/2021/12/11/%EC%9E%85-%EA%B0%9C%EB%B0%9C-mariadb-connector-%EC%99%80-aws-aurora/)  
