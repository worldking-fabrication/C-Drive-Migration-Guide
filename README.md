# C-Drive-Migration-Guide

# Windows C盘 AppData 空间清理与迁移指南

这个仓库记录了如何快速排查并解决 Windows C 盘 `AppData` 文件夹爆满的问题。

## 第一步：找出占空间的“元凶”

1. **打开路径**：
   在任意文件夹的地址栏中直接输入 `%AppData%` 并回车。
   *(系统会自动进入 `C:\Users\你的用户名\AppData\Roaming` 目录)*

2. **执行扫描命令（判断谁最大）**：
   在当前窗口的地址栏输入 `powershell` 并回车，打开 PowerShell 窗口。然后复制粘贴并执行以下命令：

   ```powershell
   Get-ChildItem -Directory | ForEach-Object { $size = (Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum; [PSCustomObject]@{ Name = $_.Name; "Size(GB)" = "{0:N2}" -f ($size / 1GB) } } | Sort-Object "Size(GB)" -Descending
该命令会扫描所有文件夹，并按大小（GB）从大到小排列输出，方便你一眼看出是谁吃掉了 C 盘空间。

## 第二步：判断能否迁移及处理方法
根据第一步跑出的结果，查看列表排名前几的文件夹名称，然后采取以下对应策略：

常规软件大户（如 Docker, 微信, 百度网盘等）：
直接去该软件内部，找到软件设置，将“存储位置”、“缓存位置”或“镜像路径”修改到 D 盘即可。让软件自己去完成搬家。

针对 Python / pip：
如果发现是 pip 缓存或 Python 库（site-packages）占用了巨大空间，最简单彻底的方法是：重装 Python。
卸载 C 盘的 Python，重新安装时选择自定义安装（Customize installation），将安装路径直接设置到 D 盘。这样以后所有 pip install 下载的库都会乖乖待在 D 盘了。
