# MHTML 转 HTML 工具

这是一个零依赖、纯浏览器端的 `.mhtml` / `.mht` 文件转换工具，可将 MHTML 转换为独立的 HTML 文件。所有处理均在浏览器本地完成，文件不会上传到服务器。

[English](README.md)

## 功能特性

- 支持一次转换一个或多个 `.mhtml` / `.mht` 文件。
- 支持点击选择文件，也支持将文件拖放到上传区域。
- 将图片、样式表、框架及其他引用资源以内嵌 Base64 Data URI 的方式写入 HTML。
- 支持解析 `cid:`、绝对 URL、相对路径和文件名引用，并处理 `src`、`href`、`srcset` 及 CSS `url(...)`。
- 针对 Microsoft Word、WPS 等办公软件导出的 HTML 提供精简选项：
  - 删除 Office 条件注释和元数据。
  - 删除 Office 命名空间元素及声明。
  - 删除 `mso-*` 样式、空标签和 Office 专用 class。
  - 将重复的行内样式提取为可复用的 CSS class。
  - 压缩多余空白，同时保护 `pre`、`textarea` 和 `code` 内容。
- 支持在新标签页预览，或下载为 `.html` 文件。
- 支持中英文界面切换，并在本地记住语言选择。

## 快速开始

无需构建，也无需安装依赖。

1. 使用现代浏览器直接打开 [`index.html`](index.html)。
2. 将一个或多个 `.mhtml` / `.mht` 文件拖入上传区域，或点击上传区域选择文件。
3. 根据需要选择转换选项：
   - **精简输出**：默认启用，适合 Word/WPS 生成的 HTML。
   - **内联资源**：默认启用，将 HTML 引用的资源嵌入输出文件。
  - **解码 CSS Unicode 转义和 HTML 数字字符引用**：默认启用，将 CSS 中的 `\\5B8B\\4F53` 和正文中的 `&#25968;` 等引用转换为实际字符。
4. 点击 **下载 HTML** 保存转换结果，或点击 **预览** 在新标签页中打开。

如果浏览器或运行环境限制直接打开本地文件，也可以使用任意静态 HTTP 服务器提供当前目录。

## 工作原理

转换器全部实现于 [`index.html`](index.html) 中，主要流程如下：

1. 使用浏览器 File API 读取用户选择的文件。
2. 从 MHTML 头部提取 MIME `boundary`。
3. 解析各个 MIME 部分，并解码 Base64、quoted-printable 或原始二进制内容。
4. 将第一个 `text/html` 部分作为主 HTML 文档。
5. 根据资源引用替换为生成的 Data URI。
6. 可选执行 HTML 清理和样式去重。
7. 将输出字符集统一为 UTF-8，并在需要时补充 doctype。

## 隐私说明

这是一个纯本地工具。源文件和转换内容只保留在当前浏览器标签页中，不会通过网络上传或交给远程服务处理。

清空或替换结果时，工具会释放已创建的 Blob URL。界面语言保存在浏览器 `localStorage` 的 `mhtml2html-language` 项中。

## 使用限制

- 输入文件必须包含有效的 MIME boundary，并至少包含一个 `text/html` 部分。
- 第一个 `text/html` MIME 部分会被作为主文档。
- 资源匹配依赖 MHTML 的 `Content-Location` / `Content-ID` 和文件名兜底匹配；同名资源存在歧义时，可能使用第一个匹配项。
- 大文件会在浏览器内存中读取和转换，可能需要较多内存。
- 输出始终以 UTF-8 保存。可选解码 CSS Unicode 转义和 HTML 数字字符引用。对于原本不是 Base64 编码的资源，内联后文件体积可能增大。
- 浏览器需要支持 `File.arrayBuffer()`、`TextDecoder`、`Blob`、`URL.createObjectURL()` 及 Data URI。

## 许可证

本项目使用 [MIT License](LICENSE) 授权。
