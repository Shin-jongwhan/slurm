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
### slurm 을 설치하면 munge 라는 프로그램도 같이 설치가 된다.
### munge
#### 서버 클러스터링 상에서, master node (slurmctld) 가 있고, 작업을 수행하는 compute node (slurmd) 가 있다.
#### 그리고 프로세스 (또는 작업)를 관리하고 작업 정보에 대해 저장하는 slurmdbd 가 있다. 
#### 작은 cluster 상에서는 mysql 과 소통할 수 있다. 
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
### srun 은 slurmstepd 에 할당된 port range 와 소통한다. random 으로 작업 제출에 사용될 임시 포트 (ephemeral port) 가 할당된다. 
### 이는 SrunPortRange 으로 port range 를 정의할 수 있고, firewall 설정이 이루어져야 한다.
#### ![image](https://github.com/Shin-jongwhan/slurm/assets/62974484/598b6828-c2d2-4c7e-be00-94acc46217ad)



--------------------------------------------------------------------------


## 설치 방법
### slurm 설치
```
sudo apt install slurm-wlm
```
