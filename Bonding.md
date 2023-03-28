### Bonding 구성

1. Network 카드 두 개 구성
   - 가상화 vm에서 네트워크 카드 두 개 생성 

2. 네트워크 설정 파일 변경
   - cat /etc/sysconfig/network-scripts 로 이동
   - vi ifcfg-ens33<br>
     TYPE=Ethernet<br>
     BOOTPROTO=none<br>
     NAME=ens32<br>
     DEVICE=ens32<br>
     ONBOOT=yes<br>
     MASTER=bond0<br>
     SLAVE=yes<br>
   
   - vi ifcfg-ens33<br>
     TYPE=Ethernet<br>
     BOOTPROTO=none<br>
     NAME=ens33<br>
     DEVICE=ens33<br>
     ONBOOT=yes<br>
     MASTER=bond0<br>
     SLAVE=yes<br>
     
3. bond NIC 추가
   - vi ifcfg-bond0
     BOOTPROTO=none<br>
     NAME=bond0<br>
     DEVICE=bond0<br>
     ONBOOT=yes<br>
     IP ADDR=0.0.0.0<br>
     NETMASK=255.255.255.0<br>
     GATEWAY=0.0.0.0<br>
     BONDING_OPTS="mode=1 miimon=100"
     
     * mode=0 (round-robin) : 순차적 부하 분산
     * mode=1 (active-backup) : 사용중인 인터페이스 장애 발생 시 다른 인터페이스 활성화

4. 본딩 Bond0 커널에 적용
   -  vi /etc/modprobe.d/bonding.conf
      alias bond0 bonding
      options bond0 mode=1 miimon=100
       
5. 본딩 모듈 사용
   -  modprobe bond0


6. 설정 파일 적용 후 서비스 재 시작
   centos 6버전 : service network restart
   centos 7버전 : systemctl restart network
   centos 8버전 : os 재 시작해야 적용됨    

7. Bonding 적용 상태 확인  
   - watch -d -n 1 "cat/proc/net/bonding/bond0"
     * watch 명령어 세부사항 확인

7-1. 본딩 구성 확인

<br>Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)<br>
<br>Bonding Mode: fault-tolerance (active-backup)
<br>Primary Slave: None
<br>urrently Active Slave: ens33
<br>MII Status: up
<br>MII Polling Interval (ms): 100
<br>Up Delay (ms): 0
<br>Down Delay (ms): 0
<br>Peer Notification Delay (ms): 0<br>
<br>Slave Interface: ens33
<br>MII Status: up
<br>Speed: 1000 Mbps 
<br>Duplex: full
<br>Link Failure Count: 0
<br>Permanent HW addr: 00:0c:29:fb:9f:74
<br>Slave queue ID: 0<br>
<br>Slave Interface: ens36
<br>MII Status: up
<br>Speed: 1000 Mbps
<br>Duplex: full
<br>Link Failure Count: 0
<br>Permanent HW addr: 00:0c:29:fb:9f:7e
<br>Slave queue ID: 0 

---------------------------------------------------------------------------------------------------------------------------------------------------------------------  

7-2. 장애상황 발생 / 네트워크 카드 하나 삭제 -> ens36이 올라온 것 확인가능
                 
<br>thernet Channel Bonding Driver: v3.7.1 (April 27, 2011)<br>
<br>Bonding Mode: fault-tolerance (active-backup)
<br>Primary Slave: None
<br>Currently Active Slave: ens36**
<br>MII Status: up
<br>MII Polling Interval (ms): 100
<br>Up Delay (ms): 0
<br>Down Delay (ms): 0
<br>Peer Notification Delay (ms): 0<br>
<br>Slave Interface: ens36
<br>MII Status: up
<br>Speed: 1000 Mbps
<br>Duplex: full
<br>Link Failure Count: 0
<br>Permanent HW addr: 00:0c:29:fb:9f:7e
<br>Slave queue ID: 0<br>
