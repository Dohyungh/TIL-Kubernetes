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

ConfigMaps 는 data volume으로 마운트 될 수 있으며, 직접적인 노출 없이도 시스템 내 다른 부분에서 사용할 수 있다.
가장 흔하게는 같은 namespace안에 있는 pod내 컨테이너들을 위한 공통 config 양식을 만들곤 한다.
`addon` 이나 `operator` 들이 ConfigMap 을 기반으로 동작을 조정하는 것을 볼 수 있을 것이다.

#### Pod에서 ConfigMap을 파일로 사용하기

```yml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: mypod
      image: redis
      volumeMounts:
        - name: foo
          mountPath: "/etc/foo" # ConfigMap이 나타날 그 폴더 이름을 적어라
          readOnly: true # 여기저기서 수정하면 안되겠지?
  volumes:
    - name: foo
      configMap:
        name: myconfigmap
```

한 pod에 여러 container가 있을 경우 `containers` 란에 컨테이너들을 모두 적어주고, 각자 `volumeMounts`도 모두 적어줘야 한다.
그러나 `volumes`에 있는 `configMap`은 한 번만 적으면 된다.

#### 마운트 된 ConfigMap은 자동으로 업데이트 된다.

우리의 kubelet이 동기화 주기마다 검사해서 ConfigMap 이 바뀌었다면 자동으로 업데이트 해준다.
`watch`, `ttl-based`, `모든 요청 API 서버로 redirect` 방식을 사용할 수 있다.

- 환경변수로 작동하는 ConfigMap은 자동 업데이트가 안된다.
- subPath 볼륨으로 마운트 된 ConfigMap은 업데이트 적용이 안된다.

#### 환경변수로서 ConfigMap 사용하기

다음은 예시 파일이다. "myconfigmap.yaml"

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfigmap
data:
  username: k8s-admin
  access_level: "1"
```

`username` 과 `access_level`을 프로퍼티로 가지고 있다.

아래 명령어로 ConfigMap 오브젝트를 만들 수 있다.

```r
kubectl apply -f myconfigmap.yaml
```

다음 yaml 파일로 만들어진 파드는 myconfigmap의 내용을 환경변수로 쓸 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-configmap
spec:
  containers:
    - name: app
      command: ["/bin/sh", "-c", "printenv"]
      image: busybox:latest

      # 전부 가져오기
      envFrom:
        - configMapRef:
            name: myconfigmap

      # 일부만 이름을 지정해 가져오기
      env:
        - name: CONFIGMAP_USERNAME
          valueFrom:
            configMapKeyRef:
              name: myconfigmap
              key: username
```

`envFrom`필드는 그 내부의 source 들로부터 환경변수를 만들어 준다. `configMapRef` 는 `name`을 기준으로 ConfigMap을 선택하고, 그 내부의 모든 key-value 쌍을 환경변수로 설정한다.

ConfigMap의 변화가 의도치 않았는데 발생했을 불상사를 미연에 방지하고자 immutable Configmap을 지원하고 있다.

`immutable: true` 를 ConfigMap의 yaml 파일에 추가해 주면 된다. 다만, 이 설정은 후에 변경이 불가능하기에 Pod를 삭제하고 다시 설치해야 한다.
