# Usage.
```
git config --global core.autocrlf false
git config --global core.eol lf
cd
git clone https://github.com/Finfra/sreMsa
cd sreMsa
```

## 주의) 202107이전에 수업들었던 분들은 아래 방식으로 사용하세요.
```
cd
git clone https://github.com/Finfra/sreMsa
cd sreMsa
git checkout 202107
```

## 주의) 202107~202207에 수업들었던 분들은 아래 방식으로 사용하세요.
```
cd
git clone https://github.com/Finfra/sreMsa
cd sreMsa
git checkout 202207
```


# [Lab1. 사전 실습 환경 세팅](./Lab1.Kubespray/)
## Lab1-1~3. Terraform으로 EC2 인스턴스 생성
### Lab1-1. 실습용 Terraform Instance 구성 
### Lab1-2. IAM Key생성 
### Lab1-3. Terraform으로 K8s용으로 사용할 Instance 생성
## Lab1-4~5. Ansible+Terraform Provisiong(Kubespray)
### Lab1-4. Kubepray를 통한 K8s 구성 
### Lab1-5. K8s 작동 테스트

# [Lab2. Kubernetes](./Lab2.Kubernetes/)
* Kubernetes 기본 기능 실습
## Lab2-1. 개념이해를 위한 쿠버네티스 학습 시뮬레이터 사용 실습
## Lab2-2. 노드 관리 실습
## Lab2-3. Kubernetes Deplyment
## Lab2-4. Kubernetes 각종 레이블 관리와 모니터링
## Lab2-5. Kubernetes 서비스, 로드발렌싱, AutoScaling
## Lab2-6. Kubernetes 네트워킹
## Lab2-7. Kubernetes 스토리지
## Lab2-8. Container Deploy(CI/CD에서 사용할 Java 동작 OS) 실습

# [Lab3. Istio](./Lab3.Istio/)
* Istio를 통한 Service Mesh 구현 실습
## Lab3-1. Istio 셋팅 실습
## Lab3-2. Istio Traffic 관리 실습
## Lab3-3. 인증, 권한 부여 및 서비스 통신 암호화를 관리 실습
## Lab3-4. Istio를 통한 K8s 클러스터 접근 실습(외부에서 내부로 접근)

# [Lab4. Argo CD](./Lab4.ArgoCd/)
* Argo CD를 통한 GitOps 구현 실습
## Lab4-1. Argo CD install
## Lab4-1. Argo CD를 통한 Canary배포 실습

# [Lab5. zipkin](./Lab5.Zipkin/)
* ZipKin을 통한 MSA Monitoring 실습
## Lab5-1. zipkin/jaeger 셋팅 실습
## Lab5-2. Zipkin을 이용한 MSA 환경에서 분산 트렌젝션의 추적
## Lab5-3. Transaction UI(API / Web) 연동 실습


## 기타

* alias egrep='egrep --color=auto'
* alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'



## k alias만들고 자동완성 기능 되게 하기

* echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
. ~/.bashrc


## Short-names 
| Short name    |Full name                  |
|---------------|---------------------------|
| po            |pods                       |
| rs            |replicasets                |
| svc           |services                   |
| ns            |namespaces                 |
| no            |nodes                      |
| ep            |endpoints                  |
| ds            |daemonsets                 |
| deploy        |deployments                |
| -             | -                         |
| cm            |configmaps                 |
| cs            |componentstatuses          |
| csr           |certificatesigningrequests |
| ev            |events                     |
| hpa           |horizontalpodautoscalers   |
| ing           |ingresses                  |
| limits        |limitranges                |
| pdb           |poddisruptionbudgets       |
| psp           |podsecuritypolicies        |
| pv            |persistentvolumes          |
| pvc           |persistentvolumeclaims     |
| quota         |resourcequotas             |
| rc            |replicationcontrollers     |
| sa            |serviceaccounts            |


## tmux
* 세로운 세션 생성 : tmux ⏎ 
* 세로운 세션 생성(이름지정) : tmux new -s 이름⏎
* 세션 리스트 : tmux ls⏎
* 세션에서 나오기 : ctl+b,d
* 세션 접속 : tmux attach -t 이름

## 강의 주소
https://github.com/Finfra

* 강의 파일 구글 드라이브	https://drive.google.com/drive/folders/1Mnxg2dLnyCBaY_ZwnTUuUvjotBKaGS1B
* 강의 노트 ( 현재 파일 )	bit.ly/202311_msa
* 강의 수강후 모임	https://www.facebook.com/groups/namjunggu
* 강의 소스	 https://github.com/Finfra/sreMsa

## 참소 싸이트 

* json <=> yaml 변환	https://www.json2yaml.com
* Json Path	https://jsonpath.com


