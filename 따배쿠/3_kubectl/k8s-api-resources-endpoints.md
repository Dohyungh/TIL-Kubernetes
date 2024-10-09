## EndPoints

참고:

[kmaster의 Kubernetes 이야기](https://kmaster.tistory.com/112)

[What is an 'endpoint' in kubernetes](https://stackoverflow.com/questions/52857825/what-is-an-endpoint-in-kubernetes)

`Service` 와 각 `Pod`의 트래픽 분배를 담당하는 인터페이스 계층이다.

왜 인터페이스 계층이 필요할지 생각해보면, 각 파드는 언제든 죽고 다시 태어날 수 있다. 그럴 때마다 각 파드의 IP는 달라지기 마련인데, 해당 IP가 죽었고, 다시 새로운 IP로 연결해줘야 한다는 사실을 Service는 알 수 있어야 한다. 해당 문제를 해결하기 위해 중간에 도입된 것이 EndPoints(이름 자체가 복수형이다.) 라고 생각하면 된다.

따라서 Service를 선언했을 때 담당하는 Pod의 개수에 따라 EndPoints를 가져오도록 명령했을 때 IP와 포트 묶음이 보이게 된다.

## EndPointSlices

EndPoint의 확장성이 떨어지다 보니 새롭게 적용시킨 EndPoint 묶음이다.
100개의 EndPoint를 최대로 묶을 수 있으며, 100개를 넘으면 새로운 EndPointSlices가 생성되어야 한다.

Service의 Selector가 지정되면 자동으로 EndPointSlices가 생성된다.

(~~Service를 먼저 공부하고 다시 오자. 순서가 잘못 됐다.~~)
