### systemctl

> Systemctl接受服务（.service），挂载点（.mount），套接口（.socket）和设备（.device）作为单元。

1、服务状态

```shell
systemctl status  network.service # 服务状态
```

2、服务启停

```shell
systemctl enable     network.service # 启用服务自启动
systemctl disable    network.service # 禁用服务自启动
systemctl is-enabled network.service # 检查是否自启动
systemctl start      network.service # 启动服务
systemctl restart    network.service # 重启服务
systemctl stop       network.service # 停止服务
```

3、查看开机启动项

> 列出所有服务、某个服务、已启动的服务
>
> 在过去，你能很容易通过查看 /etc/init.d 了解到哪些服务进程会在引导时启动。Systemd 以不同的方式展现，你可以使用如下命令罗列允许开机启动的服务进程。

```shell
systemctl list-unit-files --type=service
systemctl list-unit-files --type=service | grep enabled
```

（待补充...）