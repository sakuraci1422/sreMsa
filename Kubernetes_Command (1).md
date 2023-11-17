# KuKubernetes Command
```
kubectl       # k 
```
## get 
```
k get --help
k get all
k cluster-info
k get --help
k get no
k get ep
k get ds
k get deploy
k get svc
k get all

```

## create
```
k create  --help
    ~~
k create deployment --help
kubectl create deployment nginx --image=nginx
```
## exec
```
k get po  # pod이름 알아내고
k exec nginx-알아낸pod명 -- ls      # 명령만 전달
k exec -it nginx-알아낸pod명 -- bash      # 콘솔에 연결 가능.
```

## expose (service)
```
kubectl expose --help 
#kubectl expose deployment nginx --port=80  #외부에서 접근 X
kubectl expose deploy nginx --type="NodePort" --port=80
k get svc
k describe svc/nginx     # node port를 알아냄
curl 10.233.115.195      # end port로 접근한(외부접근 X)
curl vm01:30634          # node port로 접근한(외부접근 O)
```

## scale (replicaset)
```
k get rs
kubectl scale deployment nginx --replicas=4
k get rs
k get po
kubectl scale deployment nginx --replicas=100
k get po -o wide
k get po -o wide |grep vm01|wc -l
k get po -o wide |grep vm02|wc -l
k get po -o wide |grep vm03|wc -l
k describe rs/nginx-7854ff8877
kubectl scale deployment nginx --replicas=2
# rs가 load banlancing을 한다! 어떻게?
## node port를 알아내고,
k describe svc/nginx|grep NodePort
## curl명령으로 결과 확인.
curl vm01:30634|head -n 2
## 한개의 pod에 들어가서 /usr/share/nginx/html/index.html을 수정하면? 결과 확인 가능!
k get po
k exec -it nginx-7854ff8877-ht7cc -- bash
    echo "hi" > /usr/share/nginx/html/index.html
    exit
curl vm01:30634|head -n 2
curl vm01:30634|head -n 2
curl vm01:30634|head -n 2
curl vm01:30634|head -n 2
```

## -o -f 
```
k get deploy/nginx -o yaml   # 저장 -→ git --→ argoCD배포
k get deploy/nginx -o json   # jsonpath구문을 통해서 검색 (HELM에서 필수)
k get deploy/nginx -o json |jq '.status.replicas'
```
* svc/nginx의 NodePort를 np라는 환경 변수에 저장하여 curl로 결과를 확인하세요.
```
k get svc/nginx -o json|jq '.spec.ports[0].nodePort'
np=$(k get svc/nginx -o json|jq '.spec.ports[0].nodePort')
curl vm01:$np
```


* json query
```
apt install -y jq
echo '{"apple":1,"pear":[1,2,3,4]}' |jq '.pear[2]'
```


* 객체 파일로 저장하고 저장한 파일에서 객체 생성
```
k get svc/nginx -o yaml > service_nginx.yaml
k delete svc/nginx
k get svc
k create -f service_nginx.yaml
k get svc
```

## label
```
k get all --show-labels 
k get deploy --show-labels
k label deploy/nginx test=yes #레이블 지정
k label deploy/nginx test=yes #또 지정하면 error비슷하게 나왔지만 변경되지 않았다는 뜻
k label deploy/nginx test-    #레이블 제거
```
## autoscale
```
k get rs
kubectl create deployment nginx --image=nginx
kubectl expose deploy nginx --type="NodePort" --port=80
k autoscale rs nginx-7854ff8877 --max=10 --cpu-percent=80
k describe hpa/nginx-7854ff8877
kubectl scale deployment nginx --replicas=3
        # to Test
k describe hpa/nginx-7854ff8877
```
### kubernetes HPA문제 해결
####  1. metric sever api 문제 해결
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
# "k top no" 명령으로 metric서버 작동여부 확인
# "error: Metrics API not available"라는 메세지 뜨면 metric pod를 describe해 봄.
#            k describe po/metrics-server-fbb469ccc-dthqd -n kube-system
#        Readiness probe failed: HTTP probe failed with statuscode: 500 에러 발견시
         k edit -n kube-system deployments.apps/metrics-server
            # "- -metric-resolution=15s"줄 밑에 "- --kubelet-insecure-tls"추가 
k get po -n kube-system |grep metric
k top no
```

####  2. AutoScale Example ( source : https://younsl.github.io/blog/k8s/hpa-metrics-unknown/ )
```
kubectl config set-context --current --namespace=default
kubectl delete all --all
k get all

kubectl create deploy --image=nginx nginx
kubectl patch deploy/nginx \
    -p '{
  "spec": {
    "template": {
      "spec": {
        "containers": [{
          "name": "nginx",
          "resources": {
            "requests": {
              "cpu": "250m"
            },
            "limits": {
              "cpu": "1000m"
            }
          }
        }]
      }
    }
  }
}'
kubectl scale deployment nginx --replicas=2
kubectl expose deploy nginx --type="NodePort" --port 80
sleep 30
rsName=$(kubectl get rs -ojsonpath='{.items[?(@.spec.replicas!=0)].metadata.name}')
kubectl autoscale rs/$rsName --min=1 --max=5 --cpu-percent=80
sleep 30
k describe hpa/$rsName| grep -A 5 "^Conditions"

```

####  3. Stress
```
np=$(k get svc/nginx -o json|jq '.spec.ports[0].nodePort')

for i in $(seq 100000); do
    curl 127.0.0.1:$np 2>> /dev/null
done
```
* 잘 안되면 각 pod에 stress툴 설치해서 "stress -c 4" 명령 실행
```
k get po
k exec -it nginx-6dcd9fddb-h49kv -- bash
    apt update -y&&apt install -y stress

```


####  4. check
```
k get po
```


## namespace (ns)
```
kubectl config set-context --current --namespace=kube-system
kubectl config set-context --current --namespace kube-system
echo "alias kun='kubectl config set-context --current --namespace '">>~/.bashrc
echo "alias kunc='kubectl config view |grep namespace:'">>~/.bashrc
. ~/.bashrc
kun default
kunc
kun kube-system
kunc

k get all --all-namespaces
k get po  --all-namespaces
k get svc --all-namespaces
k get svc -n default
k get svc -n kube-system

k create ns prj1
kun prj1
kubectl create deployment nginx --image=nginx
k get deploy -n default
k get deploy -n prj1
k get deploy --all-namespaces|wc -l
k delete ns prj1
k get deploy --all-namespaces|wc -l #줄어듦
```


## Kubernetes Dashboard (참고용)
```
kubectl config set-context --current --namespace=kube-system
kubectl proxy &
# curl 127.0.0.1:8001
# curl 127.0.0.1:8001/api/v1
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.4.0/aio/deploy/recommended.yaml

kubectl get svc -n kubernetes-dashboard

kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
  # type: ClusterIP    ==> NodePort로 변경

# firefox, ie, edge, chrome(셋팅필요) 인증서 없는  https:// 접근가능 브라우저. (public ip로 접근)

#kubeadm token list
#kubeadm token delete $(kubeadm token list |awk '{print $1}'|tail -n 1) # 기존 토큰 지우기(kubeadm token list 쳤을때 안나올때 까지)
kubeadm token list| awk 'NR>1{print $1}'|xargs -i{} kubeadm token delete {}

kubeadm token list
kubeadm init phase upload-certs --upload-certs
kubeadm token list
kubectl get secrets

SECRET_NAME=$(kubectl get secrets|grep bootstrap|awk '{print $1}')

kubectl delete -f admin-sa.yml
cat <<EOF> admin-sa.yml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
secrets:
- name: $SECRET_NAME

EOF
kubectl apply -f admin-sa.yml
kubectl get sa admin-user

kubectl -n kube-system create token admin-user
# 현재는 Login만 가능한 것이고 권한이 없음...

kubectl delete -f admin-rbac.yml
cat <<EOF> admin-rbac.yml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kube-system
EOF
kubectl apply -f admin-rbac.yml

kubectl get clusterrolebinding.rbac.authorization.k8s.io/admin-user

kubectl get svc -n kubernetes-dashboard
#NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
#dashboard-metrics-scraper   ClusterIP   10.233.49.245   <none>        8000/TCP        28m
#kubernetes-dashboard        NodePort    10.233.38.28    <none>        443:31086/TCP   28m
#                                                                            ↑
#                                                                          nodeport

# 로컬 브라우저에서 https://public-ip:31086
kubectl -n kube-system create token admin-user
# 결과값이 토큰 값 ==> 비번 처럼 사용

kubectl config set-context --current --namespace=default
```

for i in $(ls|grep y|grep ml); do
    kubectl delete -f $i
done
