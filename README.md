# MathWeaver Desktop

MathWeaver 是面向数学教材的桌面知识工作台：将 PDF 转换为 Markdown，抽取定义、定理、例题等知识节点及其关系，并在图谱界面中浏览和检查结果。

当前发布是 Windows x64 研究预览版。应用和 OCR 组件通过 GitHub Release 分发；本仓库不包含 MathWeaver 源代码。

## 下载

固定版本：`v0.1.1`

- [下载 MathWeaver-0.1.1-windows-x64.zip](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/MathWeaver-0.1.1-windows-x64.zip)
- [下载 SHA256SUMS.txt](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/SHA256SUMS.txt)
- [查看 v0.1.1 Release](https://github.com/SJTU-AI4MATH/MathWeaver/releases/tag/v0.1.1)

下载后请先核对 ZIP 的 SHA-256，再将整个压缩包解压到本地目录。必须保留 `win-unpacked` 中的全部 DLL、`resources` 和其他文件；不能只复制或单独运行 `MathWeaver.exe`。

## 首次启动与 OCR

1. 解压完整 ZIP。
2. 运行解压目录中的 `MathWeaver.exe`。
3. 首次处理 PDF 时，应用会从公开 Release 下载本地 OCR 组件。
4. OCR 安装完成后，选择 PDF 并开始处理。

首次 OCR 下载约 1.88 GB，安装过程需要约 5.34 GB 可用磁盘空间。组件安装在：

```text
%LOCALAPPDATA%\MathWeaver\ocr
```

首次安装需要联网；安装成功后，OCR 运行时和模型保存在本机，可在断网状态下重复进行 OCR。下载中断时重新打开应用即可继续校验和安装；磁盘不足时请先释放空间后重试。

## 工作流程

桌面端的基本流程是：

1. 上传 PDF。
2. OCR 生成 Markdown。
3. 抽取定义、定理、例题等知识节点。
4. 构建节点之间的逻辑关系。
5. 在图谱和条目视图中检查结果，并导出处理产物。

本地 OCR 不要求预先安装 Python、uv 或 MinerU。后续知识抽取阶段如需调用 LLM，需在应用设置中配置 API 地址、模型名称和 API Key；这些配置不包含在发布包中。

## 常见问题

### OCR 显示需要下载

这是首次安装或本机组件尚未完成校验。请保持网络连接，确认磁盘空间充足，并重新开始安装。已安装成功的组件不会每次重复下载。

### Windows 显示 SmartScreen 警告

当前研究预览版未进行代码签名。请先核对 Release 中的 SHA-256，再决定是否运行；如果组织安全策略不允许未签名程序，请等待签名版本。

### 只打开 EXE 后启动失败

请重新解压完整 ZIP 并从解压目录运行，不要把 EXE 单独复制出来。

### OCR 成功但知识图谱没有生成

OCR 与后续 LLM 知识抽取是两个阶段。请检查 API 地址、模型名称、API Key 和网络连接，并查看应用中的任务错误详情。

## 许可证与发布范围

MathWeaver 桌面二进制为研究预览版，本分发仓库不授予 MathWeaver 源代码的开源许可。OCR 组件包含 MinerU 运行时和 PDF-Extract-Kit 模型；相关许可证和第三方说明见 [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) 及 `licenses/` 目录。

