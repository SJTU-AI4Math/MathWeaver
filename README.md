# MathWeaver Desktop

MathWeaver 是面向数学教材的桌面知识工作台：把 PDF 转换为 Markdown，抽取定义、定理、例题等知识节点及其关系，并在图谱界面中浏览、检查和导出结果。

当前公开版本是 **Windows x64 研究预览版 `v0.1.1`**。本仓库用于分发桌面程序和 OCR 组件，不包含 MathWeaver 源代码，也不提供 macOS 或 Linux 安装包。

## 下载与快速开始

### 1. 下载桌面程序

请从固定版本 `v0.1.1` 的 Release 下载以下两个文件：

- [MathWeaver-0.1.1-windows-x64.zip](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/MathWeaver-0.1.1-windows-x64.zip)：完整 Windows 桌面程序。
- [SHA256SUMS.txt](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/SHA256SUMS.txt)：发布文件的完整性校验清单。

也可以先打开 [v0.1.1 Release 页面](https://github.com/SJTU-AI4MATH/MathWeaver/releases/tag/v0.1.1) 查看发布说明和全部资产。

请不要手动下载 OCR 的 29 个分片。首次处理 PDF 时，应用会从公开 Release 自动下载并安装 OCR 组件。

### 2. 用 `SHA256SUMS.txt` 校验下载文件

`SHA256SUMS.txt` 记录了 Release 中各文件的 SHA-256 摘要。它的作用是确认下载的 ZIP 没有损坏，也没有在下载过程中被意外替换。它不是安装文件，不需要放进解压后的程序目录。

在包含两个下载文件的目录中打开 PowerShell，运行下面的命令：

```powershell
$zip = Join-Path (Get-Location) "MathWeaver-0.1.1-windows-x64.zip"
$record = Select-String -LiteralPath ".\SHA256SUMS.txt" `
    -Pattern ([regex]::Escape((Split-Path -Leaf $zip))) |
    Select-Object -First 1

if (-not $record) {
    throw "SHA256SUMS.txt 中没有找到 ZIP 的校验记录。"
}

$expected = ($record.Line -split '\s+', 2)[0].ToLowerInvariant()
$actual = (Get-FileHash -LiteralPath $zip -Algorithm SHA256).Hash.ToLowerInvariant()

if ($actual -ne $expected) {
    throw "校验失败：请删除当前 ZIP 后重新下载。"
}

Write-Host "校验通过，可以解压并运行 MathWeaver。"
```

只有显示“校验通过”后才应继续使用该 ZIP。如果校验失败，请删除 ZIP 和不完整的下载副本后重新下载；不要使用校验不一致的程序。

校验完成后可以保留 `SHA256SUMS.txt` 供日后复核，也可以删除它，不会影响 MathWeaver 的安装、启动或 OCR 运行。若需要再次确认文件完整性，请保留它。

### 3. 完整解压并启动

1. 将 ZIP 解压到一个有写入权限的本地目录。
2. 保留解压目录中的全部文件，尤其是 `win-unpacked` 下的 DLL、`resources` 和其他运行时文件。
3. 从完整解压目录运行 `MathWeaver.exe`，不要把 EXE 单独复制到其他位置。

当前版本未进行代码签名，Windows SmartScreen 可能显示警告。请先确认下载地址和 SHA-256 校验结果；如果组织安全策略不允许运行未签名程序，请等待签名版本或联系管理员。

### 4. 首次安装 OCR

第一次处理 PDF 时，应用会提示安装本地 OCR 组件：

- 首次下载约 **1.88 GB**。
- 安装过程建议至少预留 **5.34 GB** 可用磁盘空间。
- 组件安装在 `%LOCALAPPDATA%\MathWeaver\ocr`。
- 首次安装需要网络连接；安装成功后，OCR 运行时和模型保存在本机，可以在断网状态下重复进行 OCR。
- 下载中断时重新打开应用并重试即可；已完成校验的组件不会每次重复下载。

OCR 组件已经包含在桌面程序支持的发布流程中，不要求新手预先安装 Python、`uv` 或 MinerU。

### 5. 处理 PDF

完成 OCR 安装后，在应用中选择 PDF 并开始处理。基本流程是：

1. 上传 PDF。
2. OCR 生成 Markdown。
3. 抽取定义、定理、例题等知识节点。
4. 构建节点之间的逻辑关系。
5. 在图谱和条目视图中检查结果，并导出处理产物。

## 生成知识图谱前的 API 配置

桌面版会由 Electron 在 Windows 本机启动随包后端，前端默认通过本机地址（`127.0.0.1`）调用它。OCR、任务状态和配置处理都在本机完成。API Key 输入后只会在本机应用与本机后端之间传递，再由本机后端发送给你填写的模型服务商；不会回传到 MathWeaver 项目、GitHub Release 或公开网页，也不会在界面中公开显示。调用模型服务时，API Key 必然会发送到你指定的服务商 API 地址。

这里的“本地”指本分发仓库提供的 Windows 桌面程序。如果你把项目部署到远程服务器，或使用其他在线版本，则应以对应部署的隐私和安全政策为准。

OCR 是本地运行的，但后续知识抽取和关系构建可能需要调用大语言模型服务。请在应用的 API 设置中填写：

- LLM API URL（服务商的 API Base URL，不是控制台网页地址）。
- LLM 模型名称。
- LLM API Key。
- Embedding API URL、模型名称和 API Key；如果服务商支持兼容接口，可以按应用提示复用 LLM 配置。

API 请求是否收费、收费多少由你选择的服务商决定。建议为 MathWeaver 单独创建 API Key、设置额度，并不要把 Key 提交到 GitHub 或发送给其他人。

如果 OCR 已成功但图谱没有生成，请依次检查 API URL、模型名称、API Key、Embedding 配置和网络连接。OCR 与后续 LLM 知识抽取是两个独立阶段。

## 知识图谱提取技术概览

```mermaid
flowchart LR
    A[PDF] --> B[本地 MinerU OCR]
    B --> C[Markdown]
    C --> D[14 阶段知识抽取与关系构建]
    D --> E[节点与边]
    E --> F[图谱界面与导出]
```

公开版默认使用 14 阶段固定流程：

- **文本修复与分段**：校正原文、识别文档结构并切分上下文。
- **知识提取与补全**：提取数学知识单元，补全可能遗漏的内容，清理无效内容并拆分复合知识。
- **结构化与引用校正**：生成标题和结构化节点，补充语义信息，修复知识结构，识别和校正文内引用。
- **关系构建与输出**：根据节点内容提取关系，生成最终节点/边结果，供图谱界面浏览和导出。

### 不同输入文档的处理方式

流水线会根据输入类型选择不同的前置处理方式，但后续知识节点和关系仍会进入统一的 14 阶段流程：

| 输入类型 | 前置处理 | 后续处理 |
| --- | --- | --- |
| PDF | 在本机调用 MinerU OCR，将版面、文字和公式转换为 Markdown；随后清洗和归档 OCR 产物。 | 对生成的 Markdown 进行知识节点、引用和关系抽取。 |
| Markdown 或 TXT | 直接把文本作为源输入，跳过 OCR 和 PDF 清洗，避免重复转换。 | 直接进入文档分段、知识抽取和关系构建。 |
| TeX | 不经过 OCR；识别定理、定义、引理、证明等 TeX 结构，保留标签、原始文本和源文件位置。无法被确定性解析的残余文本再进入受控的 LLM 提取与恢复流程。 | 在保留源锚点的同时进入统一的节点清理、结构化、引用和关系阶段。 |

### 多通道混合召回与 LLM 调用控制

关系构建不会把所有节点两两组合后全部交给 LLM，而是先进行多通道候选召回：

- **稀疏通道**：局部窗口、文内引用和别名匹配、BM25F 文本匹配、谓词与数学符号重合。
- **稠密通道**：对逻辑陈述、定义内容以及“条件—结论”进行 Embedding 相似度检索；Embedding 结果写入缓存，重复运行时可以复用。
- **图结构扩展**：从已确认的显式关系出发扩展可能的支持节点，补充单一文本通道可能遗漏的候选。

各通道的候选通过 RRF 等方式合并，并保护每个通道的高排名候选；随后限制预重排和最终候选数量，只把筛选后的候选批次交给现有 LLM 做关系重排和判定。这样可以利用互补通道提高候选召回覆盖，同时避免对全部节点对进行 LLM 调用，从而减少请求次数和费用。实际费用仍取决于文档规模、候选数量、Embedding/LLM 服务商和模型价格；召回阶段本身不会直接创建最终知识图谱边，最终关系仍需后续判定和输出阶段确认。

流程完成不等于每个节点或关系都无需人工复核，数学教材中的公式、引用和跨段关系仍建议在图谱界面中检查。

## Release 中的辅助文件

Release 还提供以下可供复核或开发者使用的文件：

- [`manifest.json`](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/manifest.json)：OCR 组件版本、下载地址、容量和摘要。
- [`mineru-sbom.json`](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/mineru-sbom.json)：OCR 依赖的软件物料清单。
- [`models-manifest.json`](https://github.com/SJTU-AI4MATH/MathWeaver/releases/download/v0.1.1/models-manifest.json)：随 OCR 发布的模型清单。
- [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) 与 [`licenses/`](licenses/)：第三方组件说明和许可证文本。

普通用户只需要下载桌面 ZIP 和 `SHA256SUMS.txt`；不需要单独处理 OCR 分片、模型分片或 SBOM 文件。

## 常见问题

### OCR 一直显示需要下载

这是首次安装或本机组件尚未完成校验。保持网络连接，确认磁盘空间足够，然后重新开始安装。若下载曾中断，重新打开应用并重试即可。

### OCR 下载失败或磁盘空间不足

确认网络可以访问 GitHub Release，至少预留 5.34 GB 可用空间，并检查 `%LOCALAPPDATA%\MathWeaver\ocr` 所在磁盘的权限。不要手动解压或改名 OCR 分片。

### Windows 显示 SmartScreen 警告

这是因为当前研究预览版未进行代码签名。请先核对 Release 地址和 SHA-256；如果单位策略禁止未签名程序，请不要绕过策略。

### 只打开 EXE 后启动失败

请重新下载并完整解压 ZIP，从解压目录运行 `MathWeaver.exe`。不要只复制 EXE，也不要删除 `resources` 或 DLL 文件。

### SHA-256 校验失败

删除当前 ZIP 和不完整的下载副本，重新从 [v0.1.1 Release](https://github.com/SJTU-AI4MATH/MathWeaver/releases/tag/v0.1.1) 下载。不要运行校验值不一致的文件。

### OCR 成功但知识图谱没有生成

OCR 与 LLM 知识抽取是两个阶段。检查 API URL、模型名称、API Key、Embedding 配置和网络连接，并查看应用中的任务错误详情。

## 许可证与发布范围

- MathWeaver 桌面程序是研究预览版，适用范围和限制见 [`EULA.md`](EULA.md)。
- MathWeaver 源代码不包含在本二进制分发仓库中；本仓库不授予 MathWeaver 源代码的开源许可。
- OCR 运行时和模型是第三方组件，适用其自身许可证和通知文件。MathWeaver 的 EULA 不替代、修改或限制这些第三方许可证。
- 详细第三方说明见 [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) 和 [`licenses/`](licenses/)；Release 中的许可证、SBOM 和模型清单以对应版本资产为准。

使用本分发包前，请阅读 [`EULA.md`](EULA.md) 以及随包提供的第三方许可证文本。
