## 1. 安装 `kdump` 工具

```bash
sudo apt install -y linux-crashdump kexec-tools
```

## 2. 激活 `kdump`

```bash
kdump-config load
```

查看 `kdump` 状态

```bash
kdump-config show
```

这样， 当 Linux Kernel crash 的时候， 会产生如下的日志，并保存到 `/var/crash` 目录下

可以通过如下命令触发一次挂死来查看产生的 kdump 文件

```bash
echo c > /proc/sysrq-trigger
```


