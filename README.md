# Amazon RDS

## Relational Database Service
### 개요
> Amazon Web Service 에서 관리해주는 Relational Database 서비스

## Aurora
### 개요
> Aurora 는 컴퓨팅 영역(mysql 프로세스) 과 Storage 영역(하드 디스크) 이 분리되어 각 영역의 가용성 및 확장성을 확보한다.    
> Storage 용량은 Aurora 사용 시 따로 지정하지 않으며 128TB 까지 용량이 자동으로 늘어난다.

### Cluster 
> Aurora 는 클러스터로 구성하며, 클러스터는 2개 타입의 인스턴스로 구성된다.
> 첫번째는 primary instance 이며, 두번째는 Aurora Replica 이다.  
> Primary instance: 읽기와 쓰기가 모두 가능한 인스턴스 이며 클러스터에서는 하나의 primary instance 만 갖는다.  
> Aurora Replica: 쓰기만 가능한 인스턴스. 하나의 Aurora DB Cluster 에는 최대ㅑ 15개의 Aurora Replica 와 1개의 Primary instance 를 지원한다.  

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

### Minor update
> RDS 는 클러스터 설정에서 지정된 시간에 마이너 업데이트를 자동으로 진행하도록 스케줄링할 수 있음. 
> 마이너 업데이트는 해당 클러스터의 인스턴스 모두가 한꺼번에 업데이트가 진행되기 때문에 약 20~30분의 다운타임이 발생됨.  
> 마이너 버전 자동 업데이트를 on 한 상태에서 스케줄링을 따로 지정하지 않으면 랜덤한 시간에 마이너 업데이트를 자동으로 진행할 수 있음.  

### 알람 설정
> 평균 CPU 점유율 등의 임계 값 지표를 설정하여 임계 값을 넘으면 이메일/SMS 로 알람을 보내주는 기능을 제공한다.  
> 클러스터로는 알람 설정이 적용되지 않으며 인스턴스 별로 알람 설정을 적용한다.  
> 인스턴스 클릭 후 로그 및 이벤트 탭에서 CloudWatch 경보에서 경보 생성 버튼을 클릭하여 경보를 생성한다.

### 스냅샷
> 특정 시점의 DB 상태 그대로를 스냅샷으로 저장할 수 있음.
> 하나의 DB 인스턴스만 사용하는 경우 몇 초에서 몇 분 동안 DB I/O 가 지연될 수 있음.

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

---

## DB instance
### 설명
> 격리된 데이터베이스 환경. RDS 에서 가장 기반이 되는 블록이 DB instance.

### DB instance class
> DB instance 가 사용할 컴퓨팅 스펙. ex) db.t3.medium, db.r5.large, 등등 

### DB instance storage
> DB instance 에서 사용할 storage. General Purpose(SSD), Provisioned IOPS(PIOPS), Magnetic 이 있음.

### 인스턴스 scale-up
> 

### 인스턴스 추가
>

---

## 마이너 업데이트
### 
>

---

## 백업/복원
### 
>

---

## Failover
### Endpoint 의 변화
>

---

## CloudWatch
### 
>

---

## Web Application - SpringBoot
### Write 와 Read 인스턴스 함께 사용하기
> 
