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
Usage: ./gxde-k9 [options]

Options:
  --slimy-dir <path>   Specify the directory to monitor for .slimy scripts.
  --timer-dir <path>   Specify the directory to monitor for .timer files.
  --pid-dir <path>     Specify the location for the PID DIR.
  -h                   Show this help message and exit.

Description:
  K9 Lick Daemon gen 2 monitors a specified directory for .slimy scripts
  and executes them. It also supports .timer files with crontab-like schedules.
  By default, it monitors /usr/share/gxde-k9/slimy/ and /usr/share/gxde-k9/timer/, as well as
  user-specific directories: $HOME/.local/share/GXDE/gxde-k9/slimy/ and $HOME/.local/share/GXDE/gxde-k9/timer/.

Timer Example:
* * * * * | <command>
- - - - - -
| | | | | |
| | | | | +--- IMPORTANT: K9 Need an extra '|' to identify commands !!!!!!!!!!
| | | | +----- Day of week (0 - 7) (Sunday=0 or 7)
| | | +------- Month (1 - 12)
| | +--------- Day of month (1 - 31)
| +----------- Hour (0 - 23)
+------------- Minute (0 - 59)
                                                            |
注意：如果是使用root启动，则默认的系统slimy和timer位置为

/usr/share/gxde-k9/system/slimy/ and /usr/share/gxde-k9/system/timer/


#### **目录结构**

* **`slimy` 目录**：存放需要自动执行的 `.slimy` 脚本。
* **`timer` 目录**：存放定时器配置文件（`.timer` 文件），每行使用以下格式：

slimy有example,可以非常简单地创建一个[watchdog](src/usr/share/gxde-k9/slimy/example.slimy.example)



```
Timer Example:
* * * * * | <command>
- - - - - -
| | | | | |
| | | | | +--- IMPORTANT: K9 Need an extra '|' to identify commands !!!!!!!!!!
| | | | +----- Day of week (0 - 7) (Sunday=0 or 7)
| | | +------- Month (1 - 12)
| | +--------- Day of month (1 - 31)
| +----------- Hour (0 - 23)
+------------- Minute (0 - 59)

*/2 * * * * Use / to explain every
```
注意这里要多一个 | 


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

