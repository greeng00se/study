# Docker Images, Containers

### Images vs Contrainers

- 이미지는 템플릿, 청사진 역할을 한다.
- 컨테이너는 실행 애플리케이션이다.

### Dockerfile

```docker
# 이미지 사용
FROM node

# Working Directory
WORKDIR /app

# 첫 번째는 컨테이너 외부, 두 번째는 컨테이너 내부
COPY . /app

# 내부에서 명령어 실행
# 명령어는 보통 루트에서 실행됨 그래서 WORKDIR을 설정해야 함
RUN npm install

# 포트
# Documentation
EXPOSE 80

# 서버 시작
CMD ["node", "server.js"]
```

- 도커파일 빌드
    - `docker build 도커파일위치`
- 실행(Bad)
    - `docker run IMAGEID`
    - 위와 같이하면 포트에 접근하지 못함
- 실행(Good)
    - `docker run -p ACCESSPORT:EXPOSEPORT IMAGEID`
    - ex) `docker run -p 3000:80 4daf5a0cd2a0`
- 확인 및 종료
    - 확인 → `docker ps`
    - 종료 → `docker stop CONTAINERNAMES`

### 이미지 레이어

- 이미지의 모든 명령은 캐시 가능한 레이어를 생성한다.
- 레이어는 이미지 재구축을 및 공유를 도와준다.

```docker
# 이미지 사용
FROM node

# Working Directory
WORKDIR /app

# 첫 번째는 컨테이너 외부, 두 번째는 컨테이너 내부
COPY package.json /app

# 내부에서 명령어 실행
# 명령어는 보통 루트에서 실행됨 그래서 WORKDIR을 설정해야 함
RUN npm install

# 여기까진 변경이 없었으니 재사용 (최적화)

COPY . /app

# 포트
# Documentation
EXPOSE 80

# 서버 시작
CMD ["node", "server.js"]
```

- 캐시된다는 점을 이용해 명령어 최적화