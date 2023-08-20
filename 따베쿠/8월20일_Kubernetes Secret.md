ConfigMap과 secret

ConfigMap: 컨테이너 구성 정보를 한곳에 모아서 관리

Secret: 컨테이너가 사용하는 password,auth token, ssh key와 같은 중요한 정보를 저장하고 민감한 구성정조를 base64로 인코딩해서 한 곳에 모아서 관리

민감하지 않은 일반 설정파일 ConfigMap을 사용하고 민감한 데이터는 secret을 사용하고

Secret 데이터 전달 방법
- Command-line Argument
- Environment Variable
- Volume Mount

secret 만들기
# kubectl create secret <Available Commands> name [flags] [options]
<Available Commands>
- docker-registry    ( Create a secret for use with a Docker registry )
- generic ( Create a secret from a local file, directory or literal value )
- tls ( Create TLS secret )

ex) 
# kubectl create secret tls my-secret --cert=path/to/cert/file --key=path/to/key/file
# kubectl create secret docker-registry reg-secret --docker-username=tiger --docker-password=pass --docker-email=tiger@acme.com
# kubectl create generic ttabae-secret --from-literal=INTERVAL=2 --from-file=./genid-web-config/

secret 사용하기

secret 데이터 용량 제한

Secret etcd에 암호화 하지 않은 텍스트로 저장되므로 secret value가 커지면 메모리 용량을 많이 사용하게 됨.

secret의 최대 크기는 1MB
