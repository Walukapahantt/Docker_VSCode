FROM ubuntu:latest

# 필요한 패키지 설치
RUN apt-get update && \
    apt-get install -y curl sudo && \
    curl -fsSL https://code-server.dev/install.sh | sh

# code-server를 위한 환경 변수 설정
ENV USER="mirero" \
    PASSWORD="system" \
    PORT=8080 \
    EXTENSIONS="ms-python.python" \
    WORKINGDIR="vscode"

# 새로운 사용자 생성 및 비밀번호 설정
RUN useradd -m ${USER} && echo "${USER}:${PASSWORD}" | chpasswd && adduser ${USER} sudo

# 폴더 생성
RUN mkdir "/home/${USER}/${WORKINGDIR}"

# code-server를 위한 포트 노출
EXPOSE ${PORT}

# code-server 시작
ENTRYPOINT ["nohup", "code-server", "--bind-addr", "0.0.0.0:8080", "--auth", "password", "/home"]

# docker build -t vscode-docker .
# docker run -it --name vscode-container -p 8080:8080 vscode-docker
