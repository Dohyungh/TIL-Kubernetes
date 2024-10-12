## Service

[kubernetes.io](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors)

쿠버네티스의 기본 자원 타입이다.

파드 내부의 어플리케이션이 외부와 연결되기 위해 꼭 필요한 자원이며,

ClusterIP, NodePort, LoadBalancer 의 세 가지 타입이 있다.

파드는 계속해서 삭제되고 다시 생성되기 때문에 매번 바뀌는 IP를 이용해 포워딩해주기는 어려운 일이다.

이 점을 해소해주기 위한 것이 Service 자원이다.

---

한 클러스터 내에 여러개의 파드를 네트워크에 노출시키기 위한 방법이 바로 Service 이다.  
Service 자원의 핵심 목표는 파드 내부의 코드에서 복잡한 클라우드 환경에 대하 설정을 하지 않아도 되게 만드는 것이다.

만약 `Deployment`로 앱을 구동하고 있다면, 파드가 삭제되고 생성되고를 반복할 것이다. 즉, 파드는 분명 극히 일시적으로 존재하는 자원임을 명심해야 한다.

네트워크 플러그인이 배정해주는 IP 주소가 매번 바뀌고 있기 때문에, 지금의 Pod 주소와 나중의 Pod 주소는 다를 수 있다. 과연 서로 의존성이 있는 Pod 들끼리 클러스터 내에서 소통한다면 어떻게 해야 할까?

---

> 네트워크 상에 파드 그룹을 노출 시키기 위한 추상화 계층이 바로 Service이다.

각각의 Service 오브젝트는 endpoints 의 논리적 집합(logical set) 을 정의하는데 이때 대부분 엔드포인트는 Pod를 가리킨다.
좀 더 구체적 예시로서, Front 와 Back 의 replica 들이 떠 있다고 해보자. 각 Front 가 Back에 요청할 때 구체적으로 어떤 복사본으로 요청을 보낼 지는 사실 Front 입장에서 노관심이다. 이런 Decoupling을 구현해 주기 위한 것이 바로 Service 오브젝트이다.

Service가 Pod 들을 지목하기 위해서 대체로 `selector`를 이용한다. 물론 다른 방법도 있다.
HTTP를 사용할 예정이라면 `Ingress` 도 좋은 방법이다. Ingress는 Service 오브젝트는 아니지만, 클러스터의 entry point로 작동한다.
Ingress는 여러 라우팅 규칙을 하나의 자원으로 통합시켜 주는 아주 유용한 친구다.

`Gateway`는 더 많은 기능을 내포하고 있다. API 묶음에 속하는 Gateway는 CRD(Custom Resource Definitions)로 구현된다.

### Defining Service

```yml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

- `app.kubernetes.io/name=MyApp` 으로 명명된 Pods가 9376 TCP 포트에서 듣고 있다고들 해보자.
- 해당 TCP Listener를 위의 yaml 파일을 apply 함으로써 만들 수 있다.
- kubernetes는 이 Service에 IP 주소 (Cluster IP)를 할당하고, 이는 가상 IP 주소로 쓰인다.
- 이 Service는 계속해서 label과 일치하는 Pods를 스캔한다.

> port 는 기본적으로 tartgetPort와 동일한 값을 가진다. (default)
> protocol은 기본적으로 TCP 로 설정된다.

---

이외에도 Selector 없이 Service 선언하기, targetPort의 변수화, Custom EndpointSlices와 관련한 작성법과 Convention 등을 본 문서 최상단의 k8s.io 공식 문서에서 확인할 수 있다.

다 적으려면 끝도 없을 것 같아서 이만 줄인다.

### Service Type

#### ClusterIP

#### NodePort

#### LoadBalancer

#### ExternalName
