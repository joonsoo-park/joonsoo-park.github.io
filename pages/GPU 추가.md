# 테스크
- https://app.clickup.com/t/25540965/DSP-42350?block=block-l_7b9rEfD7
-
- # 작업 브랜치
- DSP-42350_gpu_monitoring
-
- # 작업 목적
- host GPU + container GPU
- 통합 테이블 이용한 데이터 관리
-
- # 진행 상황
- DONE 수집단 작업
- DONE API 작업
- DONE agent 작업
- WAITING 프론트 (6월 25일 확인 가능, 담당자 연차)
-
- # 담당자
- #### API
	- 김효정
	- 박희원
- #### Front
	- 배지호
- #### Agent
	- 박세훈
	- 김예진
-
- # 용어 설명
- #### MIG
	- 하나의 물리 GPU를 여러 개의 독립적인 GPU처럼 나누어 사용하는 것
-
- # 작업 모듈
- ## receiver
- `internal/pkg/redis/cache/gpu_infra_state.go`
	- SaveHostGpuInfraStates / SaveContainerGpuInfraStates
		- 메시지 내 GPU uuid·MIG uuid를 ``InfraState.<uuid>` 키로 저장
- ## core
- |Table|설명|
  |--|--|
  |infra_gpu_monitoring_info_v2|ReplacingMergeTree / (host_id, uuid)|
  |infra_gpu_monitoring_stat|MergeTree / (host_id, uuid, collect_time)|
  |infra_gpu_monitoring_process|MergeTree / (host_id, uuid, collect_time)|
  |infra_gpu_mig_monitoring_info|ReplacingMergeTree / (host_id, gpu_uuid, mig_uuid)|
  |infra_gpu_mig_monitoring_stat|MergeTree / (host_id, gpu_uuid, mig_uuid, collect_time)|
- host_id
	- host의 경우는 host_id
	- container의 경우는 node_id 를 host_id 로 사용
- `infra_gpu_monitoring_info` 테이블의 과거 host GPU 수집 목적으로 사용했던 테이블로 `deprecated`.
- `infra_gpu_monitoring_info` 테이블 DROP 은 할 수 없어 v2 생성
-
- ## ingester
- stat 데이터 68개 모두 `metric_dist`에  저장
	- data_id: infra_gpu_*, host/node 공통
	- 매퍼가 직접 만들던 알람 Kafka 메시지는 제거 — 알람은 AlertMetricProducer(metric_dist→활성 룰 data_id 필터→Kafka) 단일 경로로 일원화
- DCGM prof 8종(gr_engine/dram/sm/sm_occupancy/tensor/fp64/fp32/fp16 active)은 metric에서 /100하여 %(0~100) 로 기록. stat 테이블 칼럼은 raw(0~10000) 유지
- process 메트릭은 미생성 (process는 타깃이 아님 — 테이블 저장만)
- MIG도 동일하게 idle_since (mig_uuid 키, 같은 함수 재사용)
-
-