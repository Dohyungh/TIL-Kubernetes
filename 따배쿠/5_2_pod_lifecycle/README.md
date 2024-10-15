# Pod lifecycle

`Pending` - `Running` - `Succeded` / `Failed`

Pods 도 컨테이너 처럼 일시적인 resource에 속한다는 사실을 잊지 말자. Pods 들은 생성될 때 UID 를 받아 식별가능 하며, 어떤 Node에서 실행될지 스케줄링 된다. Node 가 죽으면 Control plane은 그 Node 에서 구동하고 있던 pods 를 일정 시간 후에 삭제한다.

## Pod lifetime

- 쿠버네티스는 파드의 상태를 관찰하고, 해당 상태를 유지하기 위해 해야할 다음 동작을 결정한다.

- 사용자는 필요하다면, 파드의 상태를 기술하는 방식을 직접 정의할 수도 있다.

- 파드는 생애 주기 동안 단 한번만 스케줄링 된다. 여기서 파드가 특정 노드에 배정되는 것을 `스케줄링`이라고 하고, 그 배정을 `바인딩`이라고 한다.

## Pods and fault recovery

컨테이너가 실패하면 쿠버네티스는 일단 해당 컨테이너를 재시작한다.

반면에, 컨테이너가 아닌 파드가 클러스터가 회복할 수 없을 정도로 실패하면 파드를 회복하는 것을 포기한다. 해당 파드를 **삭제** 하고, 다른 컴포넌트의 회복 가능성에 의존해버린다.

- 이때 연관된 `volume` 도 같이 삭제되고 다시 생성된다.

- 파드는 같은 UID로 reschedule 되지 않는다.

해당 reschedule은 `controller`가 주관하는데, 다시 스케줄링 될 때 실패했던 해당 노드로 스케줄링 다시 스케줄링 된다는 보장은 없다.

## Pod phase

파드의 `status` 필드에는 `PodStatus` 오브젝트가 존재하고, `phase` 필드를 가진다.

phase 가 가질 수 있는 값들은 다음과 같다.

| Value     | Description                                                                                                        |
| --------- | ------------------------------------------------------------------------------------------------------------------ |
| Pending   | 클러스터에게 받아들여졌지만, 준비가 덜 된 상태. 스케줄링을 기다리고 있거나, 컨테이너를 다운로드 받고 있을 수 있다. |
| Running   | 바운딩이 이루어졌고, 컨테이너가 작동중이거나 시작 혹은 재시작 중이다.                                              |
| Succeeded | 성공적으로 파드의 모든 컨테이너가 종료됨.                                                                          |
| Failed    | 파드의 모든 컨테이너가 실패하면서 종료됨.                                                                          |
| Unknwon   | Pod의 상태가 수집될 수 없었음을 의미. 노드에 문제가 생겼을 확률이 높다.                                            |

## Pod conditions

- `PodScheduled` : 스케줄링 됨
- `PodReadyToStartContainers` : 샌드박스가 성공적으로 생성되었으며, 네트워킹이 확인됨
- `ContainersReady` : 모든 컨테이너가 준비상태임
- `Initialized` : 모든 `init containers`가 성공적으로 완료됨
- `Ready` : 파드가 요청을 처리할 수 있고, 매칭된 Service, 로드밸런싱 pool에 추가되어야 함을 의미.

## Container states

컨테이너의 상태 또한 수집된다! 컨테이너 생명주기 훅으로 그 사이 사이에 이벤트를 발생시킬 수 있다.

다음의 세가지 상태가 있다.

- waiting
- Running
  - Running 상태를 봤다면 `postStart` 훅의 내용은 이미 실행됐을 것이다.
- Terminated
  - Terminated 상태 이전에 `preStop` 훅이 실행된다.

## 파드가 컨테이너 문제를 해결하는 방법

Pod의 `spec`에 기술된 `restartPolicy`에 따라 컨테이너를 관리한다.

- `Always`, `OnFailure`, `Never`의 세가지 종류가 있다.

- sidecar 컨테이너는 `initContainers` 에 정의되어 있기에 컨테이너 레벨의 재시작 정책은 `Always`로 설정되어 있다.

- `initContainers`는 재시작 정책이 `OnFailure` 이어도 재시작 되는 컨테이너이다.

1. Initial crash : 즉시 컨테이너를 재시작한다.
2. Repeated crashes : 지수적으로 증가하는 backoff delay를 설정해가면서 컨테이너를 재시작한다. 과도한 재시작을 요구를 예방한다.
3. CrashLoopBackOff state : 자꾸 재시작 중임을 의미
4. Backoff reset : 한번이라도 재시작이 되어서 일정 시간동안 정상적으로 작동했다면 backoff delay를 리셋한다.

`CrashLoopBackOff`를 해결하기 위해 열심히 일해라~
