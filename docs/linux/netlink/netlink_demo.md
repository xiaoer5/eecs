以下是一个完整的Netlink示例，包括用户空间应用程序和内核模块。该示例展示了如何通过Netlink实现内核与用户空间的双向通信。

## 内核模块代码

内核模块负责接收用户空间发送的消息，并向用户空间回复一条消息。

代码：`netlink_kernel.c`

```c
#include <linux/module.h>
#include <linux/netlink.h>
#include <linux/skbuff.h>
#include <net/sock.h>
#include <linux/init.h>
#include <linux/kernel.h>

#define NETLINK_USER 31

static struct sock *nl_sk = NULL;

// 接收用户空间消息的回调函数
static void netlink_recv_msg(struct sk_buff *skb) {
    struct nlmsghdr *nlh;
    struct sk_buff *skb_out;
    char *reply_msg = "Hello from kernel";
    int msg_size;
    int res;
    int pid;

    // 获取Netlink消息头
    nlh = nlmsg_hdr(skb);
    pid = nlh->nlmsg_pid; // 用户空间进程的PID

    // 打印接收到的消息
    pr_info("Received message from user space: %s\n", (char *)nlmsg_data(nlh));

    // 构造回复消息
    msg_size = strlen(reply_msg) + 1;
    skb_out = nlmsg_new(msg_size, GFP_KERNEL);
    if (!skb_out) {
        pr_err("Failed to allocate skb_out\n");
        return;
    }

    // 填充Netlink消息头
    nlh = nlmsg_put(skb_out, 0, 0, NLMSG_DONE, msg_size, 0);
    if (!nlh) {
        pr_err("Failed to create nlmsg\n");
        kfree_skb(skb_out);
        return;
    }

    // 填充消息体
    memcpy(nlmsg_data(nlh), reply_msg, msg_size);

    // 发送回复消息到用户空间
    res = nlmsg_unicast(nl_sk, skb_out, pid);
    if (res < 0) {
        pr_err("Failed to send netlink message\n");
    }
}

// 初始化模块
static int __init netlink_init(void) {
    struct netlink_kernel_cfg cfg = {
        .input = netlink_recv_msg, // 注册回调函数
    };

    // 创建Netlink套接字
    nl_sk = netlink_kernel_create(&init_net, NETLINK_USER, &cfg);
    if (!nl_sk) {
        pr_err("Failed to create netlink socket\n");
        return -ENOMEM;
    }

    pr_info("Netlink kernel module loaded\n");
    return 0;
}

// 卸载模块
static void __exit netlink_exit(void) {
    if (nl_sk) {
        netlink_kernel_release(nl_sk);
    }
    pr_info("Netlink kernel module unloaded\n");
}

module_init(netlink_init);
module_exit(netlink_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("Netlink Kernel Module Example");

```

## 用户空间应用程序代码

用户空间程序负责向内核发送消息，并接收内核的回复。

代码：`netlink_user.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <linux/netlink.h>

#define NETLINK_USER 31
#define MAX_PAYLOAD 1024

int main() {
    struct sockaddr_nl src_addr, dest_addr;
    struct nlmsghdr *nlh = NULL;
    struct iovec iov;
    struct msghdr msg;
    int sock_fd;
    char *message = "Hello from user space";

    // 创建Netlink套接字
    sock_fd = socket(AF_NETLINK, SOCK_RAW, NETLINK_USER);
    if (sock_fd < 0) {
        perror("socket");
        return -1;
    }

    // 绑定源地址
    memset(&src_addr, 0, sizeof(src_addr));
    src_addr.nl_family = AF_NETLINK;
    src_addr.nl_pid = getpid(); // 使用进程ID作为端口ID

    if (bind(sock_fd, (struct sockaddr *)&src_addr, sizeof(src_addr)) {
        perror("bind");
        close(sock_fd);
        return -1;
    }

    // 设置目标地址
    memset(&dest_addr, 0, sizeof(dest_addr));
    dest_addr.nl_family = AF_NETLINK;
    dest_addr.nl_pid = 0; // 发送到内核
    dest_addr.nl_groups = 0; // 单播

    // 构造Netlink消息
    nlh = (struct nlmsghdr *)malloc(NLMSG_SPACE(MAX_PAYLOAD));
    memset(nlh, 0, NLMSG_SPACE(MAX_PAYLOAD));
    nlh->nlmsg_len = NLMSG_SPACE(MAX_PAYLOAD);
    nlh->nlmsg_pid = getpid();
    nlh->nlmsg_flags = 0;

    // 填充消息体
    strcpy(NLMSG_DATA(nlh), message);

    // 设置消息结构
    iov.iov_base = (void *)nlh;
    iov.iov_len = nlh->nlmsg_len;
    msg.msg_name = (void *)&dest_addr;
    msg.msg_namelen = sizeof(dest_addr);
    msg.msg_iov = &iov;
    msg.msg_iovlen = 1;

    // 发送消息到内核
    printf("Sending message to kernel: %s\n", message);
    if (sendmsg(sock_fd, &msg, 0) < 0) {
        perror("sendmsg");
        close(sock_fd);
        free(nlh);
        return -1;
    }

    // 接收内核的回复
    printf("Waiting for message from kernel...\n");
    if (recvmsg(sock_fd, &msg, 0) < 0) {
        perror("recvmsg");
        close(sock_fd);
        free(nlh);
        return -1;
    }

    // 打印接收到的消息
    printf("Received message from kernel: %s\n", (char *)NLMSG_DATA(nlh));

    // 清理
    close(sock_fd);
    free(nlh);
    return 0;
}
```

## 编译和运行

### 编译内核模块

将内核模块代码保存为netlink_kernel.c

编写Makefile：

```makefile
obj-m += netlink_kernel.o
all:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

编译模块：

```bash
make
```

加载模块：

```bash
sudo insmod netlink_kernel.ko
```

### 编译用户空间程序

将用户空间程序代码保存为netlink_user.c

编译程序：

```bash
gcc -o netlink_user netlink_user.c
```

### 运行程序

运行用户空间程序：

```bash
./netlink_user
```

查看内核日志：

```bash
dmesg
```

## 运行结果

用户空间输出：

```bash
Sending message to kernel: Hello from user space
Waiting for message from kernel...
Received message from kernel: Hello from kernel
```

内核日志：

```bash
[  123.456789] Netlink kernel module loaded
[  123.456790] Received message from user space: Hello from user space
```

## 总结

内核模块通过netlink_kernel_create()创建Netlink套接字，并注册回调函数处理用户空间消息。

用户空间程序通过socket()创建Netlink套接字，并通过sendmsg()和recvmsg()与内核通信。

该示例展示了Netlink的双向通信机制，适用于内核与用户空间的高效数据交换。