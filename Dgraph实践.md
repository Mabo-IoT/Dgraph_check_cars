## 安装Dgraph（根据官网&采用docker）

环境：虚拟机centos

#### 安装docker

```shell
sudo yum update
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo systemctl start docker
sudo systemctl enable docker
```

​    ps: 若出现错误：Another app is currently holding the yum lock; waiting     for it to exit... The other application is:

​    输入命令：rm -f /var/run/yum.pid

   ps: 若出现错误：could not change group /var/run/docker.sock to docker: group docker not found

​    输入命令：sudo vim /etc/sysconfig/docker   设置–selinux-enabled=false

   ps :出现错误：虚拟机docker connect: network is unreachable.

​    输入命令： sudo dhclient

#### 安装docker-compose

采用离线安装方式（网路安装方式自行尝试）

```shell
cp  docker-compose-Linux-x86_64 /usr/local/bin
cd /usr/local/bin
mv docker-compose-Linux-x86_64 docker-compose
chmod +x /usr/local/bin/docker-compose
```

#### 安装Dgraph

```shell
docker pull dgraph/dgraph
```

#### 运行Dgraph

编写docker-compose.yml

```yml
version: "3.2"
services:
  zero:
    image: dgraph/dgraph:latest
    volumes:
      - type: volume
        source: dgraph
        target: /dgraph
        volume:
          nocopy: true
    ports:
      - 5080:5080
      - 6080:6080
    restart: on-failure
    command: dgraph zero --my=zero:5080
  server:
    image: dgraph/dgraph:latest
    volumes:
      - type: volume
        source: dgraph
        target: /dgraph
        volume:
          nocopy: true
    ports:
      - 8080:8080
      - 9080:9080
    restart: on-failure
    command: dgraph alpha --my=server:7080 --lru_mb=2048 --zero=zero:5080
  ratel:
    image: dgraph/dgraph:latest
    volumes:
      - type: volume
        source: dgraph
        target: /dgraph
        volume:
          nocopy: true
    ports:
      - 8000:8000
    command: dgraph-ratel

volumes:
  dgraph:
```

运行：

```
docker-compose up -d
```

