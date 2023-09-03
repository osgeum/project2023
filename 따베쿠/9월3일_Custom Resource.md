CustomResource 이해

# kubectl api-resources 
리소스 확인 가능

Resource는 Kubernetes API에서 특정 종류의 API 오브젝트 모음을 저장하는 Endpoint

https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/

https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/

Custom Resource는 Kubernetes API의 extentsions.
- 쿠버네티스틑 Pods, Deployments와 같은 API 리소스를 사용자가 직접 추가하여 쓸 수 있도록 커스텀리소스(CustomResource) 기능들을 지원
- CustomResource를 정의해 놓으면 쿠버네티스 시스템 내부에서 kubectl명령어로 사용할 수 있고, 정의된 CustomResource를 이용해 API 컨트롤러들을 만들어서 사용하는 것도 가능.

CustomResourceDefinition 사용하기

CustomResourceDefinition 생성 -> Custom Object 만들기

