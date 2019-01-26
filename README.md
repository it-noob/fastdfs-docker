# FastDFS Docker

Usage:

`docker build -t quentinyy/fastdfs-docker:alpine .`
or
`docker pull quentinyy/fastdfs-docker:alpine`

# stand-alone

tracker 127

`docker run -dti --network=host --name tracker -e PORT=22122 -v /var/fdfs/tracker:/var/fdfs quentinyy/fastdfs-docker:alpine tracker`

storage 127

`docker run -dti --network=host --name storage0 -e TRACKER_SERVER=192.168.1.127:22122 -e PORT=23000 -e NGINX_PORT=8080 -v /var/fdfs/storage0:/var/fdfs quentinyy/fastdfs-docker:alpine storage`

# cluster

tracker 127/128

`docker run -dti --network=host --name tracker -e PORT=22122 -v /var/fdfs/tracker:/var/fdfs quentinyy/fastdfs-docker:alpine tracker`

storage group1 127/128

`docker run -dti --network=host --name storage1 -e GROUP_NAME=group1 -e GROUP_COUNT=2 -v /var/fdfs/storage1:/var/fdfs quentinyy/fastdfs-docker:alpine storage 192.168.1.127:22122 192.168.1.128:22122`

storage group2 129/130

`docker run -dti --network=host --name storage2 -e GROUP_NAME=group2 -e GROUP_COUNT=2 -v /var/fdfs/storage2:/var/fdfs quentinyy/fastdfs-docker:alpine storage 192.168.1.127:22122 192.168.1.128:22122`

# monitor

`docker run -ti --network=host --name monitor -e TRACKER_SERVER=192.168.1.127:22122 quentinyy/fastdfs-docker:alpine monitor`

# test

`fdfs_upload_file /etc/fdfs/client.conf test.jpg`

# problem

通过查看 fastdfs 相关资料，docker 的集群环境搭建，由于 storage 的端口在组内配置，只有一个，因此不可以在一台宿主机的 docker 环境里模拟集群，在多台机器的测试中发现，Mac 和 Win 的host网络不生效，查阅官方文档发现如下。目前遗留问题，使用 port 映射端口的情况下，tracker 拿到的 storage 地址是 docker 容器的内网地址

The host networking driver only works on Linux hosts, and is not supported on Docker Desktop for Mac, Docker Desktop for Windows, or Docker EE for Windows Server.

详情，可以点击此处[查看](https://docs.docker.com/network/network-tutorial-host/)
