


## 6.4 전문 검색 엔진

* MySQL 5.5에서는 MyISAM 스토리지 엔진에서 전문 검색 인덱스를 제공
* MyISAM 아키텍처 특성으로 인해 InnoDB 스토리지 엔진보다 안정적이지 못했고 트랜젝션이나 레코드 기반 잠금 등과 같이 최적화된 기능을 사용할 수 없기 때문에 호용성이 크지 않음
* 그래서 MySQL 5.6부터는 InnoDB 스토리지 엔진에서도 전문 검색 엔진을 지원

### 6.4.1 전문 검색 인덱스 추가

### 6.4.2 전문 검색 인덱스를 위한 테이블 스페이스

### 6.4.3 전문 검색 인덱스 관련 INFORMATION_SCHEMA 정보

### 6.4.4 전문 검색 인덱스 사용

### 6.4.5 주의 사항