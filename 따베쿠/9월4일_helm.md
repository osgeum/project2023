Helm 소개와 설치
쿠버네티스 패키지 관리자
Helm의 기능
- 새로운 차트 생성
- chart로 chart archive(tgz) files로 패키지화 하기
- chart가 저장되는 chart 저장소와 상화작용
- Kubernetes cluster에 chart의 설치 및 제거, 릴리즈 주기 관리

https://helm.sh/

https://bitnami.com/stacks/helm

스크립트 기반으로 설치
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

# helm version
# helm help

# source < (helm completion bash)
# echo "source <(helm completion bash)" >> ~/.bashrc

helm char 리파짓 등록

Helm의 구성
Char: helm 패키지, k8s application, tool, service를 구동하는데 필요한 resource의 집합(mariadb, nginx, redis등)

Repository: Helm char를 모아두고 공유하는 저장소
- bitnami(https://bitnami.com/stacks)
- https://bitnami.com/stacks/helm
- Bitnami는 가상 어플라이언스 및 웹 애플리케이션, 개발 스택용 소프트웨어 패키지 및 설치 라이브러리

Repository 추가

Help repo [add|remove|list] [NAME] [URL]
# helm repo add bitnami https://charts.bitnami.com/bitnami
# helm repo list
# helm repo remove bitnami

    
Helm 명령어 사용과 패키지 구조 보기           현재 폴더에 차트 아카이브 생성

helm search                         Helm Hub 및 Helm Repositories에서 차트 검색
helm install                        Kubernetes에 차트 설치
helm list                           주어진 Kubernetes 네임 스페이스의 모든 릴리즈 나열
helm show                           차트에 대한 정보 출력
helm upgrade                        릴리즈를 기본 차트의 새 버전으로 업그레이드
helm pull                           Helm 허브 또는 Helm 저장소에서 차트 다운로드 및 추출
helm repohelm package-tgz           차트 저장소 추가, 업데이트, 색인 생성, 나열 또는 제거    

# helm search repo nginx
# helm show chart bitnami/nginx
# helm inspect values bitnami/nginx

# helm install webserver bitnami/nginx
# helm install webserver --set service.type=NodePort bitnami/nginx
# helm list
# kubectl get all
# helm uninstall webserver

# helm inspect values bitnami/nginx > nginx_values.yaml

# helm install webserver -f nginx_values.yaml bitnami/nginx

#helm uninstall webserver

helm 패키지 구조 보기
# helm pull bitnami/nginx
# tar -zxvf nginx-15.2.0.tgz

Helm chart 만들기

Chart: Helm에서 사용하는 패키지 포맷
helm pull [chart] : Helm Chart를 로컬에 다운로드
helm create [chart] : 새로운 Helm Chart를 생성 chart 구조

# helm create myapp
# tree myapp

Chart.yaml         chart 정보를 정의 - helm show chart bitnami/nginx 실행시 보여주는 정보
charts 디렉토리       dependency chart파일들을 저장하는 디렉토리
templates 디렉토리    kubernets 리소스 템플릿이 보관되는 디렉토리
  NOTES.txt        chart를 설치 후 출력할 메시지 정의
  *.yaml           쿠버네티스 클러스터에 실행할 리로스 템플릿이
values.yaml        kubernetes templates file에 적용하는 변수 정의

Chart 만들기 : mynginx
# mkdir manifests
# kubectl create deployment webserver --image=nginx:1.25.2 --port 80 -o yaml > manifests/deployment.yaml
# kubectl expose deployment webserver --port 80 --target-port 80 --type NodePort -o yaml > manifests/service.yaml

cat manifests/deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      labels:
        app: webserver
    spec:
      containers:
      - image: nginx:1.25.2
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
		
cat service.yaml

apiVersion: v1
kind: Service
metadata:
  name: webserver
  namespace: default
spec:
  ports:
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: webserver
  sessionAffinity: None
  type: NodePort
  
  
# helm lint mynginx/
문법 검사

# helm install webserver ./mynginx/

# helm upgrade --set image.repository=httpd --set image.tag=2.2.34-alpine webserver mynginx
리파짓, 이미지 변경
		
# helm rollback webserver 1

helm chart 파일 만들기

# helm package mynginx
mynginx-0.1.0.tgz 생성

