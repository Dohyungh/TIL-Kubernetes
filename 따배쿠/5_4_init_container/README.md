# Init Containers

`containers` 파트에 정의되는 app 컨테이너가 실행되기 전에 utilities나 setup scripts 등의 기능을 제공하고 종료되는 컨테이너를 `Init Container`라고 한다.

메인이 되는 app 컨테이너가 실행되기 전에 시작해서 app 컨테이너 구동 동안에 같이 구동하고 있는 `Sidecar container`와는 다르다.

## Init Container 이해하기

파드 안에는 여러 컨테이너가 있을 수 있다. 동시에 어플리케이션 컨테이너가 작동하기 전에 실행되고 종료되는 Init Container도 여러개 있을 수 있다.

- Init Container는 항상 "완료"된다.
- 다음 Init Container가 시작되기 전에 항상 성공적으로 완료되어야 한다.

Init Container가 구동에 실패해도, kubelet은 계속해서 재구동시킬 것이다. (`restartPolicy`가 Never 가 아니라면.)

Pod 의 세부항목에 `initContainers` 필드에 `container` 아이템들을 추가하면 `Init Container`로 지정할 수 있다.

### 일반, SideCar 컨테이너와의 차이점

일반 app 컨테이너의 모든 필드와 특징들을 사용할 수 있다. (자원 limit, 볼륨, 보안 세팅)

그러나 `lifecycle`, `livenessProbe`, `readinessProbe`, `startupProbe`는 지원하지 않는다. 항상 app 컨테이너가 구동되기 전에 완료되어야 한다는 점을 생각해보면 이유가 납득이 간다. (사이드카 컨테이너가 앱 컨테이너와 동시에 구동되어야 하기 때문에 probe를 지원한다는 점과 정확히 반대다.)

Init Container를 여러개 지정할 경우 이전 컨테이너가 성공해야 다음 컨테이너로 넘어가다. 

## Init Container 사용하기

app 컨테이너와는 다른 이미지를 사용한다는 점에서, 시작단계 관련 코드를 작성하는 데에 여러 이점들이 있다.

- Init Containers는 app 이미지에는 없는 utilities나 custom code를 사용할 수 있다. 예를 들어 시작단계에서 `sed`, `awk`, `python`, `dig` 같은 툴을 사용하기 위해서 굳이 새로운 이미지를 파생시킬 필요가 없다.
- app 이미지와 배포 역할을 한 이미지에 작성할 필요 없이 분리할 수 있는 것이다.
- 파일 시스템에 app 컨테이너와는 다른 방법으로 접근할 수 있다. (app 컨테이너는 접근할 수 없는 `Secrets`에 접근 가능하다.)
- app 컨테이너의 구동 시작을 특정 조건에 맞출 수 있게 늦춰주는 역할을 할 수 있다.
- 보안 관련해 유리해진다. (app 컨테이너에 굳이 안넣어도 되는 것들을 init container에 넣어서 접근 불가능하게 할 수 있으니.)

### 예제

#### Service가 구동될 때까지 기다리기

```
for i in {1..100}; do sleep 1; if nslookup myservice; then exit 0; fi; done; exit 1
```

#### 원격 서버 API로 파드 등록하기(?)

```
curl -X POST http://$MANAGEMENT_SERVICE_HOST:$MANAGEMENT_SERVICE_PORT/register -d 'instance=$(<POD_NAME>)&ip=$(<POD_IP>)'
```

#### 잠시 기다리기

```
sleep 60
```

#### git 레포 `Volume`에 클론하기

#### configuration 파일에 값들 넣어놓고(ex. POD_IP) 동적으로 app 컨테이너 configuration 생성하기

### yaml 예제

- 순서대로 `myservice`와 `mydb`를 기다린다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done"]
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"]
```