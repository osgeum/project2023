Kubernetes Ingress

HTTP나 HTTPS를 통해 클러스터 내부의 서비스를 외부로 노출
기능
Service에 외부 URL을 제공
트래픽을 로드밸런싱
SSL 인증서 처리
Virtual hosting을 지정

Ingress Controller 설치

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/baremetal/deploy.yaml

Ingress를 이용한 웹서비스 운영

Kubernetes Ingress 동작


