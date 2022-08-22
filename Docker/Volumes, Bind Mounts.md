# Volumes, Bind Mounts

### Application

- 개발자에 의해 작성된 코드, 데이터
- 빌드 단계에서 코드가 이미지에 복사
- 컨테이너는 제공된 환경에서 코드를 사용하고, 빌드 후 코드는 수정될 수 없다.
- 읽기 전용

### Temporary App Data

- 애플리케이션이 실행될 때 생성된 임시 데이터
- 메모리나 파일에 저장될 수 있음
- 컨테이너가 종료될 때 삭제되어도 상관 없는 데이터
- 읽기 및 수정 가능

### Permanent App Data

- 영구 애플리케이션 데이터, 예) 계정 데이터
- 데이터베이스에 저장되어야하 데이터
- 컨테이너가 제거되어도 삭제되면 안된다.
- 읽기 및 수정 가능, 영구적으로 저장해야되기 때문에 볼륨의 도움을 받아야 한다.

### Volumes

- 볼륨에는 익명 볼륨과 명명된 볼륨이 있다.
- 볼륨은 `docker volume ls`로 확인할 수 있다.
- Anonymous Volumn
    - Dockerfile → `VOLUME [컨테이너 내부 위치]`
- Named Volume
    - 중요한 데이터, 편집할 필요가 없거나 중요한 데이터에 적합하다.
    - 컨테이너를 실행할 때 -v 옵션을 이용하여 사용할 수 있다.
    - 예) `docker run -p 3000:80 -d --name feedback-app -v feedback:/app/feedback --rm feedback-node`

### 익명 볼륨 제거

- --rm 옵션으로 컨테이너를 실행한다면 익명 볼륨이 자동으로 제거된다.
- 하지만 옵션 없이 컨테이너를 실행한다면 제거되지 않고 익명볼륨이 쌓인다.
- `docker volume rm VOL_NAME` 또는 `docker volume prune`을 통해 삭제할 수 있다.

### 바인드 마운트

- 볼륨을 직접 관리할 수 있다.
- 영구적이고 편집가능한 데이터에 적합하다.
- 바인드마운트를 사용할 때 Preferences → Resources → FILE SHARING에 마운트하려는 상위 폴더가 있어야지 바인드 마운트를 설정할 수 있다.
- shortcuts macOS / Linux: `-v $(pwd):/app`

### 다른 모듈과 병합하기

- `docker run -p 3000:80 -d --name feedback-app -v feedback:/app/feedback -v -v $(pwd):/app --rm feedback-node`
- 현재 node.js 애플리케이션을 도커로 띄웠고 이 경우 npm i로 설치한 종속성에 대한 내용이 덮어씌워지게 되면서 애플리케이션이 실행이 되지 않는다.
- 익명 볼륨을 이용해서 이 문제를 해결할 수 있다.
    - 조금 더 긴 경로가 우선시 되기 때문에 /app/node_modules 경로를 익명 볼륨을 사용하도록 한다.
    - 바인드 마운트한 /app보다 익명 볼륨을 사용한 /app/node_modules 우선
- `docker run -p 3000:80 -d --name feedback-app -v feedback:/app/feedback -v -v $(pwd):/app -v /app/node_modules --rm feedback-node`
- 익명 볼륨은 외부 경로보다 컨테이너 내부 경로의 우선 순위를 높이는데 사용할 수 있다.

### Volumns & BindMounts 사용 정리

- Anonymous Volume
    - `docker run -v /app/data …`
- Named Volume
    - `docker run -v data:/app/data …`
- Bind Mount
    - `docker run -v 절대경로/app/data …`
    - `docker run -v $(pwd):/app/data …`

### 읽기 전용 볼륨

- Read Only의 약자인 :ro를 뒤에 붙여주면 된다.
- `docker run -v $(pwd):/app/data:ro …`

### volume

- 볼륨 생성
    - `docker volume create`
- 볼륨 정보
    - `docker volume inspect 볼륨명`

### 바인드마운트 대신 Dockerfile에서 COPY를 사용하는 이유

- 스냅샷을 유지하기 위해

### dockerignore

- `.dockerignore` 파일을 생성하여 복사하지 않을 파일이나 폴더를 설정할 수 있다.

### env

- DOCKER FILE에서 환경변수를 사용한다는것을 알림 또한 기본값을 설정할 수 있음

```docker
# ENV로 환경변수 설정 가능
ENV PORT 80

EXPOSE $PORT
```

- `--env-file .env`
- `-e PORT=8000`
- `--env PORT=8000`

### Arguments

```docker
# 이미지 사용
FROM node:14

# Working Directory
WORKDIR /app

# 첫 번째는 컨테이너 외부, 두 번째는 컨테이너 내부
COPY package.json .

# 내부에서 명령어 실행
# 명령어는 보통 루트에서 실행됨 그래서 WORKDIR을 설정해야 함
RUN npm install

COPY . .

# Argument 런타임 명령
ARG DEFAULT_PORT=80

# ENV로 환경변수 설정 가능
ENV PORT $DEFAULT_PORT

# 포트
# Documentation
EXPOSE $POST

# VOLUME 컨테이너 내부 위치
# VOLUME ["/app/feedback"]

# 서버 시작
CMD ["npm", "start"]
```

- 위와 같이 설정한다면 빌드시에 유연하게 이미지를 생성할 수 있다.
- 빌드 매개변수 추가하는 경우 기본적으로 8000번 포트 사용
    - `docker build -t feedback-node --build-arg DEFAULT_PORT=8000 .`
- 그냥 빌드하는 경우 80번 포트 사용
    - `docker build -t feedback-node .`