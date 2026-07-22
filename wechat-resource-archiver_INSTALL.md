# 微信资源归档 OpenClaw Skill 安装指南

本技能用于将通过微信 ClawBot 转发的公众号文章、网页、图片、视频、音频、文档和文字卡片归档到本地知识库。

## 能力范围

| 微信输入 | 归档结果 |
|---|---|
| 公众号文章链接 | Markdown 正文、原始 HTML、图片和元数据 |
| 普通网页链接 | Markdown 正文、原始 HTML、图片和元数据 |
| 图片、视频、音频、文档附件 | 原文件、SHA-256、消息备注和元数据 |
| 视频号卡片 | 保存卡片文字和来源；仅当 ClawBot 提供实际媒体文件时保存视频 |

技能不会绕过微信登录、访问验证、付费限制或 DRM。

## 前提条件

- 已安装并运行 OpenClaw。
- 已通过微信 ClawBot 绑定 OpenClaw。
- Python 3.9 或以上。
- 可以访问 GitHub Raw 下载服务。

## 安装

执行：

```bash
PACKAGE_BASE_URL="https://raw.githubusercontent.com/Asukamadoka/wechat-resource-archiver/main"
INSTALL_ROOT="${OPENCLAW_SKILLS_DIR:-$HOME/.openclaw/skills}"

mkdir -p "$INSTALL_ROOT"
curl -fsSL -o /tmp/wechat-resource-archiver.zip \
  "$PACKAGE_BASE_URL/wechat-resource-archiver.zip"
unzip -q -o /tmp/wechat-resource-archiver.zip -d "$INSTALL_ROOT"
python3 -m pip install --user -r \
  "$INSTALL_ROOT/wechat-resource-archiver/requirements.txt"
rm /tmp/wechat-resource-archiver.zip
```

## 配置永久归档目录

将路径替换成你的知识库 `resource` 绝对路径：

```bash
mkdir -p "$HOME/.wechat-resource-archiver"
printf '%s\n' \
  'WECHAT_RESOURCE_DIR=/absolute/path/to/knowledge-base/resource' \
  > "$HOME/.wechat-resource-archiver/config"
chmod 600 "$HOME/.wechat-resource-archiver/config"

printf '%s\n' \
  'export WECHAT_RESOURCE_DIR="/absolute/path/to/knowledge-base/resource"' \
  >> "$HOME/.zshrc"
```

如果 OpenClaw 作为后台服务运行，还需要在其服务环境中设置同一个 `WECHAT_RESOURCE_DIR`，然后重启 OpenClaw。

## 验证

```bash
python3 "$INSTALL_ROOT/wechat-resource-archiver/scripts/archive_wechat.py" --help
```

在微信中向 ClawBot 发送以下任一内容进行验证：

- “保存这篇文章”并转发公众号文章链接；
- “归档到知识库”并发送图片或文件；
- 转发视频号卡片并说“保存这条资料”。

成功后 ClawBot 应返回归档状态和本地路径。重复内容不会再次复制。
