# Linux内核参数管理

*Linux* 内核有很多可以定制化的参数 —— **内核参数** ( *kernel parameters* )， 斟酌设置内核参数对 **系统调优** 意义重大。 **内核参数** 涵盖内核的方方面面，包括 **网络** ( *net* )、 **文件系统** ( *fs* )等等。

本文以 `fs.file-max` 参数为例，介绍设置内核参数的几种方式。

## procfs

*Linux* 内核通过 *procfs* 伪文件系统暴露 **内核参数** 的访问接口， 通过 *procfs* 接口，用户可 **设置** 或者 **查看** 指定内核参数。 内核参数位于 */proc/sys* 目录下，以 `fs.file-max` 参数为例，路径为 `/proc/sys/fs/file-max` 。

查看参数只需读取对应文件内容：

```sh
$ cat /proc/sys/fs/file-max
94524
```

同样地，设置参数只需将值写入对应文件：

```sh
$ echo 1000000 > /proc/sys/fs/file-max
```

> **注解:**
> 通过 *procfs* 调整内核参数可以 **立即生效** ，但 **重启失效**

## sysctl

*Linux* 内置 *sysctl* 命令用于管理内核参数，用法非常直观。

查看给定内核参数值：

```sh
$ sysctl fs.file-max
fs.file-max = 94524
```

如果只需返回参数值，可以指定 *-n* 参数：

```sh
$ sysctl -n fs.file-max
94524
```

设置给定内核参数：

```sh
$ sysctl -w fs.file-max=1000000
fs.file-max = 1000000
```

> **注解:**
> 由于 *sysctl* 命令底层通过 *procfs* 接口与内核交互，因此效果与 *procfs* 类似 —— **立即生效，重启失效** 。

## 配置文件

*procfs* 只是访问内核参数的接口，参数设置 **持久化** 仍需借助 **配置文件** 实现。

用户可将内核参数设置写在一个文件里：

```sh
$ cat /some/path/to/sysctl.conf
fs.file-max = 1000000
```

然后，运行 *sysctl* 命令加载配置文件以便生效：

```sh
$ sysctl -p /some/path/to/sysctl.conf
```

如果系统重启，再次运行以上命令即可重新设置内核参数。

## 系统配置文件

*Linux* 系统启动后，默认从以下 **系统配置文件** 加载内核参数：

```sh
/run/sysctl.d/*.conf
/etc/sysctl.d/*.conf
/usr/local/lib/sysctl.d/*.conf
/usr/lib/sysctl.d/*.conf
/lib/sysctl.d/*.conf
/etc/sysctl.conf
```

因此，更推荐将内核参数设置写到这些 **系统配置文件** 中。

系统配置调整后，需要重启系统或者运行以下 *sysctl* 命令方能生效：

```sh
$ sysctl --system
```

如果只改动 */etc/sysctl.conf* ，则只需以 *-p* 选项运行 *sysctl* 命令：

```sh
$ sysctl -p
```

*-p* 选项未指定文件时， *sysctl* 命令默认加载 */etc/sysctl.conf* 。

## 附录

更多 *Linux* 技术文章请访问：[学习Linux](https://linux.fasionchan.com/)，转至 [原文](https://linux.fasionchan.com/zh_CN/latest/administration/kernel/sysctl.html) 可获得最佳阅读体验

订阅更新，获取更多学习资料，请关注我们的 [微信公众号](https://python.fasionchan.com/zh_CN/latest/about/contact.html#wechat-mp) ：

![小菜学编程](https://cdn.fasionchan.com/coding-fan-wechat-soso-qrcode.png?x-oss-process=image/resize,w_640)
