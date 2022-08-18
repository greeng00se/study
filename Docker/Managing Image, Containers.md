# Managing Image, Containers

### Attatched Mode

- `dokcer run`의 default mode
- 실행한 컨테이너를 계속 확인할 수 있다.

### Detached Mode

- `docker start`의 default mode
- `docker attatch NAMES` 으로 컨테이너 확인 가능

### 상호작용

- docker run으로 하면 attached mode가 되지만 상호작용은 할 수 없다.
- iteractive (-i) tty (-t) flag를 사용해야 한다.
- ex) `docker run -it 5504de66f4f1dd`
- 기존 컨테이너를 다시 시작(start)하려면 -i 옵션을 붙여야 한다.
- 이 때 터미널은 처음에 -t 옵션을 사용했기 때문에 안붙여도 된다.
- 또한 Attatched Mode로 실행해야 되기 때문에 -a 옵션을 사용해야 한다.
- ex) `docker start -ai upbean_pascal`

### 이미지 컨테이너 삭제

- 컨테이너 삭제
    - `docker rm`
- 이미지 삭제
    - `docker rmi`
- 컨테이너가 사용하지 않는 이미지 제거
    - `docker image prune`
- 중지된 컨테이너를 자동 제거한다.(`--rm`)
    - `docker run -it --rm 5504`

### 이미지 정보 찾기

- 이미지 정보 확인 (`inspect`)
    - `docker image inspect IMAGE_ID`

### 파일 복사

- 로컬 → 컨테이너
    - `docker cp dummy/. sharp_wozniak:/test`
- 컨테이너 → 로컬
    - `docker cp sharp_wozniak:/test dummy/test`

### 이미지와 컨테이너의 이름, 태그 지정

- 컨테이너 이름 지정 (--name 사용)
    - `docker run -p 3000:80 -d --rm --name goalsapp 775fe71a6`
- 이미지의 이름은 name:tag로 구분된다.
    - 이름은 일반적인 이름을 의미한다.
    - tag로 특정화된 버전을 표현할 수 있다.
- 빌드 시 이름과 태그 지정(-t 옵션)
    - `docker build -t goals:latest .`
    

### 이미지 공유

- Docker Hub, Private Registry
- 공유
    - `docker push id/reponame:tag`
- 이미지 복사, 이름과 태그 변경
    - `docker tag goals:latest greeng00se/node-hello-world`
- 배포하려면 생성한 레포와 도커 이미지의 이름이 동일해야한다.
- 또한 권한이 필요하기 때문에 다음과 같이 로그인해야 한다.
    - `docker login`