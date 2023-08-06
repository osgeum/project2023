학습내용

파트

Pod 개념 및 사용하기
컨테이너를 표현하는 K8S API의 최소 단위
Pod에는 하나 또는 여러 개의 컨테이너가 포함될 수 있음

파드 실행 명령
# kubectl run webserver --image=nginx:1.14

# kubectl create -f pod-nginx.yaml

# kubectl exec multipod -it -c centos-container -- /bin/bash

# kubectl logs multipod -c nginx-container

Pod 동작 Flow

스케쥴러에 의하여 작동 노드를 배정 받지 못하면 Pending 상태, 배정이후 Create -> Runngin or Suceeded Failed 

Pod 관리하기

동작중인 파드 정보 보기

# kubectl get pods

# kubectl get pods -o wide

# kubectl describe pod webserver

# kubectl get pods --all-namespaces

동작중인 파드 수정

# kubectl edit pod webserver

동작중인 파드 삭제

# kubectl delete pod webserver

# kubectl delete pod -all

Question & Answer

1. 현재 namespace에서 동작중인 Pod는 몇 개인가?
# kubectl get pods

2. 현재 시스템에서 동작중인 Pod 수는 ?
# kubectl get pods --all-namespaces

3. 컨테이너 nginx를 실행하는 nginx-pod라는 이름의 Pod 생성하시오.
# kubectl run nginx-pod --image=nginx:1.14

4. 앞에서 생성한 Pod의 image 정보를 확인하는 명령은 무엇인가?
# kubectl describe pods nginx-pod

5. 앞에서 생성한 nginx-pod는 어느 node에 배치되었나?
# kubectl get pods -o wide

6. 앞에서 생성한 Pod에는 몇 개의 컨테이너가 포함되어 있나?
# kubectl get pods -o wide

7. 앞에서 생성한 Pod의 현재 상태는 어떤한가?
# kubectl get pods -o wide 

8. 새 파드의 컨테이너 상태는 어떤습니까?
# kubectl describe pods nginx-pod

9. 'kubectl get pods'명령의 출력에서 READY 열은 무엇을 의미하나?
전체 파드수중 현재 동작중인 파드수

10. 생성한 pod를 삭제하시오.
# kubectl delete pods nginx-pod

11. 컨테이너 image 'redis123'을 실행하는 pod 'redis'를 redis.yaml을 이용해 생성하시오.
cat redis.yaml
apiVersion: v1
kind: Pod
metadata:
	name: redis
spec:
	containers:
	- name: redis
	- image: redis123
	
# kubectl create -f redis.yaml

# kubectl run redis --image=redis123 -o yaml --dry-run > redis.yaml
12. 앞서만든 redis pod의 image를 redis로 수정하여 동작시키시오.
# kubectl edit pods edit

image: redis123 image: redis 수정
 
LivenessProbe를 사용한 self-healing Pod
- 컨테이너가 정상적으로 작동이 되지 않을때 컨테이너를 리스타트, 건강한 컨테이너로 서비스 하도록 보장 하겠다.
  셀프힐링이 포함되어 있는 기능 LivenessProbe이다.
  컨테이너는 리스타트 되어도 IP는 파드가 가지고 있으므로 IP는 변경되지 않는다.
  
livenessProbe 매커니즘

- httpGet probe : 지정한 IP주소, port, path에 HTTP GET 요청을 보내, 해당 컨테이너가 응답하는지 확인한다. 반환코드가 200이 아닌 값이 나오면 오류, 컨테이너를 다시 시작한다.
  정상 응답이 없으면, 기존 컨테이너를 죽이고, 새로 컨테이너 이미지를 다운 받아서 기동.
  livenessProbe	
	httpGet:
		patch /
		port 80
		
- tcpSocket probe : 지정된 포트에 TCP 연결을 시도. 연결되지 않으면 컨테이너를 다시 시작한다.
  livenessProbe
	tcpSocket:
		prot:22

- exec probe : exec 명령을 전달하고 명령의 종료코드가 0이 아니면 컨테이너를 다시 시작합니다.
  livenessProbe:
	exec:
		command:
		- ls
		- /data/file



liveness probe 매개 변수
	periodSeconds: health check 반복 실행 시간(초)
	initialDelaySeconds: Pod 실행후 delay할 시간(초)
	timeoutSeconds: health check후 응답을 기다리는 시간(초)

기본값
Liveness:       http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
	                                 Pod가 실행후 바로 건강 체크를 하겠다 ( delay=0s )
									 건강진단후 1초후 답변이 없으면 재시작 하겠다. ( timeout=1s )
									 건강검진을 주기 ( period=10s )
									 1번 성공하면 성공으로 보겠다 ( #sucess=1 )
									 3번 실해하면 실패로 보겠다 ( # failure = 3)
									 

EXAMPLE

아래의 liveness-exam.yaml 파일에 self-healing 기능을 추가하시오.
- 동작되는 Pod내의 컨테이너에 /tmp/healthy 파일이 있는지 5초마다 확인한다.
- Pod 실행 후 10초 후부터 검사한다.
- 성공횟수는 1번, 실패횟수는 연속 2회로 구성한다.

apiVerison: v1
kind: Pod
metadata:
  name: livness-exam
spec:
  containers:
  - name: busybox-container
    image: busybox-container
	args:
	- /bin/sh
	- -c
	- touch /tmp/healthy; sleep 30; rm -rf /tmp/healty; sleep 600
  livenessProbe:
    exec:
	  command:
	  - ls
	  - /tmp/healthy
	periodSeconds: 5
	initialDelaySeconds: 10
	successThreshold: 1
	failureThreshold: 2

		
init container        

infra container(pause) 이해하기

static pod 만들기

Pod에 resource 할당 하기

환경변수를 이용해 컨테이너에 데이터 전달하기

Pod 구성 패턴의 종류

