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

### Object 의 Metadata

nested 필드인 "metadata" 안에 다음의 항목들이 필수이다.

- `namespace` : DNS compatible label
- `name` : 개개의 오브젝트에 접근할 때 경로로 사용됨. 현재 namespace에서 유일해야 한다.
- `uid` : 같은 name을 가진 오브젝트 사이에서도 구분하기 위해 필요

다음의 항목들은 선택이다.

- `resourceVersion` : 오브젝트의 수정날짜를 유추할 수 있는 버전 정보. 클라이언트가 관찰할 수 있지만, 수정해서는 안된다. 그대로 서버에 다시 돌려줘야 함.
- `generation` : 특정 상태가 생성된 sequence number를 나타냄. 시스템에 의해 부여되어 자동으로 증가함.
- `creationTimestamp` : 오브젝트가 생성된 날짜와 시간
- `deletionTimestamp` : 오브젝트가 삭제될 날짜와 시간, 유저가 정상적으로 삭제를 요청했을 때 서버에 의해 부여된다. 오브젝트가 finalizer가 설정되지 않았다면 해당 시간 후에 오브젝트가 삭제된다. finalizer 가 설정되어 있다면 최소한 그게 없어질 때까지 삭제가 보류된다.
- `labels` : 오브젝트를 조직하고 범주화하기 위해 쓰일 수 있는 key, value
- `annotations` : 해당 오브젝트에 대한 임의의 메타데이터를 저장하거나 복원하기 위해 쓰일 수 있는 key, value

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

## ComponentStatus

클러스터의 유효성검증 정보를 담는다.

[stackoverflow](https://stackoverflow.com/questions/73407661/componentstatus-is-deprecated-what-to-use-then)

v1.19 부터 deprecated.

위의 stackoverflow에서 벌이는 논쟁은

`kubectl get --raw='/readyz?verbose'` 혹은 로컬 환경에서 `curl -k https://localhost:6443/livez?verbose` 로 `etcd, kube-scheduler, and kube-controller-manager`의 상태를 확인할 수 있다는 말인데,

원래 Kubernetes API server의 경우 `healthz`, `livez`, `readyz` 의 세 API로 현재 상태를 나타냈는데, `healthz` 도 v1.16 부터 deprecated 여서 `livez`, `readyz` 를 사용해야 한다는 것이다.

반면에 댓글은 해당 API 들이 정상으로 나와도 componentstatuses 는 문제를 띠고 있을 수 있고, 실제로 문제가 있었다고 주장하고 있다.

## ConfigMaps

기밀(은 configmap 말고 Secret으로)이 아닌 데이터를 키-밸류 쌍으로 저장하기 위한 API 오브젝트이다.

파드들은 이를 환경변수처럼, command-line 인자로, 볼륨의 config 파일로 쓸 수 있다.

컨테이너 이미지가 환경 종속적인 config와의 연결이 느슨해지게 해서 어플리케이션의 이동성이 좋아진다.

### 동기

앱과 그 config 파일을 분리하자. 예를 들어, `DATABASE_HOST` 변수를 클라우드 상에서는 kuberenetes service로, 로컬에서는 `localhost`로 설정할 수 있을 것이다.

### ConfigMap Object

다른 kubernetes 오브젝트들과 달리 ConfigMap은 `spec` 이 아닌, `data`(UTF-8)와 `binaryData`(base64-encoded string) 필드를 가진다. 두 필드 모두 key-value 쌍으로 구성된다. `key`는 모두 알파벳, 숫자, `-`, `_`, `.` 으로 이루어져야 한다.

### ConfigMaps and Pods

Pod의 `spec`을 같은 namespace 안에 있는 ConfigMap을 참조하도록 작성할 수 있다.

- 작성예시

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  # property-like keys; each key maps to a simple value
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"

  # file-like keys
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
```

Pod 안의 컨테이너가 Configmap을 사용하는 4가지 방법이 있다.

1. 컨테이너 커맨드 혹은 내부 인자로.
2. 컨테이너 환경변수로.
3. 읽기 전용 volume에 어플리케이션이 읽을 수 있도록 추가.
4. **Write code to run inside the Pod that uses the Kubernetes API to read a ConfigMap**

마지막 방법의 특별한 점은 ConfigMap과 그 데이터를 읽기 위해 코드를 따로 작성해야 한다는 점이다. 때문에 ConfigMap이 변화할 때마다 그에 맞는 반응을 분기로 작성하는 등의 방식이 가능하다. API를 직접 호출하기 때문에 다른 namespace의 ConfigMap에도 접근이 가능하다는 점이 큰 장점이다.

- 작성예시

```yml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo-pod
spec:
  containers:
    - name: demo
      image: alpine
      command: ["sleep", "3600"]
      env:
        # Define the environment variable
        - name: PLAYER_INITIAL_LIVES # Notice that the case is different here
          # from the key name in the ConfigMap.
          valueFrom:
            configMapKeyRef:
              name: game-demo # The ConfigMap this value comes from.
              key: player_initial_lives # The key to fetch.
        - name: UI_PROPERTIES_FILE_NAME
          valueFrom:
            configMapKeyRef:
              name: game-demo
              key: ui_properties_file_name
      volumeMounts:
        - name: config
          mountPath: "/config"
          readOnly: true
  volumes:
    # You set volumes at the Pod level, then mount them into containers inside that Pod
    - name: config
      configMap:
        # Provide the name of the ConfigMap you want to mount.
        name: game-demo
        # An array of keys from the ConfigMap to create as files
        items:
          - key: "game.properties"
            path: "game.properties"
          - key: "user-interface.properties"
            path: "user-interface.properties"
```

단일 줄로 작성하든, 여러 줄에 걸쳐 file-like 값으로 작성하든 상관없다. 그저 어떻게 다른 오브젝트가 그 값들을 사용하는지에 달려있다.

위의 예제에서 `demo` 컨테이너에 volum을 정의하고 이를 /config 로서 마운트 한 것은 두 개의 파일을 생성한다.

1. /config/game.properties
2. /config/user-interface.properties

키는 정확히 4개로 보이지만, `items` 를 이용해 배열로 묶었기 때문에 두 개만 생성된다. `items`를 생략하면 생각하는 대로 네 개의 파일을 얻을 수 있다.

### Using ConfigMaps
