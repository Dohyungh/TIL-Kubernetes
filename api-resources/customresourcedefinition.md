## Custom Resource Definition

[k8s custom resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

커스텀 리소스가 어떤 데이터로 구성되어 있는지 정해놓은 것.

일종의 선언적 메타데이터이다. 따라서 껍데기에 다를바 없는데,

컨트롤러를 구현함으로써 해당 메타데이터가 어떤 역할, 혹은 요청을 받아 처리할지가 결정된다.

### Custom Resources

`Pods` 가 파드의 모임으로 정의되어 k8s에서 사용할 수 있는 것 처럼, 새롭게 사용자가 Resource를 정의할 수 있다. 여기서 Resource 는 k8s API의 endpoint로, API 오브젝트의 그룹을 저장한다.

Custom Resource는 원래 k8s가 기본으로 설치, 구동해주는 자원은 아니지만, 많은 것들이 custom resource를 통해 만들어지고 배포되고 있다. 이는 k8s의 모듈화에 좋은 영향을 미치고 있다.

### Custom Controllers

Custom Resource는 Custom Controller와 같이 썼을 때 특히 강력하다.
진정한 의미의 선언적 API를 구현할 수 있기 때문이다. (~~실제로 써봐야 느낌이 올 듯?~~)

클러스터에 Custom Controller를 언제든 배포할 수 있다. 그대로도 기존의 k8s 자원을 마음껏 사용 가능하지만,
공식 문서에서는 Custom Resource와 같이 쓰기를 특히 추천하고 있다.
`Operator Pattern`은 그 둘을 엮어주는 역할을 한다.

### k8s 클러스터에 꼭 추가해야 하는가?

- k8s api aggregation에 대한 내용인데, CRD가 아닌 다른 방법으로 Custom Resource를 만드는 방법에 대한 내용이다.

- 더 어려운 방법이나, 더 low한 레벨에서 API를 만들어 kube-apiserver 에 통합시킬 수 있다.

| Aggregation을 추천해요                                           | 그냥 Stand-alone API를 추천해요                      |
| ---------------------------------------------------------------- | ---------------------------------------------------- |
| 선언적 API                                                       | 그리 선언적이지 않은 모델일 때                       |
| kubectl로 접근하고 싶을 때                                       | 굳이 kubectl이 필요 없을 때                          |
| k8s UI 상에서 새로운 타입이 보고 싶을 때 like 대시보드           | k8s UI 가 필요없을 때                                |
| 새로운 API를 개발 중일 때                                        | 이미 만들어 놓고 작동을 잘하고 있을 때               |
| API Groups와 Namespaces, 자원 Path 등의 제약을 감내할 수 있을 때 | 이미 정의된 REST Path와 겹치지 않는 Path가 필요할 때 |
| 자원이 클러스터나 namespace에 자연스럽게 속해 있을 때            | 자원 Path 제약이 싫을 때                             |
| k8s API feature를 재사용하고 싶을 때                             | 필요 없을 때                                         |
