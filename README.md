# GXDE K9

**GXDE K9** 是一个轻量级的脚本监控与定时任务执行工具，支持自动执行 `.slimy` 脚本并根据定时器配置文件的规则触发任务。

K9是警犬的英文音译（canine）.ca-nine，GXDE-K9是一个简单的用户态watchdog，如果你因为种种原因无法使用systemd(比如正在容器中使用)，则可用gxde-k9实现一个基本的服务拉起工具/定时任务工具

https://gitee.com/GXDE-OS/gxde-k9

---

#### **功能特点**

1. 监控指定目录下的 `.slimy` 脚本，每 5 秒自动执行一次。
2. 支持 `crontab` 格式的定时任务，通过 `.timer` 文件配置触发条件和执行指令。
3. 自动检测并清理僵尸锁文件，防止多次运行。
4. 支持自定义 PID 文件路径，方便灵活部署。

#### **参数说明**

| 参数              | 说明                                                                             |
| ----------------- | -------------------------------------------------------------------------------- |
| `--slimy-dir`   | 指定 `.slimy`脚本监控目录（默认：`/usr/share/gxde-k9/slimy/`）。                   |
| `--timer-dir`   | 指定 `.timer`定时器文件目录（默认：`/usr/share/gxde-k9/timer/`）。                 |
| `--pid-dir`    | 自定义 PID 文件位置（默认： `/tmp/GXDE/gxde-k9/`）。 |
| `-h`,`--help` | 显示帮助信息并退出。                                                             |

#### **目录结构**

* **`slimy` 目录**：存放需要自动执行的 `.slimy` 脚本。
* **`timer` 目录**：存放定时器配置文件（`.timer` 文件），每行使用以下格式：

slimy有example,可以非常简单地创建一个[watchdog](src/usr/share/gxde-k9/slimy/example.slimy.example)



```
<crontab格式>|<执行指令>
```

示例：

```
* * * * *|echo "每分钟运行"
0 12 * * *|bash /path/to/script.sh
```

#### **示例**

1. 默认启动：
   
   ```
   gxde-k9
   ```
2. 监控自定义目录并指定 PID 文件：
   
   ```
   gxde-k9 --slimy-dir /my/slimy/scripts --pid-dir /tmp/mydaemon/
   ```
3. 检查帮助信息：
   
   ```
   gxde-k9 --help
   ```

注意事项

```
确保 .slimy 和 .timer 文件的内容正确且可执行。
脚本需要运行在 Bash 环境中，且定时任务的 crontab 格式需合法。
```

