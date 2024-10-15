## ComponentStatus

클러스터의 유효성검증 정보를 담는다.

[stackoverflow](https://stackoverflow.com/questions/73407661/componentstatus-is-deprecated-what-to-use-then)

v1.19 부터 deprecated.

위의 stackoverflow에서 벌이는 논쟁은

`kubectl get --raw='/readyz?verbose'` 혹은 로컬 환경에서 `curl -k https://localhost:6443/livez?verbose` 로 `etcd, kube-scheduler, and kube-controller-manager`의 상태를 확인할 수 있다는 말인데,

원래 Kubernetes API server의 경우 `healthz`, `livez`, `readyz` 의 세 API로 현재 상태를 나타냈는데, `healthz` 도 v1.16 부터 deprecated 여서 `livez`, `readyz` 를 사용해야 한다는 것이다.

반면에 댓글은 해당 API 들이 정상으로 나와도 componentstatuses 는 문제를 띠고 있을 수 있고, 실제로 문제가 있었다고 주장하고 있다.
