# K9 Lick Daemon Gen 2

K9 Lick Daemon 是一个类 `systemd` 的轻量级服务管理工具，可以自动运行指定目录中的 `.slimy` 脚本，并实现服务的动态管理。它支持通过参数指定目录，并提供友好的日志信息。

K9 Lick Daemon is a lightweight service management tool similar to `systemd`. It automatically runs `.slimy` scripts in a specified directory and supports dynamic management of services. You can specify directories through parameters and view detailed logs.

---

## 功能 Features

- **自动运行**：监控指定目录中的 `.slimy` 脚本并执行。
- **动态配置**：支持通过命令行参数设置监控目录。
- **日志输出**：以时间戳记录操作日志，便于调试。
- **信号处理**：捕获 `SIGINT` 和 `SIGTERM` 信号，确保子进程安全退出。

- **Auto Execution**: Monitors and executes `.slimy` scripts in the specified directory.
- **Dynamic Configuration**: Supports setting the monitored directory through command-line arguments.
- **Logging**: Logs operations with timestamps for easier debugging.
- **Signal Handling**: Captures `SIGINT` and `SIGTERM` signals to safely terminate child processes.

---

## 使用方法 Usage

### 启动脚本 Run the Script

#### 使用默认目录 Use Default Directory

```bash
./k9-daemon.sh
```

默认监控目录为 /etc/gxde-k9/slimy/

The default monitored directory is /etc/gxde-k9/slimy/

#### 指定自定义目录 Specify a Custom Directory
 
```bash
./k9-daemon.sh --slimy-dir /path/to/custom/slimy/


```

#### 查看帮助 Show Help
```bash
./k9-daemon.sh -h


```

### 目录结构建议 Suggested Directory Structure

```
/etc/gxde-k9/
├── slimy/      # 监控目录，存放 .slimy 脚本
└── timer/      # （可选）存放定时器相关配置

```

* slimy/: 存放需要自动运行的 .slimy 脚本
* timer/: （暂未实现）存放与定时任务相关的配置。

### 信号处理 Signal Handling

当脚本接收到 Ctrl+C（SIGINT）或其他终止信号（SIGTERM）时，会自动清理所有子进程。

When the script receives a Ctrl+C (SIGINT) or other termination signals (SIGTERM), it will automatically clean up all child processes.

### 注意事项 Notes

* 确保 .slimy 脚本具有可执行权限： Ensure .slimy scripts are executable:

```bash
chmod +x /path/to/slimy/script.slimy
```

* 如果指定的目录不存在，脚本会提示错误并退出： If the specified directory does not exist, the script will show an error and exit.

* 使用日志信息便于排查运行中的问题。 Use log information to troubleshoot runtime issues.