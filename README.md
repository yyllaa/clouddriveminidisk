# CloudDrive MoviePilot Bridge

这个仓库现在用于保存 `clouddrive-mini` 和 `MoviePilot` 之间的存储桥接源码，目标是让 MoviePilot 能把文件通过 `clouddrive-mini` 直传到已经挂载好的云盘，而不是先落到本地缓存目录。

## 仓库结构

- `package.v2.json`
  - MoviePilot 插件市场元数据
- `plugins.v2/clouddrivestoragebridge/`
  - MoviePilot V2 插件
  - 负责读取可用根目录、解析保存路径、执行上传预检查，并把文件流直传到 `clouddrive-mini`
- `clouddrive-mini/storage/plugins/moviepilot-storage/`
  - `clouddrive-mini` 侧桥接插件
  - 负责暴露挂载根目录、路径解析、写入探测、直传准备和秒传预检查
- `icons/Cloudrive_A.png`
  - MoviePilot 插件图标

## 当前能力

- 读取 `clouddrive-mini` 已挂载根目录
- 按电影 / 剧集 / 动漫规则解析目标路径
- 上传前先做 `upload-probe`
- 命中秒传时直接完成，不重复上传内容
- 需要上传时走已知大小流式直传，避免回退到 `clouddrive-mini` 本地上传缓存目录

## 重要说明

这份仓库保存的是桥接插件源码。

直传数据面依赖 `clouddrive-mini` 主项目里的对应服务端接入，包括本地已经完成的：

- `backend/app/web_server.py`
- `backend/app/web_server_builtin_routes.py`

也就是说，MoviePilot 侧插件和 `moviepilot-storage` 插件可以直接从这里取，但要完整启用 50G 级文件直传，仍需要把上述主项目改动合并到你的 `clouddrive-mini` 运行环境中。

## MoviePilot 安装

把下面两项放进 `MoviePilot-Plugins` 仓库：

- `package.v2.json`
- `plugins.v2/clouddrivestoragebridge/`

然后在 MoviePilot 里安装 `CloudDrive 存储桥接` 插件并配置：

- `server_url`
- `token`
- `root_key`（可选）
- `movie_dir` / `tv_dir` / `anime_dir`

## clouddrive-mini 安装

把 `clouddrive-mini/storage/plugins/moviepilot-storage/` 放入你的 `clouddrive-mini` 插件目录，并配置桥接 token。
