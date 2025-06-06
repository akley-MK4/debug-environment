FROM debian:bullseye

RUN apt-get update && apt install -y apt-transport-https ca-certificates && \
cp /etc/apt/sources.list /etc/apt/sources.list.bak  && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free" > /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free" >> /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free" >> /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free" >> /etc/apt/sources.list && \
apt-get clean && apt-get update

RUN apt-get install -y vim iputils-ping htop wget sudo curl tcpdump net-tools openssh-server libpcap-dev libbpf-dev gcc git jq

ENV USER_NAME=op1
ENV USER_HOME=/home/$USER_NAME
ENV USER_BIN=$USER_HOME/bin
ENV GO_SDK_VER=go1.19.13.linux-amd64

ENV GOROOT=$USER_HOME/go_sdks/go
ENV GOBIN=$USER_HOME/go_env/bin
ENV GOMODCACHE=$USER_HOME/go_env/pkg/mod
ENV GOPATH=$USER_HOME/go_env
ENV PATH=$GOPATH/bin:$GOROOT/bin:$USER_BIN:$PATH
ENV USER_PWD=pw${USER_NAME}111

RUN echo 'root:pwroot111' | chpasswd
#RUN useradd -s /bin/bash -p "debuguser1" -m -G sudo debuguser1
RUN useradd -s /bin/bash -m -G sudo $USER_NAME && echo "${USER_NAME}:${USER_PWD}" | chpasswd
RUN sed -i "s/^#PermitRootLogin.*/PermitRootLogin yes/g" /etc/ssh/sshd_config

WORKDIR $USER_HOME
USER $USER_NAME

RUN echo "export GOROOT=${GOROOT}" >> ~/.bashrc && \
echo "export GOBIN=${GOBIN}" >> ~/.bashrc && \
echo "export GOMODCACHE=${GOMODCACHE}" >> ~/.bashrc && \
echo "export GOPATH=${GOPATH}" >> ~/.bashrc && \
echo "export PATH=$GOPATH/bin:$GOROOT/bin:$USER_BIN:$PATH" >> ~/.bashrc

RUN git config --global http.sslVerify false

RUN mkdir -p bin go_sdks go_env/bin go_project_mapping go_project_local out_comp configs daemon-app-src

RUN yes | ssh-keygen -f ~/.ssh/id_rsa && touch ~/.ssh/authorized_keys

#RUN cd $USER_HOME/go_sdks && wget -O go1.18.10.tar.gz --no-check-certificate https://golang.google.cn/dl/go1.18.10.linux-amd64.tar.gz && tar -zxvf go1.18.10.tar.gz
COPY ./$GO_SDK_VER.tar.gz $USER_HOME/go_sdks/
COPY ./daemon-app-src/* $USER_HOME/daemon-app-src/
COPY ./startup.sh $USER_BIN/

RUN tar -zxvf $USER_HOME/go_sdks/$GO_SDK_VER.tar.gz -C $USER_HOME/go_sdks/

RUN go install github.com/go-delve/delve/cmd/dlv@latest

RUN go build -ldflags="-s -w" -o $USER_BIN/daemon-app daemon-app-src/main.go
RUN echo "${USER_PWD}" | sudo -S chown -R $USER_NAME:$USER_NAME $USER_BIN/startup.sh && chmod +x $USER_BIN/startup.sh
#CMD echo "$USER_NAME" |sudo -S service ssh start && $USER_BIN/daemon 
ENTRYPOINT ["startup.sh"]

