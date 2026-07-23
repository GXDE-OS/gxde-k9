# AGENTS.md

本项目是以 Bash 脚本实现的 GXDE K9。

- 主要脚本：`src/usr/bin/gxde-k9`、`src/usr/bin/gxde-k9-chocker`。
- 修改后至少验证：`bash -n src/usr/bin/gxde-k9-chocker`。
- 不要修改 Debian 打包约定（`debian/`）或安装目录结构（`src/etc/`、`src/usr/`）；脚本与资源须保留现有路径布局。
