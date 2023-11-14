FROM debian:bullseye

RUN apt-get update && apt install -y apt-transport-https ca-certificates && \
cp /etc/apt/sources.list /etc/apt/sources.list.bak  && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free" > /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free" >> /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free" >> /etc/apt/sources.list && \
echo "deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free" >> /etc/apt/sources.list && \
apt-get clean && apt-get update && apt-get install -y vim iputils-ping telnet

RUN apt-get install -y htop wget sudo tcpdump net-tools openssh-server

ENV USER_NAME=debuguser1
ENV USER_HOME=/home/$USER_NAME
ENV USER_BIN=$USER_HOME/bin
ENV GO_SDK_VER=go1.18.5.linux-amd64

ENV GOROOT=$USER_HOME/go_sdks/go
ENV GOBIN=$USER_HOME/go_env/bin
ENV GOMODCACHE=$USER_HOME/go_env/pkg/mod
ENV GOPATH=$USER_HOME/go_env
ENV PATH=$GOPATH/bin:$GOROOT/bin:$USER_BIN:$PATH

RUN echo 'root:pwroot111' | chpasswd
#RUN useradd -s /bin/bash -p "debuguser1" -m -G sudo debuguser1
RUN useradd -s /bin/bash -m -G sudo $USER_NAME && echo "$USER_NAME:$USER_NAME" | chpasswd

WORKDIR $USER_HOME
USER $USER_NAME

RUN mkdir -p bin go_sdks go_env/bin go_project_mapping out_comp configs

RUN yes | ssh-keygen -f ~/.ssh/id_rsa && touch ~/.ssh/authorized_keys

#RUN cd $USER_HOME/go_sdks && wget -O go1.18.10.tar.gz --no-check-certificate https://golang.google.cn/dl/go1.18.10.linux-amd64.tar.gz && tar -zxvf go1.18.10.tar.gz

COPY ./$GO_SDK_VER.tar.gz $USER_HOME/go_sdks/
COPY ./debug_daemon $USER_BIN/
COPY ./startup.sh $USER_BIN/

RUN tar -zxvf $USER_HOME/go_sdks/$GO_SDK_VER.tar.gz -C $USER_HOME/go_sdks/

#CMD echo "$USER_NAME" |sudo -S service ssh start && $USER_BIN/daemon 
ENTRYPOINT ["startup.sh"]

