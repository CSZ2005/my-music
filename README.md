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

⚠️ **本仓库的音频走 Git LFS，所以必须用 `media.githubusercontent.com` 这个地址**：

```
https://media.githubusercontent.com/media/CSZ2005/my-music/main/<文件名>
```

即：

```
https://media.githubusercontent.com/media/CSZ2005/my-music/main/swgq.mp3
```

实测响应（2026-10 验证）：

```
HTTP/1.1 200 OK
Content-Type: audio/mpeg
Content-Length: 8881264
Accept-Ranges: bytes
```

带 `Range: bytes=0-99` 请求时返回 `206 Partial Content` + `Content-Range: bytes 0-99/8881264`，
首字节为 `49 44 33`（`ID3`），可以直接用 `URL.openStream()` / `HttpURLConnection` / `OkHttp` 读取，无需鉴权。

### ❌ 不要用这个地址

```
https://raw.githubusercontent.com/CSZ2005/my-music/main/swgq.mp3
```

对 LFS 文件，`raw` 只会返回 **132 字节的指针文本**（`content-type: text/plain`，内容形如
`version https://git-lfs.github.com/spec/v1`），播放器/模组会报"不是音频"。

### 验证命令

```bash
curl -I https://media.githubusercontent.com/media/CSZ2005/my-music/main/swgq.mp3
# 期望：200/206 + Content-Type: audio/mpeg + Content-Length: 8881264
```

> ⚠️ **LFS 流量配额**：GitHub 免费账号的 Git LFS 额度是 **1 GB 存储 + 1 GB/月下载流量**。
> 这首歌约 8.5 MB，粗略算每月 110 次左右下载就会用尽，超限后直链会返回 403，直到下月重置。
> 如果是给模组玩家频繁下载，建议改回普通文件（见下节），那样 `raw` 直链可直接给出音频且没有流量限制。

## 可选：不用 LFS，改用 raw 直链

如果你更看重"raw 直链直接给音频 + 没有下载流量配额"，可以把音频从 LFS 里迁出来（历史会被重写）：

```bash
git lfs migrate export --include="*.mp3" --include="*.wav" --include="*.flac" --everything
git push --force-with-lease
```

之后这些地址就是真音频了：

```
https://raw.githubusercontent.com/CSZ2005/my-music/main/swgq.mp3
```

代价：音频直接进 Git 历史，仓库体积会变大（GitHub 建议单仓库 < 1 GB，单文件硬上限 100 MB，
单个文件超过 50 MB 会收到警告）。

## 添加新歌

```bash
git clone https://github.com/CSZ2005/my-music.git
cd my-music

cp ~/Music/新歌.mp3 .
git add 新歌.mp3
git commit -m "add 新歌"
git push
```

`.gitattributes` 已覆盖常见音频后缀，`git add` 时自动走 LFS，无需额外操作。
新机器的首次克隆要先装 Git LFS（`git lfs install`），否则拿到的是指针文件而不是音频。
推完之后，新歌的直链就是：

```
https://media.githubusercontent.com/media/CSZ2005/my-music/main/新歌.mp3
```

## 查看已有歌曲

```bash
git ls-files '*.mp3' '*.wav' '*.flac' '*.m4a' '*.ogg' '*.aac' '*.opus'
# 或直接看网页：https://github.com/CSZ2005/my-music
```

## 本机提示（Steamcommunity302 环境）

这台机器上 GitHub 的域名被 Steamcommunity302 劫持到本地 `127.0.0.1:443` 的代理，而该代理使用自签证书，
因此普通客户端会报证书错误。在这台机器上 push/clone 时可临时放宽校验：

```powershell
$env:GIT_SSL_NO_VERIFY = "true"     # 仅当前窗口有效
git -c http.sslBackend=openssl push # 默认的 schannel 后端会报 SEC_E_NO_CREDENTIALS
```

长期方案是在 S302 里安装它自己的根证书到系统信任区（工具设置里通常有"安装证书"选项），装好后就不需要这两个开关。

## 版权说明

本仓库为公开仓库，收录的音频仅供个人使用与分享；请确认你对上传的内容拥有相应的分发权利。
