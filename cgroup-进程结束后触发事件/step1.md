通过资源控制cgroup，可以监控到某个进程结束自动触发事件，可[参考](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/resource_management_guide/sec-common_tunable_parameters#ex-automatically_removing_empty_cgroups
)

## 安装cgroup
`apt install cgroup-tools -y`{{execute}}

## 介绍并修改 notify_on_release && release_agent

注意以下修改均在root group中

### notify_on_release
包含 Boolean 值，1 或者 0，分别可以启动和禁用释放代理的指令。如果 notify_on_release 启用，当 cgroup 不再包含任何任务时（即，cgroup 的 tasks 文件包含 PID，而 PID 被移除，致使文件变空），kernel 会执行 release_agent 文件的内容。通向此空 cgroup 的路径会作为释放代理的参数被提供。
notify_on_release 参数的默认值在 root cgroup 中是 0。所有非 root cgroup 从其父 cgroup 处继承 notify_on_release 的值。

`echo 1 > /sys/fs/cgroup/cpu/notify_on_release`{{execute}}

### release_agent （仅在 root group 中出现）
当 “notify on release” 被触发，它包含要执行的指令。一旦 cgroup 的所有进程被清空，并且 notify_on_release 标记被启用，
kernel 会运行 release_agent 文件中的指令，并且提供通向被清空 cgroup 的相关路径（与 root cgroup 相关）作为参数。例如，释放代理可以用来自动移除空 cgroup，更多信息

```
cat /usr/local/bin/xcc.sh << EOF
#!/bin/sh
rmdir /sys/fs/cgroup/cpu/$1
```{{execute}}
