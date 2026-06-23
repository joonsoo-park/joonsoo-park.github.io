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
- # 용어 설명
- #### MIG
	- 하나의 물리 GPU를 여러 개의 독립적인 GPU처럼 나누어 사용하는 것
-
- # 작업 모듈
- ## receiver
- ``internal/pkg/redis/cache/gpu_infra_state.go`
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
-