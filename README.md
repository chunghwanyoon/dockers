# DOCKER 정리

# 파일
- Dockerfile
- .dockerignore
  - .dockerignore에 node_modules, npm-debug.log 등
 
# Dockerfile
- example
```.Docker
# 어떤 이미지로부터 새로운 이미지를 생성할지 지정
FROM node:alpine

LABEL maintainer = "chunghwanyoon <email@then.co.kr>

# /app 디렉토리 생성
# REACT APP 생성시 client안에 들어이있는게 아니라면 필요함
RUN mkdir -p /app

# /app 디렉토리를 WORKDIR로 설정
WORKDIR /app

# 현재 Dockerfile경로에 있는 모든 파일을 /app에 복사
ADD . /app

# Dependencies
# package.json, package-lock.json (npn@5+부터 한번에)
COPY package*.json ./

# npm install 실행
RUN npm install

# 환경변수 NODE_ENV의 값을 development로 설정
ENV NODE_ENV development

# 가상 머신에 오픈할 포트 3000:development, 8080:production
EXPOSE 3000 80

# 컨테이너에서 실행될 명령을 지정
CMD ["npm", "start"]
```

# Docker commands
- docker images: 이미지 확인
- docker ps: running중인 컨테이너 확인
- docker rm <container id>: 컨테이너 삭제
- docker rmi <image id>: 이미지 삭제
  - docker -f rmi, rm으로 force 삭제 가능
  
- docker build --tag name:tag .: 현재 디렉토리를 도커에 빌드
- docker run --name <name> -p 8080:3000 <name>:<tag>: Dockerfile에 적어놓은 포트와 로컬 연결
  
- docker stop $(docker ps -a -q): 구동중인 모든 컨테이너 정지
- docker rm $(docker ps -a -q): 모든 컨테이너 삭제
- docker rmi $(docker images -q): 모든 이미지 삭제


  
