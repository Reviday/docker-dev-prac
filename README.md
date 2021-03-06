# docker-dev-prac
도커로 개발 환경 세팅 연습



### docker-networks

**************************************************

**bridge**: 도커의 각 컨테이너에 연결하는 네트워크 구조로, 컨테이너는 연결된 브리지를 통해 **외부**와 통신할 수 있습니다.

```
  $ docker network create --driver bridge saasbot/docker
```



### docker-volume

**************************************************

**volume**: Docker 데이터 볼륨은 데이터를 컨테이너가 아닌 호스트에 저장하는 방식입니다. 따라서 데이터볼륨은 컨테이너끼리 데이터를 **공유**할 때 활용할 수 있습니다.

해당 디렉터리에서는 **volume**을 **.env** 파일에서 Docker 내에서 사용될 **환경변수**로 관리하고 있습니다. 아래와 같이 작성하면 됩니다.

```yaml
SEARCH_VOLUME=/볼륨을 연결할 디렉터리 경로
LOGS_VOLUME=/볼륨을 연결할 디렉터리 경로
```

만약, 실행 중에 **PermissionDenied**, **AccessDenied** 관련 에러가 발생한다면 해당 디렉터리의 **권한**을 조절해야 합니다(되도록 미리 설정해놓는 편이 좋습니다). 해당 디렉터리로 이동하여 아래 과정을 수행합니다.

```
$ chmod 777 볼륨을 연결할 디렉터리
$ chgrp 1000 볼륨을 연결할 디렉터리
```





## Error History

1. **Read timed out**

```
ERROR: for docker-dev-prac_es_logs_1  UnixHTTPConnectionPool(host='localhost', port=None): Read timed out. (read timeout=70)
ERROR: An HTTP request took too long to complete. Retry with --verbose to obtain debug information.
```

- **Solution**(근본적인 해결방법은 아닙니다.)

  ```
  # Restarting docker service:
  $ sudo systemctl restart docker
  
  # and setting DOCKER_CLIENT_TIMEOUT and COMPOSE_HTTP_TIMEOUT environment variables:
  $ export DOCKER_CLIENT_TIMEOUT=120
  $ export COMPOSE_HTTP_TIMEOUT=120
  ```



2. **Docker Container exited with code 137**

```
container_name exited with code 137
```

- **Solution**

  : 주로 할당 된 Memory 부족으로 발생되는 에러로, 아래와 같이 docker-compose.yml에 적절히 할당해 주면 해결됩니다.

  ```yaml
  deploy:
  	resources:
    	limits:
      	cpus: '2.00'
        memory: 4096M
      reservations:
      	cpus: '1.00'
        memory: 1024M
  ```

  

