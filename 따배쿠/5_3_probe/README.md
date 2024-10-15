# Probe

## exec

특정 커맨드를 컨테이너 안에서 실행한다. 0을 반환하면 성공으로 간주한다.

## grpc

원격 procedure 요청을 gRPC로 보낸다. 대상은 gRPC health checks를 구현한 상태여야 한다. 응답의 상태가 `SERVING`이면 성공으로 간주한다.

## httpGet

HTTP `GET` 요청을 보내고, 200과 399 사이의 상태코드를 반환하면 성공으로 간주한다.

## tcpSocket

TCP 체크를 수행하고, 포트가 열려있으면 성공으로 간주한다.

(수행하자마자 포트를 닫아도 성공이다.)

## Types of probe

### livenessProbe

컨테이너가 작동 중인지를 체크한다. 실패할 경우 kubelet이 컨테이너를 죽인다.

> 컨테이너가 스스로 작동 중에 죽을 수 있다면 livenessProbe 를 설정할 필요가 없다. kubelet이 알아서 `restartPolicy`에 따라 재시작 해준다.

> probe가 실패했을 때 곧바로 컨테이너를 죽이고 다시 시작시켜주고 싶다면, livenessProbe를 설정해주고, restartPolicy를 `Always` 나 `OnFailure`로 설정하자.

### readinessProbe

컨테이너가 요청에 응답할 수 있는 상태인지를 체크한다. 실패할 경우 Pod의 Ip 주소를 모든 연관된 Service의 Endpoints 에서 삭제한다.

> probe 가 성공했을 때만 요청을 보내고 싶다면 설정하자.

> 혹은 백엔드 서비스가 포함된 컨테이너일 경우 구현이 제대로 되었는지 확인하는 용도로 쏠 수도 있다.

> 시작 준비 작업이 오래 걸리는 컨테이너라면 아래의 startupProbe를 쓸 수 있지만, 시작 준비 작업 중인지 어플리케이션이 잘못됐는지 확인하고 싶을 때도 readinessProbe 를 쓸 수 있다.

### startupProbe

컨테이너 내부의 어플리케이션이 시작됐는지 체크한다. 이 probe가 작동하면 이 probe가 성공하기 전까지 다른 모든 probe들은 해제된다.

> 시작 준비 작업이 오래 걸릴 경우 설정하자.
