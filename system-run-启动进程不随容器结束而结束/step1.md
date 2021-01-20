systemd-run 指令用于创建、启动临时 service 或 scope 单位，并在此单位中运行自定义指令。
在 service 单位中执行的指令在后台非同步启动，它们从 systemd 进程中被调用。
在 scope 单位中运行的指令直接从 systemd-run 进程中启动，因此从调用方继承执行状态。
此情况下的执行是同步的。

## 创建运行脚本

```
cat << EOF > /root/start.sh
#!/bin/sh
systemd-run --scope --slice=system sleep 3610 &
sleep 3615 &
sleep 3620
EOF
```{{execute}}

修改脚本权限
`chmod +x /root/start.sh`{{execute}}

## 运行容器

`docker run -d -i -v /sys/fs/cgroup:/sys/fs/cgroup -v /root/start.sh:/root/start.sh -v /run/dbus/system_bus_socket:/run/dbus/system_bus_socket:ro --ipc=host --network=host --pid=host --name test --privileged docker.io/openstackhelm/libvirt:latest-ubuntu_bionic /root/start.sh`{{execute}}

## 查看结果

```
ps -ef | grep sleep
docker rm -f  test
ps -ef | grep sleep
```{{execute}}

从以上结果可以看到，用system-run跑的sleep并没有被kill
