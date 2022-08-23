# 자주 사용하는 Docker 명령어 정리

### HELP

- `docker --help`

### 빌드

- `docker build .`
- 이름과 태그 추가
    - `-t NAME:TAG`

### 확인

- 이미지 확인
    - `docker images`
- 컨테이너 확인
    - `docker ps`
- 실행 종료된 컨테이너 까지 확인
    - `docker ps -a`

### 이미지 실행

- `docker run IMAGE_NAME`
- 컨테이너에 이름 명시(명시하지 않으면 자동 생성)
    - `--name NAME`
- detached mode로 실행 옵션
    - `-d`
- interactive mode로 실행 옵션
    - `-it`
- 종료시 자동으로 컨테이너 제거 옵션
    - `--rm`

### 컨테이너 종료와 재시작

- 컨테이너 종료
    - `docker stop NAME`
- 종료한 기존 컨테이너 재시작
    - `docker start`

### 제거

- 이미지 제거
    - `docker rmi IMAGE`
- 컨테이너 제거
    - `docker rm CONTAINER`
- 컨테이너가 사용하지 않는 이미지 제거
    - `docker image prune`

### 파일 복사

- 로컬 → 컨테이너
    - `docker cp dummy/. sharp_wozniak:/test`
- 컨테이너 → 로컬
    - `docker cp sharp_wozniak:/test dummy/test`

### 이미지 정보 확인

- `docker image inspect IMAGE_ID`

### 이미지 이름과 태그 변경

- `docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`

### DockerHub

- 이미지 가져오기
    - `docker pull IMAGE`
- 이미지 배포하기
    - `docker push IMAGE`