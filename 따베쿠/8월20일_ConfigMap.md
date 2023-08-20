ConfigMap 생성
ConfigMap: 컨테이너 구성정보를 한곳에 모아서 관리

# kubectl create configmap NAME --from-literal=key1=value1

# kubectl create configmap ttabae-config --from-liternal=INTERVAL=2 --from-liternal=OPTION=boy --from-file=config.dir/

# kubectl create configmap ttabae-config --from-literal=INTERVAL=2 --from-literal=OPTION=boy --from-file=config.dir/

# kubectl get configmaps ttabae-config
ConfigMap의 일부분을 적용하기
spec:
  containers:
  - image: smlinux/genid:env
    env:
    - name: INTERVAL
      valueFrom:
        configMapKeyRef:
          name: ttabae-config
          key: INTERVAL 
		  
ConfigMap 전체를 적용하기
spec:
  containers:
  - image: smlinux/genid:env
    envFrom:
    - configMapRef:
        name: ttabae-config
		
ConfigMap을 볼륨으로 적용하기
- name: config
    configMap:
      name: ttabae-config
      items:
      - key: nginx-config.conf
        path: nginx-config.conf
      - key: INTERVAL
        path: INTERVAL
