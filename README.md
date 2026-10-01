# 🎵 my-music

个人音乐库。音频文件用 **Git LFS** 管理，仓库本身只存指针文件，方便长期累积歌曲。

## 目录结构

| 路径 | 说明 |
| --- | --- |
| `*.mp3` / `*.wav` / `*.flac` / `*.m4a` / `*.ogg` / `*.aac` / `*.opus` | 音频文件，由 `.gitattributes` 规则自动交给 Git LFS 存储 |
| `.gitattributes` | Git LFS 追踪规则（含音频文件清单） |
| `.gitignore` | 忽略系统与临时文件 |

当前收录：

| 文件 | 大小 | 格式 |
| --- | --- | --- |
| `swgq.mp3` | 8.49 MB (8,881,264 字节) | MPEG-1 Layer III，128 kbps，LAME3.101 编码 |

## 直链用法（给《我的世界》模组等第三方应用）

音频文件的直链格式：

```
https://raw.githubusercontent.com/CSZ2005/my-music/main/<文件名>
```

例如：

```
https://raw.githubusercontent.com/CSZ2005/my-music/main/swgq.mp3
```

GitHub 会返回 `Content-Type: audio/mpeg`（LFS 文件会先 302 到 `media.githubusercontent.com`），
可以直接用 `URL.openStream()` / `HttpURLConnection` / `OkHttp` 读取，无需任何鉴权。

验证命令：

```bash
curl -I https://raw.githubusercontent.com/CSZ2005/my-music/main/swgq.mp3
# 期望看到 200/302 + Content-Type: audio/mpeg
```

> ⚠️ **LFS 流量配额**：GitHub 免费账号的 Git LFS 额度是 **1 GB 存储 + 1 GB/月下载流量**。
> 这首歌约 8.5 MB，粗略算每月 110 次左右下载就会用尽，超限后直链会返回 403 直到下月重置。
> 如果只是给模组玩家频繁下载，建议改用普通提交（不走 LFS）：
> ```bash
> git lfs migrate export --include="*.mp3" --everything
> git push --force-with-lease
> ```

## 添加新歌

```bash
git clone https://github.com/CSZ2005/my-music.git
cd my-music

cp ~/Music/新歌.mp3 .
git add 新歌.mp3
git commit -m "add 新歌"
git push
```

`.gitattributes` 已经覆盖了常见音频后缀，`git add` 时自动走 LFS，不需要额外操作。
第一次在新机器上克隆时记得先装 Git LFS（`git lfs install`），否则拿到的是指针文件而不是音频。

## 查看已有歌曲

```bash
git ls-files '*.mp3' '*.wav' '*.flac' '*.m4a' '*.ogg' '*.aac' '*.opus'
# 或直接在网页上看：https://github.com/CSZ2005/my-music
```

## 本机提示（Steamcommunity302 环境）

这台机器上 GitHub 的域名被 Steamcommunity302 劫持到本地 `127.0.0.1:443` 的代理，而该代理使用自签证书，
因此普通客户端会报证书错误。在这台机器上 push/clone 时可以临时放宽校验：

```powershell
$env:GIT_SSL_NO_VERIFY = "true"     # 仅当前窗口有效
git push
```

长期方案是让 S302 把自己的根证书装进系统信任区（工具设置里通常有"安装证书"选项），装好后就不需要这个开关。

## 版权说明

本仓库为公开仓库，收录的音频仅供个人使用与分享；请确认你对上传的内容拥有相应的分发权利。
