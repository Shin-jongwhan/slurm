### 231204
## server clustering - 작업 관리 프로그램
### <br/>

### 참고 링크
- [slurm 공식 홈페이지](https://slurm.schedmd.com/network.html)
- [Ubuntu 20.4 에 SLURM 스케줄러 설치하기](http://luxnox.iptime.org/blog/ubuntu-20-4-%EC%97%90-slurm-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/)
- [\[Linux\] Slurm 사용법](http://shumin.co.kr/linux-slurm-%EC%82%AC%EC%9A%A9%EB%B2%95/)
- [\[Linux\] Slurm 스케줄러 활용법](https://doheejin.github.io/linux/2021/02/18/linux-slurm.html)
### <br/><br/><br/>

## 설명
#### 서버 클러스터링 상에서, master node (slurmctld) 가 있고, 작업을 수행하는 compute node (slurmd) 가 있다.
#### 그리고 프로세스 (또는 작업)를 관리하고 작업 정보에 대해 저장하는 slurmdbd 가 있다. 
#### 작은 cluster 상에서는 mysql 과 소통할 수 있다. 
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/598b6828-c2d2-4c7e-be00-94acc46217ad)
### <br/>

### slurmctld 의 default port 는 6817 이다. 이는 slurm.conf 에서 바꿀 수 있다.
### slurmdbd 의 default port 는 6819 이다.
### slurmd 의 default port 는 6818 이다.
### <br/>

### default 로 IPv4 로 listen 한다. 
### IPv6 로 변경할 수도 있다. 이 경우에는 IPv4 를 desable 해야 한다.
### <br/>

### default 로 mysql 의 port 3306 과 communication 한다.
### <br/>

### `srun`
### job 을 running 하는 명령어
### srun 은 slurmstepd 에 할당된 port range 와 소통한다. random 으로 작업 제출에 사용될 임시 포트 (ephemeral port) 가 할당된다. 
### 이는 SrunPortRange 으로 port range 를 정의할 수 있고, firewall 설정이 이루어져야 한다.
### <br/>

### 전반적인 과정은 srun - slurmctld, srun - slurmd - slurmstepd 와 소통하는 과정이다.
### srun 이 실행되면 slurmctld 에게 job 제출되었다고 알려주고, 인증한다.
### 그리고 job 에 I/O 를 할당하기 위해 socket 을 열고, 거기에 각 job 을 할당한다. 여기에서 slurmstepd 와 소통하여 job 을 실행할 수 있도록 제출한다.
### slurmstepd 는 task 를 실행한다.
### 그리고 다시 위로, slurmstepd -> slrumd -> srun -> slurmctld 에게 job 에 대한 termination 을 알려준다.
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/10456f68-a679-404b-bf0f-86f7b1bf5a9f)
### <br/>

### slurmctld, slurmdbd 는 여러 개 구성할 수 있고 (multi cluster), 각각의 slurmctld, slurmdbd 는 각각 할당된 올바른 destination 으로 가야 한다. 
### 그리고 slurmdbd 는 mysql server 와 소통해야 한다.
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/716f1ade-9ec1-45f4-8502-bc0378a1394e)
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/b9f69cad-0771-4c13-860f-97f5dfdc3d52)
### <br/>

### `federation`
### 다음과 같이 multiple slurmctld 를 구성하고 연결할 수도 있다. 이를 federation 이라고 한다.
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/513b5ac3-5559-491f-b452-eeb534e7c814)
### <br/>

### `munge`
### slurm 을 설치하면 munge 라는 프로그램도 같이 설치가 된다.
### munge 는 authentication plugin 이다.
### Authentication plugins identifies the user originating a message.
### 어느 한 계정에서 slurm 을 실행한다고 하면 그 계정에서 실행된 고유의 uid 값을 가질 것이고, 다른 계정에서는 그 uid 값이 달라진다. 그 고유번호가 달라지면 권한에 문제가 발생한다.
### 그래서 munge 에게 실행 권한을 주고 같은 uid 값을 할당하게 한다.
### <br/>

### `database`
### 다음의 database 가 사용가능하다.
- MySQL
- MariaDB
### <br/>

### `MPI`
### [MPI (message passing interface)](https://github.com/Shin-jongwhan/TIL/tree/main/server_clustering/MPI_message_passing_interface)
### 병렬 처리를 위한 통신 규약으로, 노드 간 정보 교환을 해주는 매개체이다.

### <br/><br/><br/>

--------------------------------------------------------------------------


## 설치 방법
### 다음의 사이트를 참고하였다.
- http://luxnox.iptime.org/blog/ubuntu-20-4-%EC%97%90-slurm-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/
- https://ai4nlp.tistory.com/25
### <br/>

### slurm 설치
```
# slurm 설치
sudo apt install slurm-wlm

# slurm 문서 패키지 설치
sudo apt install slurm-wlm-doc
```
### <br/>

### 만약 munge 에 대해 user, group add 가 안 되어 있으면 다음을 따라한다.
```
export MUNGEUSER=991
groupadd -g $MUNGEUSER munge
useradd  -m -c "MUNGE Uid 'N' Gid Emporium" -d /var/lib/munge -u $MUNGEUSER -g munge  -s /sbin/nologin munge
```
### <br/>

### /etc/slurm-llnl/slurm.conf 설정
### slurm.conf 를 만드는 데에 도움을 주는 웹사이트가 있으니 참고
- slurm.schedmd.com/configurator.html
```
#
# slurm.conf for ubuntu 20.04
#
ClusterName=cluster1
#SlurmctldHost=182.162.88.158
ControlMachine=bidev2
ControlAddr=182.162.88.158
#
MpiDefault=none
AuthType=auth/munge
ProctrackType=proctrack/linuxproc
ReturnToService=1
SlurmctldPidFile=/var/log/slurm-llnl/slurmctld.pid
#SlurmctldPort=6817
SlurmdPidFile=/run/slurm-llnl/slurmd.pid
#SlurmdPort=6818
SlurmdSpoolDir=/var/lib/slurm-llnl/slurmd
SlurmUser=slurm
StateSaveLocation=/var/spool/slurm-llnl
TaskPlugin=task/none
# SCHEDULING
SchedulerType=sched/backfill
SelectType=select/cons_res
SelectTypeParameters=CR_Core_Memory
# LOGGING AND ACCOUNTING
AccountingStorageType=accounting_storage/none
JobCompType=jobcomp/none
JobAcctGatherType=jobacct_gather/linux
SlurmctldLogFile=/var/log/slurm-llnl/slurmctld.log
SlurmdLogFile=/var/log/slurm-llnl/slurmd.log
#
DebugFlags=NO_CONF_HASH
#
# COMPUTE NODES
NodeName=bidev2 NodeAddr=182.162.88.158 CPUs=8 Boards=1 SocketsPerBoard=1 CoresPerSocket=8 ThreadsPerCore=1 RealMemory=16000
PartitionName=batch Nodes=bidev2 Default=YES MaxTime=INFINITE State=UP
```
### <br/>

### 마스터 노드와 컴퓨터 노드 설정
#### * 원래는 둘 다 설정해야 하는데, 지금은 서버 하나로만 테스트하기 때문에 마스터 노드만 설정
```
dd if=/dev/urandom of=/etc/munge/munge.key bs=1c count=4M
ls -l /etc/munge/munge.key
chmod a-r /etc/munge/munge.key
chmod u-w /etc/munge/munge.key $ chmod u+r /etc/munge/munge.key
chown munge:munge /etc/munge/munge.key
munge –n
munge –n | unmunge
# 컴퓨터 노드에 munge.key 를 복사 (컴퓨터 노드가 있는 경우 실행)
# $ scp /etc/munge/munge.key node1:/etc/munge/
systemctl enable munge
/etc/init.d/munge restart
```
### 다음과 같이 ok 가 출력되어야 한다.
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/524d58d7-01fb-42a5-a1bf-d11fc948b486)
### <br/>

### munge daemon 실행 확인
```
systemctl status munge
```
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/fa8dfb28-80e1-4604-b26f-eb3a7d070b52)
### <br/>

### slurm master node 설정
```
# slurmctld
mkdir /var/spool/slurmctld
chown slurm: /var/spool/slurmctld
chmod 755 /var/spool/slurmctld
touch /var/log/slurmctld.log
chown slurm: /var/log/slurmctld.log
touch /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log
chown slurm: /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log
systemctl enable slurmctld
/etc/init.d/slurmctld restart

# slurm-llnl
mkdir /var/spool/slurm-llnl
chown slurm: /var/spool/slurm-llnl
chmod 755 /var/spool/slurm-llnl
```
### <br/>

### slurm 실행
```
slurmctld -Dcvvv
```

