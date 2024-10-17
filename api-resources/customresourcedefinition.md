## Custom Resource Definition

[k8s custom resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

커스텀 리소스가 어떤 데이터로 구성되어 있는지 정해놓은 것.

일종의 선언적 메타데이터이다. 따라서 껍데기에 다를바 없는데,

컨트롤러를 구현함으로써 해당 메타데이터가 어떤 역할, 혹은 요청을 받아 처리할지가 결정된다.

### Custom Resources

`Pods` 가 파드의 모임으로 정의되어 k8s에서 사용할 수 있는 것 처럼, 새롭게 사용자가 Resource를 정의할 수 있다. 여기서 Resource 는 k8s API의 endpoint로, API 오브젝트의 그룹을 저장한다.

Custom Resource는 원래 k8s가 기본으로 설치, 구동해주는 자원은 아니지만, 많은 것들이 custom resource를 통해 만들어지고 배포되고 있다. 이는 k8s의 모듈화에 좋은 영향을 미치고 있다.

### Custom Controllers
