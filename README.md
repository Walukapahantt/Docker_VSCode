문서정보 : 2023.04.27. 작성, 작성자 [@SAgiKPJH](https://github.com/SAgiKPJH)

<br>

# Docker_VSCode_Server
VSCode Server를 Docker로 돌립니다.

### 목표
- [x] : VSCode Server를 내포하는 Dockerfile을 구성합니다.

### 제작자
[@SAgiKPJH](https://github.com/SAgiKPJH)

<br><br>

---

<br><br>

# VSCode Server를 내포하는 Dockerfile을 구성합니다.

다음 조건을 만족하는 VSCode Server Docker를 구성합니다.  
1. vscode server 아이디 사전에 설정 가능
2. vscode server 비밀번호 사전에 설정 가능
3. 확장 설치 설정 가능
4. 원하는 포트에 서비스 가능
5. vscode server 이미 실행되어 있게 구성

<br>

### Dockerfile 구성

다음과 같이 dockerfile을 구성합니다.  
이때, code-server를 백그라운드에서 실행하기 위해 `nohup` 명령어를 활용하였습니다.

```dockerfile
FROM ubuntu:latest

# 필요한 패키지 설치
RUN apt-get update && \
    apt-get install -y curl sudo && \
    curl -fsSL https://code-server.dev/install.sh | sh

# code-server를 위한 환경 변수 설정
ENV USER="mirero" \
    PASSWORD="system" \
    PORT=8080 \
    WORKINGDIR="vscode"

# 새로운 사용자 생성 및 비밀번호 설정
RUN useradd -m ${USER} && echo "${USER}:${PASSWORD}" | chpasswd && adduser ${USER} sudo

# 폴더 생성
RUN mkdir "/home/${USER}/${WORKINGDIR}"

# code-server를 위한 포트 노출
EXPOSE ${PORT}

# 원하는 확장 설치
RUN code-server --install-extension "ms-python.python" --install-extension "ms-azuretools.vscode-docker"

# code-server 시작
ENTRYPOINT ["nohup", "code-server", "--bind-addr", "0.0.0.0:8080", "--auth", "password", "/home"]

# nohup code-server --bind-addr 0.0.0.0:8080 --auth password "/home/mirero" &
# docker build -t vscode-docker .
# docker run -it --name vscode-container -p 8080:8080 vscode-docker
```
  
설치하고자 하는 확장은 https://marketplace.visualstudio.com/items?itemName=ms-python.python 사이트에 들어가 `Unique Identifier`항목을 찾아 입력합니다.  
<img src="https://user-images.githubusercontent.com/66783849/234725217-4081ba0a-bd39-4944-86db-afce20b1227f.png">  
<img src="https://user-images.githubusercontent.com/66783849/234725243-686def18-71a5-4319-85f4-2af2c357c8bb.png">  


<br>

### dockerfile 빌드
`vscode-docker`라는 이름의 Docker Image를 만듭니다.  
빌드가 오래걸릴경우, `--progress=plain`옵션을 추가하여 과정을 텍스트로 출력합니다.  
```bash
docker build -t vscode-docker .

docker build --progress=plain -t vscode-docker .
```
<img src="https://user-images.githubusercontent.com/66783849/234724445-877ebefd-96bf-471e-890e-4fe7df0bb44f.png">  

<br>

### docker 실행 후 검증

```bash
docker run -it --name vscode-container -p 8080:8080 vscode-docker
```
다음과 같이 접속 가능함을 확인할 수 있습니다.  
<img src="https://user-images.githubusercontent.com/66783849/236122610-ee1992db-73e6-49cc-923e-87f30581f3b6.png"/>  
<img src="https://user-images.githubusercontent.com/66783849/236123909-619f4c79-cea9-49f0-866c-8304a5b78476.png"/>  

다음과 같이 확장을 확인할 수 있습니다.  
<img src="https://user-images.githubusercontent.com/66783849/236140752-2cd56f80-8d89-4c29-9261-8219288c767c.png"/>  
