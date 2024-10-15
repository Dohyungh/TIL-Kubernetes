## Namespaces

[kubernetes.io/namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

> 나는 마치 "폴더" 같다고 느꼈다.

`namespaces`는 단일 클러스터 내에서 자원 그룹을 구분지어 준다. 자원의 이름은 한 namespace 내에서는 유일해야 하지만, namespace 들에서 유일해야 할 필요는 없다.

`Deployments`, `Services` 와 같은 namespaced objects 에 한해서만 Namespace-based scoping이 가능하다. `StorageClass`, `Nodes`, `PersistentVolumes`와 같은 Cluster-wide 오브젝트는 불가능하다.

### 그래서 언제 어떻게 쓰죠?

- 기본적으로는 다인원의 프로젝트를 관리하기 위해서 만들어졌다. 소수 프로젝트에서는 namespace 자체를 고려할 필요가 없을 수 있다.

- 아주 미세한 변화만 있는 자원들을 구분하기 위해서 namespace를 쓸 필요는 없다. 그럴 때는 `labels`를 사용하는 것을 권장한다.

### 최초 namespaces

#### default

default namespace는 namespace 생성없이 새로운 클러스터 생성이 가능하도록 하기 위함이다.

#### kube-node-lease

`Lease` 오브젝트들을 담고 있다. 컨트롤 플레인이 노드가 죽었는지 알 수 있도록 kubelet이 지속적으로 heartbeats를 보내도록 한다.

#### kube-public

모든 클라이언트(인증이 안된 것도 포함) 가 읽을 수 있도록 한다. 클러스터 전체에서 접근 가능하도록 하기 위해 사용한다.

#### kube-system

k8s 시스템에 의해 생성된다.

### 사용

- `--namespace` 혹은 `-n`

- namespace 보여줘

```
kubectl get namespace
```

```
NAME              STATUS   AGE
default           Active   1d
kube-node-lease   Active   1d
kube-public       Active   1d
kube-system       Active   1d
```

#### namespace 설정

```
kubectl config set-context --current --namespace=cookie

# 확인해보자
kubectl config view --minify | grep namespace:

kubectl config use-context cookie
```

#### Namespaces and DNS

`Service`를 생성하면, 그와 맞는 `DNS entry`도 생성한다. 다음과 같이 생겼다. `<svc-name>.<namespace-name>.svc.cluster.local`

이를 통해 namespace를 가지고 `개발`, `테스트`, `배포` 등의 환경을 구분하더라도 똑같이 `svc-name`만 작성해서 접근할 수 있다.

외부 namespace의 Service에 접근하고 싶다면 FQDN(Fully Qualified Domain Name)으로 작성해야 한다. (위의 풀 네임을 써야한다.)
