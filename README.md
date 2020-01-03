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


  
# docker-compose.yml
React App과 Nodejs서버를 같은 디렉토리에 넣은 후, 각 서비스별 Dockerfile은 그대로 두고 상위 디렉토리에 docker-compose.yml을 추가하고 코드 작성한다.
```
APP
 |-- client(react app)
 |-- server(nodejs server)
 | .gitignore
 | .dockerignore
 | docker-compose.yml
 | .env
 | README.md
```
빌드시에는 `docker-compose up --build`

최상위폴더 APP의 .env에는 docker-compose.yml에 들어갈 환경변수 설정
docker-compose.yml의 예시는 다음과 같다.
```
version: "3"

services:
  ########################
  # setup node container #
  ########################
  server:
    build: ./server
    expose:
      - ${APP_SERVER_PORT}
    environment:
      API_HOST: ${API_HOST}
      APP_SERVER_PORT: ${APP_SERVER_PORT}
    ports:
      - ${APP_SERVER_PORT}:${APP_SERVER_PORT}
    volumes:
      - ./server/api:/app/api
      - ./server/models:/app/models
    command: node app.js

  #########################
  # setup react container #
  #########################
  client:
    build: ./client
    environment:
      - REACT_APP_PORT=${REACT_APP_PORT}
    expose:
      - ${REACT_APP_PORT}
    ports:
      - ${REACT_APP_PORT}:${REACT_APP_PORT}
    volumes:
      - ./client/src:/app/src
      - ./client/public:/app/public
    links:
      - server
    command: npm start
```
볼륨 설정시 path:/app/path의 코드가 변하면 자동으로 도커에 포함된다. 예를들면 Nodejs서버의 src, models와 React App의 public, src
