# 节点插件

存放节点脚手架构建生成的 `.umd.js` 文件。

- 建议路径：`nodes/插件名/版本号/文件名.umd.js`。
- 在根目录 `index.json` 中使用 `type: "node"`，登记插件标识、版本、文件相对路径与 SHA256。
- 只上传编译后的可安装产物；普通 JavaScript 或 TypeScript 源码不能替代节点构建产物。

详见[发布说明](../README.md)。当前分类已收录 `headSwapNode` 1.0.1。
