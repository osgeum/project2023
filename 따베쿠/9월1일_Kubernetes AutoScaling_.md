Kubernetes Autoscaling

실무 CSP 환경에서 Cluster level scalability 기능을 사용량을
VPA 보다는 HPA를 많이 사용량


- Cluster level scalability
  자원이 부족할시 worker node 증설하여 scaleout 진행 ( CSP 에서 사용 ( aws, azuer, gcp ))
  openstack의 auto scaling kubernetes 클러스터
  
  Pod가 node 리소스를 할당 받지 못해 pending 될 떄 worker node를 확장
  Node Pool의 min/max를 기준으로 그 범위내로 노드 확장
  할당된 node가 장시간 충분히 활용되지 못하면 node를 해제
  10초마다 불필요한 노드 확인, 10분간 적은 리소스 유지하면 Scale Down
  
- Pods layer autoscale
  Horizontal Pod Autoscaler(HPA) - 수평 , pod 갯수 증가
  
  Metrics-Server
  - 각 Pod와 Node의 사용량을 모니터링하고 API를 통해 볼 수 있게 제공 ( CPU 나 MEM 중 사용량의 메트릭 정보를 수집해서 etcd에 기록 )
  - kubectl top 커맨드 지원
  - horizontal-pod-autoscale를 통해 원하는 Replicas 수 결정
    원하는 레플리카 수 = ceil[현재 레프리카수 * ( 현재 메트릭 값 / 원하는 메트릭 값 )]
  
    
  HPA는 Pod의 replicas 수를 관리
  - 구동중인 Pod의 CPU/Memory 사용율을 기반으로 Pod를 Scale-Out
  - 확장/축소할 최소/최대 Pods 수량은 Pods의 Deployment에 의해 제어
  
  HPA 동작 조건
  - HPA는 기본 30초 간격으로 Pod 리소스 사용량을 check HPA에 설정한 임계 값을 초과할 경우 Pod를 활장
  - Scale-out 이후 3분 대기, Scale-in 이후 5분 대기
  
  구성 순서
  1. Metrics-Server 구성 ( pod )
     Metrics-Server 설치
	# git clone https://github.com/237summit/kubernetes-metrics-server.git
	# cd kubernetes-metrics-server
	# kubectl apply -f .
	# kubectl get pods -A
	
	Metrics-Server 설치 확인
	# kubectl top nodes
	# kubectl top pods -A
	
  2. Deployment로 동작되는 Service 구성
  3. HPA 구성
  
  https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/
  
  
  Vertical Pods Autoscaler(VPA) - 수직, pod의 스펙 상향
  
  VerticlaPodAutoscaler라는 사용자 정의 리소스의 정의
  Pod의 리소스를 관리
  - Pod대한 CPU/Memory 리소스를 추천
  - Pod대한 CPU/Memory 리소스를 자동으로 조정
  동작방식
  - metrics를 10초 간격으로 지속적으로 확인
  - 할당된 CPU/Memory의 임계치를 넘으면 pod 템플릿을 변경하여 Pod리소스 할당 값을 변경한후 Pod를 다시 시작.
  
  VerticlaPodAutoscaler라는 사용자 정의 리소스로 구성
  
  Metrics-Server
  - 각 Pod와 Node의 사용량을 모니터링하고 API를 통해 볼 수 있게 제공 ( CPU 나 MEM 중 사용량의 메트릭 정보를 수집해서 etcd에 기록 )
    
  구성 순서
  1. Metrics-Server 구성 ( pod )
  2. Deployment로 동작되는 Service 구성
  3. VPA 구성
  
  https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/
  
HPA Autoscaling 운영

