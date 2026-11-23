# Weavra-plugins

Weavra 的独立公开插件发布仓库，用于按类别存放插件包和维护插件目录。

当前已收录 2 个插件。主程序的“发现插件”读取本仓库的 `index.json`；推送后客户端刷新即可看到。

## 仓库结构

```text
index.json
nodes/
tools/
skills/
extensions/
```

| 目录 | 索引 type | 内容 |
| --- | --- | --- |
| [nodes](./nodes/) | `node` | 节点插件，脚手架构建后的 `.umd.js` |
| [tools](./tools/) | `tool` | 工具插件，构建后的 `.tool.js` |
| [skills](./skills/) | `skill` | 技能包，`.zip`、`.md`、`.tar`、`.tar.gz`、`.tgz` |
| [extensions](./extensions/) | `ext` | 文件扩展，`ext-名称.umd.js` |

主程序代码和应用更新包继续在 [Weavra](https://github.com/liuwqq/Weavra) 维护，插件可独立发布。

## 发布一个插件

1. 将构建后的插件包放入对应分类，建议使用 `分类/插件名/版本号/文件名`，已有版本保持不变。
2. 为新插件增加一条 `index.json` 记录；更新已有插件时修改其版本、路径和 SHA256。`type` 与 `name` 的组合必须唯一。
3. 将插件包与索引一起提交到 `main` 分支。文件路径和文件名区分大小写。
4. 检查索引引用的文件可以下载，再发布公告。接入索引的客户端刷新列表后可发现新版本，已安装版本不会因目录变化自动替换。

公开仓库只存放准备公开分发的内容，不提交账号配置、密钥、会话、本地数据目录或用户素材。第三方插件保留其许可证和署名。

## 索引格式

索引入口：<https://raw.githubusercontent.com/liuwqq/Weavra-plugins/main/index.json>

`index.json` 的顶层结构：

```json
{
  "schemaVersion": 1,
  "plugins": []
}
```

插件记录字段：

| 字段 | 类型 | 用途 |
| --- | --- | --- |
| `type` | string | `node`、`tool`、`skill`、`ext` |
| `name` | string | 插件内部标识，与构建产物中的标识一致 |
| `displayName` | string | 显示名称 |
| `version` | string | 插件版本，与构建产物版本一致 |
| `description` | string | 简介 |
| `author` | string | 作者 |
| `file` | string | 插件包相对仓库根目录的路径；不含 `..` 或绝对路径 |
| `sha256` | string | 文件的 SHA256，64 位小写十六进制 |

文件格式：`node` 使用 `.umd.js`，`tool` 使用 `.tool.js`，`ext` 的文件名以 `ext-` 开头并以 `.umd.js` 结尾；`skill` 使用上表中的技能包格式。

在仓库根目录执行以下 PowerShell 命令，可列出已上传插件包的路径与 SHA256，再将对应值填入索引：

```powershell
Get-ChildItem nodes,tools,skills,extensions -Recurse -File |
  Where-Object { $_.Name -ne 'README.md' } |
  ForEach-Object {
    [pscustomobject]@{
      file = (Resolve-Path -LiteralPath $_.FullName -Relative).Substring(2).Replace('\', '/')
      sha256 = (Get-FileHash -LiteralPath $_.FullName -Algorithm SHA256).Hash.ToLowerInvariant()
    }
  }
```

接入客户端时应先校验索引版本与字段，从同一提交下载插件包，校验 SHA256，并沿用宿主现有的插件格式和安装校验。索引拉取失败应显示加载失败，而不是把已安装插件当作空列表删除。
