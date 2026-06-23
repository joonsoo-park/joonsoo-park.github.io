- # 설치
	- 예시 `docker-compose.yaml`
	- ```yaml
	  services:
	    elasticsearch:
	      image: docker.elastic.co/elasticsearch/elasticsearch:8.0.1
	      container_name: elasticsearch
	      environment:
	        - discovery.type=single-node
	        - xpack.security.enabled=false
	        - xpack.security.http.ssl.enabled=false
	        - xpack.security.transport.ssl.enabled=false
	        - ES_JAVA_OPTS=-Xms1g -Xmx1g
	      ulimits:
	        memlock:
	          soft: -1
	          hard: -1
	      volumes:
	        - ./esdata:/usr/share/elasticsearch/data
	      ports:
	        - "9201:9200"
	      networks:
	        - exemone_network
	
	    kibana:
	      image: docker.elastic.co/kibana/kibana:8.0.1
	      container_name: kibana
	      environment:
	        - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
	        - SERVER_NAME=kibana
	        - SERVER_HOST=0.0.0.0
	      ports:
	        - "5601:5601"
	      depends_on:
	        - elasticsearch
	      networks:
	        - exemone_network
	
	  networks:
	    exemone_network:
	      external: true
	  ```
-
- # 모니터링
	- chromium 기반 웹에서 `elasticvue` 설치 후 사용
-
- # 데모3 서버, 안정화 서버 공용 사용
	- `10.10.43.72:9201`
	- `10.10.43.72:5601` kibana
-
- # 데이터 저장 안 될 경우 조치
	- ingester 설정 확인
	- API 설정 확인
		- 박희원 대리(진)
	- elasticsearch watermark 확인
		- https://www.elastic.co/docs/troubleshoot/elasticsearch/fix-watermark-errors
		- elasticsearch 가 설치된 서버의 디스크 용량이 `90%` 이상인 경우 데이터 저장이 막힘
		- 디스크 용량 정리 시 정상 작동하도록 변경 됨
-
- # 인덱스 관리 (web ui 기준)
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/4ce84e70-20bb-4ae3-8252-62e7f31f07db/image.png)
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/94ac0677-3007-445f-b605-b05030f3bf5f/image.png)
