# kubectl

kube control

`kubectl api-resources` : 자원들의 종류와 단축어 등이 나온다.

```r
NAME                              SHORTNAMES   APIVERSION                        NAMESPACED   KIND
bindings                                       v1                                true         Binding
componentstatuses                 cs           v1                                false        ComponentStatus
configmaps                        cm           v1                                true         ConfigMap
endpoints                         ep           v1                                true         Endpoints
events                            ev           v1                                true         Event
limitranges                       limits       v1                                true         LimitRange
namespaces                        ns           v1                                false        Namespace
nodes                             no           v1                                false        Node
persistentvolumeclaims            pvc          v1                                true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                false        PersistentVolume
pods                              po           v1                                true         Pod
podtemplates                                   v1                                true         PodTemplate
replicationcontrollers            rc           v1                                true         ReplicationController
resourcequotas                    quota        v1                                true         ResourceQuota
secrets                                        v1                                true         Secret
serviceaccounts                   sa           v1                                true         ServiceAccount
services                          svc          v1                                true         Service
mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1   false        MutatingWebhookConfiguration
validatingwebhookconfigurations                admissionregistration.k8s.io/v1   false        ValidatingWebhookConfiguration
customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1           false        CustomResourceDefinition
apiservices                                    apiregistration.k8s.io/v1         false        APIService
controllerrevisions                            apps/v1                           true         ControllerRevision
daemonsets                        ds           apps/v1                           true         DaemonSet
deployments                       deploy       apps/v1                           true         Deployment
replicasets                       rs           apps/v1                           true         ReplicaSet
statefulsets                      sts          apps/v1                           true         StatefulSet
selfsubjectreviews                             authentication.k8s.io/v1          false        SelfSubjectReview
tokenreviews                                   authentication.k8s.io/v1          false        TokenReview
localsubjectaccessreviews                      authorization.k8s.io/v1           true         LocalSubjectAccessReview
selfsubjectaccessreviews                       authorization.k8s.io/v1           false        SelfSubjectAccessReview
selfsubjectrulesreviews                        authorization.k8s.io/v1           false        SelfSubjectRulesReview
subjectaccessreviews                           authorization.k8s.io/v1           false        SubjectAccessReview
horizontalpodautoscalers          hpa          autoscaling/v2                    true         HorizontalPodAutoscaler
cronjobs                          cj           batch/v1                          true         CronJob
jobs                                           batch/v1                          true         Job
certificatesigningrequests        csr          certificates.k8s.io/v1            false        CertificateSigningRequest
leases                                         coordination.k8s.io/v1            true         Lease
endpointslices                                 discovery.k8s.io/v1               true         EndpointSlice
events                            ev           events.k8s.io/v1                  true         Event
flowschemas                                    flowcontrol.apiserver.k8s.io/v1   false        FlowSchema
prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1   false        PriorityLevelConfiguration
ingressclasses                                 networking.k8s.io/v1              false        IngressClass
ingresses                         ing          networking.k8s.io/v1              true         Ingress
networkpolicies                   netpol       networking.k8s.io/v1              true         NetworkPolicy
runtimeclasses                                 node.k8s.io/v1                    false        RuntimeClass
poddisruptionbudgets              pdb          policy/v1                         true         PodDisruptionBudget
clusterrolebindings                            rbac.authorization.k8s.io/v1      false        ClusterRoleBinding
clusterroles                                   rbac.authorization.k8s.io/v1      false        ClusterRole
rolebindings                                   rbac.authorization.k8s.io/v1      true         RoleBinding
roles                                          rbac.authorization.k8s.io/v1      true         Role
priorityclasses                   pc           scheduling.k8s.io/v1              false        PriorityClass
csidrivers                                     storage.k8s.io/v1                 false        CSIDriver
csinodes                                       storage.k8s.io/v1                 false        CSINode
csistoragecapacities                           storage.k8s.io/v1                 true         CSIStorageCapacity
storageclasses                    sc           storage.k8s.io/v1                 false        StorageClass
volumeattachments                              storage.k8s.io/v1                 false        VolumeAttachment
```

`kubectl explain [api-resource]` 하면 그 자원에 대한 설명이 나온다..ㄷㄷ

## 기본

> 모든 API에 의해 리턴되는 JSON 오브젝트는 다음의 두가지를 가져야 한다.

- apiVersion `<string>`

  - 오브젝트가 가져야 할 스키마의 "**버전**"
  - `a string that identifies the version of the schema the object should have`
    - 특정 자원이 가질 필드 값이 버전에 따라 차이가 있을 수 있기 때문에 명확히 지정하기 위한 용도로 보인다.

- kind `<string>`
  - 오브젝트가 가져야 할 "**스키마**"
  - `a string that identifies the schema this object should have`
  - 오브젝트가 나타내는 REST 자원
  - 서버는 클라이언트의 요청이 제출되는 엔드포인트로부터 이를 유추한다.
  - camelCase

### Types (Kinds)

- 세 종류가 있다.

- `ListOptions`, `DeleteOptions`, `List`, `Status`, `WatchEvent`, `Scale` 와 같은 "meta" API 오브젝트는 모든 API 그룹에서 사용되며, `meta.k8s.io`에 속한다. 따라서 독립적으로 운용되며 프로그래밍 언어의 interface와 비슷한 역할을 한다.

#### Objects

- 시스템의 영속적 엔티티

- Creating API Resource = a record of intent

API 자원이 한번 생성되면 시스템은 그 자원의 존재를 보장하기 위해 노력한다. (선언적 API)  
모든 API 오브젝트는 공통된 메타데이터를 가진다.(?)

한 오브젝트는 여러 자원(C, R, U, D)을 가질 수 있다.

예시 - `Pod`, `ReplicationController`, `Service`, `Namespace`, `Node`

#### Lists

- 자원의 모음

- 명명 규칙: `List`로 끝나야한다.
  - `items` 필드가 존재해야 한다.
    - 역도 성립한다. `items` 필드가 존재하면 리스트여야 한다.

예시 - `PodList`, `ServiceList`, `NodeList`

`kind: List` 를 보게 된다면 그건 Kubernetes API가 아닌 혼합된 자원들을 관리하기 위한 툴의 구현 사항을 적은 것 뿐이다.

#### Simple

- 비영속적 엔티티에 해당하며 특정 Action을 수행

예를 들자면, 에러가 발생했을 때 `Status` kind 가 리턴되는데 이는 영속적인 것이 아니다. 사라진다.  
많은 Simple 자원들은 하위자원이다. 특정 자원과 밀접한 action이나 view를 노출하고 싶을 때 하위 자원을 사용한다. 다음과 같은 것들이 있다.

- `/binding` : 유저의 요청 (Pod, PersistentVolumeClain) 과 임의의 클러스터 기반 자원을 바인드 한다. (Node, PersistentVolume)

- `/status` : 자원의 상태만을 기술한다. 예를 들어 `/pods` 엔드포인트는 `metadata`와 `spec`만을 업데이트 할 수 있다.

- `/scale` : 특정 스키마와 독립적으로 자원의 개수를 읽거나 쓸때 (read/write)

- `proxy` , `portforward`

## bindings

```html
KIND: Binding VERSION: v1 DESCRIPTION: Binding ties one object to another; for
example, a pod is bound to a node by a scheduler. Deprecated in 1.7, please use
the bindings subresource of pods instead. FIELDS: apiVersion
<string>
  APIVersion defines the versioned schema of this representation of an object.
  Servers should convert recognized schemas to the latest internal value, and
  may reject unrecognized values. More info:
  https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources
  kind
  <string>
    Kind is a string value representing the REST resource this object
    represents. Servers may infer this from the endpoint the client submits
    requests to. Cannot be updated. In CamelCase. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds
    metadata
    <ObjectMeta>
      Standard object's metadata. More info:
      https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
      target
      <ObjectReference>
        -required- The target object that you want to bind to the standard
        object.</ObjectReference
      ></ObjectMeta
    ></string
  ></string
>
```

오브젝트들끼리 연결해주는 역할을 한다. 대표적인 예로, 파드를 노드에 바인딩한다. 이는 스케쥴러에 의해 자동으로 실행되기 때문에 사용자는 이 자원을 직접 사용하는 경우가 드물다.
