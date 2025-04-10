# GXDE K9

**GXDE K9** 是一个轻量级的脚本监控与定时任务执行工具，支持自动执行 `.slimy` 脚本并根据定时器配置文件的规则触发任务。

K9是警犬的英文音译（canine）.ca-nine，GXDE-K9是一个简单的用户态watchdog，如果你因为种种原因无法使用systemd(比如正在容器中使用)，则可用gxde-k9实现一个基本的服务拉起工具/定时任务工具

非常适用于使用termux/proot-distro/需要一个简单的服务管理器但又不想写systemd的朋友

在termux上，仅需下载 [src/usr/bin/gxde-k9](src/usr/bin/gxde-k9) 文件并运行 bash ./gxde-k9 --termux 即可开始使用，无任何外部依赖

---

#### **功能特点**

1. 监控指定目录下的 `.slimy` 脚本，每 5 秒自动执行一次。
2. 支持 `crontab` 格式的定时任务，通过 `.timer` 文件配置触发条件和执行指令。
3. 自动检测并清理僵尸锁文件，防止多次运行。
4. 支持自定义 PID 文件路径，方便灵活部署。

#### **用法**

把需要监视启动的服务参照 [这个模板](src/usr/share/gxde-k9/slimy/example.slimy.example) 修改好后，后缀名改为`.slimy`并放入指定路径/slimy即可监控

把需要定时执行的任务参照 crontab 语法写好后，后缀名为`.timer`，放入指定路径/timer即可定时执行任务


示例：

```
* * * * *|echo "每分钟运行"
*/2 * * * *|echo "每2分钟运行"
0 12 * * *|bash /path/to/script.sh
```


```
用法: ./gxde-k9 [选项]

选项:
  --base-dir <路径>     指定监视 .slimy .timer .shot 脚本的目录。
  --pid-dir <路径>       指定 PID 目录的位置。
  --termux               使用 ${HOME}/gxde-k9/ 作为监视目录，以适配 termux 环境。
  -h                     显示此帮助信息并退出。

描述:
  K9 Lick Daemon 2 代监视指定目录中的 .slimy 脚本并执行它们。它还支持具有类 crontab 调度的 .timer 文件。
  默认情况下，它监视 $SLIMY_DIR_SYSTEM 和 $TIMER_DIR_SYSTEM，以及用户特定的目录：$SLIMY_DIR_USER 和 $TIMER_DIR_USER。

定时器示例：
* * * * * | <命令>
- - - - - -
| | | | | |
| | | | | +--- 重要提示: K9 需要额外的 '|' 来识别命令!!!!!!!!!
| | | | +----- 星期几 (0 - 7) (星期天=0 或 7)
| | | +------- 月份 (1 - 12)
| | +--------- 月日 (1 - 31)
| +----------- 小时 (0 - 23)
+------------- 分钟 (0 - 59)


Usage: $0 [options]

Options:
  --base-dir <path>    Specify the directory to monitor for .slimy .timer .shot scripts.
  --pid-dir <path>     Specify the location for the PID DIR.
  --termux             Use ${HOME}/gxde-k9/ as monitor directory to adapt termux.
  -h                   Show this help message and exit.

Description:
  K9 Lick Daemon gen 2 monitors a specified directory for .slimy scripts
  and executes them. It also supports .timer files with crontab-like schedules.
  By default, it monitors $SLIMY_DIR_SYSTEM and $TIMER_DIR_SYSTEM, as well as
  user-specific directories: $SLIMY_DIR_USER and $TIMER_DIR_USER.

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

```
                                                           
注意：如果是使用root启动，则默认的系统slimy和timer位置为

`/usr/share/gxde-k9/system/slimy/` and `/usr/share/gxde-k9/system/timer/`


#### **目录结构**

```
.
├── shot
├── slimy
├── edging
└── timer

3 directories, 0 files
```

* **`shot` 目录**：存放启动时执行一次的`.shot` 文件
* **`slimy` 目录**：存放需要自动执行的 `.slimy` 脚本
* **`edging` 目录**：存放需要保活的 `.edging` 脚本
* **`timer` 目录**：存放定时器配置文件 `.timer` 文件



#### **示例**

1. 默认启动：
   
   ```
   gxde-k9
   ```
2. 监控自定义目录并指定 PID 文件：
   
   ```
   gxde-k9 --base-dir /my/slimy/scripts --pid-dir /tmp/mydaemon/
   ```
3. 在termux上使用：
   
   ```
   gxde-k9 --termux
   ```

