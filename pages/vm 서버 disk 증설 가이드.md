- # 1. disk 증설할 vm → Storage → Add 선택
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/f805cbd3-9d91-4a99-8948-58ff76855600/image.png)
-
- # 2. 추가할 disk 설정
	- size 추가
	- disk 를 생성할 위치 선택
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/36cdba6b-ee60-4951-84b7-60d6093e9931/image.png)
-
- # 3. 추가된 disk 확인
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/068fd123-ef8f-423e-b4cf-40904fbadb9d/image.png)
-
- # 4. vm 재시작
-
- # 5. 앞서 추가한 디스크 파티션 확인
	- ```bash
	  lsblk
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/e60e4e7e-1734-41b7-8a67-570dee4c6b9f/image.png)
-
- # 6. 새 디스크를 PV로 생성
	- ```bash
	  sudo pvcreate /dev/xvdb
	  
	  sudo pvs
	  # /dev/xvdb 가 보여야 함
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/7db79648-a60d-461c-8ccb-fa15a1ee7b65/image.png)
-
- # 7. vg 에 추가
	- ```bash
	  sudo vgextend rl /dev/xvdb
	  
	  sudo vgs
	  # vFree 가 증가해야함
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/28a627cd-9c47-43d5-afde-039fbc7cabb1/image.png)
-
- # 8. /home LV 확장
	- ```bash
	  sudo lvextend -l +100%FREE /dev/rl/home
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/6f6c299e-5fd0-4b99-8ddd-db47e022ae83/image.png)
-
- # 9. 파일시스템 확장
	- ```bash
	  df -Th /home
	  
	  # xfs 인 경우
	  sudo xfs_growfs /home
	  
	  # ext4 인 경우
	  sudo resize2fs /dev/rl/home
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/7e2f8742-7b64-4922-bc7b-0b35c9084915/image.png)
-
- # 10. 확인
	- ```bash
	  df -h /home
	  ```
	- ![](https://t25540965.p.clickup-attachments.com/t25540965/18af8d07-f714-4807-b2de-b6cb18cfd036/image.png)
