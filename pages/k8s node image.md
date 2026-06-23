# 테스크
- https://app.clickup.com/t/25540965/DSP-30973
-
- # 피그마
- https://www.figma.com/design/Usv0ZHXBU4GLIT0g7T3Fdi/06.-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4?node-id=10001-14214&t=1M5mDXJCk5oxs74r-4
-
- # 작업 현황
- DONE Agent 완료
- DONE 수집단 완료 (receiver, core, ingester, clickhouse)
- WAITING API 작업 진행 필요
-
- # 담당자
- Agent
	- 박세훈
- API
	- 나지수
-
- # 브랜치
- develop 에 머지되어있음
-
- # 추가 테이블
- infra_k8s_node_image
- ```sql
  CREATE TABLE default.infra_k8s_node_image
  (
      `host_id` String COMMENT 'target id',
      `collect_time` DateTime64(3, 'UTC') COMMENT '에이전트 기준 수집 시간',
      `cluster_id` String COMMENT 'cluster id',
      `cluster_name` String COMMENT 'cluster 이름',
      `node_uid` String COMMENT 'node UID',
      `node_name` String COMMENT 'node 이름',
      `container_runtime_type` LowCardinality(String) COMMENT '컨테이너 런타임 타입 (CONTAINER_TYPE_DOCKER, CONTAINER_TYPE_CONTAINERD, CONTAINER_TYPE_CRIO)',
      `image_id` String COMMENT '이미지 ID (config digest, sha256:...)',
      `repo_tags` Array(String) COMMENT '이미지 repo:tag 목록',
      `repo_digests` Array(String) COMMENT '이미지 repo digest 목록',
      `size` Int64 COMMENT '이미지 크기 (byte)',
      `created_time` DateTime64(3, 'UTC') COMMENT '이미지 빌드 시간 (확인 불가 시 epoch)'
  )
  ENGINE = ReplacingMergeTree(collect_time)
  PARTITION BY toYYYYMMDD(collect_time)
  ORDER BY (node_uid, image_id)
  SETTINGS index_granularity = 8192
  ```
-
- # 저장 데이터 설명
- image_id
	- 이미지 고유 해시 값
	- 런타임 내부에서 이미지 자체 식별하는 값
- repo_digests
	- 레지스트리에서 해당 이미지 가리키는 이름 + digest
	- ex) `docker.io/maxgauge/exem-container-agent@sha:1235132` 이거는 "docker.io/maxgauge/exem-container-agent" 라는 특정 이미지의 digest 값.
- 이미지 이름은 `repo_digests` 에서 @ 기준 앞에 있는 값 사용 가능