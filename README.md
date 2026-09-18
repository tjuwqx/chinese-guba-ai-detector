# Chinese Guba AI Detector

基于 `hfl/chinese-roberta-wwm-ext` 和 C-ReD 语料微调的中文 AI 文本二分类工具，曾用于股吧文本的研究性测量。当前公开内容是训练、推理、测试和说明文档。

**权重状态：待确认训练数据的再分发相关许可，暂不提供公开下载。** 本项目不是 C-ReD 官方模型；未使用股吧帖子进行这次微调，也没有经过金融论坛领域的人工标注测试。模型输出不能证明一段文本的真实作者身份。

## 安装

测试环境为 Python 3.10。使用独立环境，先按 [PyTorch 官方说明](https://pytorch.org/get-started/previous-versions/)安装适合设备的 PyTorch 2.5.1，再安装依赖：

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

CPU 可运行；CUDA GPU 可加速。显存不足时先将 `--batch-size` 降为 8 或 4。

## 使用本地模型

需要自行合法取得已训练模型并放入 `models/detector`。目录需要 `model.safetensors`、`config.json`、`tokenizer.json`、`tokenizer_config.json`、`special_tokens_map.json`、`vocab.txt`。本仓库目前不包含这些权重与分词器文件。

```powershell
python scripts/predict.py --model-dir models/detector --input examples/synthetic_posts.csv --output outputs/demo.csv --batch-size 32
```

示例全部为虚构文本，没有真实用户、帖子或人工/AI真值标签。检测不调用外部 API，模型只从本地加载。

输入为 UTF-8 CSV，默认读取 `帖子内容文本`，并将 `股吧代码` 规范为六位文本，保留先导零。可用 `--text-column text --stock-column id` 指定列名；没有股票列时传 `--stock-column ""`。其他列按文本原样保留。

新增列：

| 字段 | 含义 |
| --- | --- |
| 是否AI生成 | 未四舍五入的模型分数大于等于阈值时为1，否则为0；空文本留空 |
| AI生成概率 | AI类别的 softmax 分数；未经概率校准 |
| AI检测模型 | 公开模型标识，不写入本机路径 |
| AI检测阈值 | 默认0.7，是项目设置，不是论文认定的最优阈值 |
| AI检测状态 | `ok` 或 `empty_text` |

默认最多输入256个token，超过部分截断，不是256个汉字。原研究的部分股吧正文仅有约203字符的片段，检测对象因此是已采集片段。

输出先写临时文件，成功后原子替换。重新运行同一命令时，只有输入、模型、参数和完整输出的校验值均匹配才跳过。中断文件从该文件开头重算；不会凭已有行数盲目续写。需要重新生成完整结果时显式加 `--overwrite`。同一输出路径只允许一个进程写入；并行时分配不同的输入和输出文件。

## 训练

先自行核实、取得 C-ReD 使用许可。我们未在上游仓库发现明确许可证，引用论文本身不等于取得再分发授权。本项目不镜像其数据。

在已经合法取得的 C-ReD 数据目录上运行：

```powershell
python -m pip install -r requirements-train.txt
python scripts/train.py --data-root data/C-ReD --output-dir models/detector --epochs 3 --batch-size 8 --eval-batch-size 16 --no-fp16
```

程序读取文件名包含 `CReD_` 的CSV及其 `text`、`label` 列。源数据标签0=AI、1=人工；训练标签转换为0=人工、1=AI。训练协议和历史指标见 [MODEL_CARD.md](MODEL_CARD.md)。重新训练采用完整随机种子初始化；历史训练在模型初始化前没有显式设置全局种子，不能承诺逐位复现旧权重。

## 验证与公开范围

```powershell
python -m unittest discover -s tests -v
python scripts/audit_release.py
```

单元测试使用虚构数据和模拟预测，不需下载模型。真实模型冒烟测试只证明可以加载和推理，不证明预测正确。

公开内容经过文件白名单与敏感信息模式检查。股吧原始CSV、发布者ID/昵称/IP、帖子URL、论文面板、商业数据库、缓存、日志、Notebook输出、训练中间检查点和历史Git记录均不进入本仓库。脱敏范围详见 [PRIVACY.md](PRIVACY.md)，发布步骤见 [docs/PUBLISHING.md](docs/PUBLISHING.md)。

## 许可与引用

本仓库原创代码与文档采用 MIT 许可。基础模型采用上游 Apache-2.0；C-ReD 数据和衍生权重的许可需另行确认，MIT不覆盖它们。见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

相关研究请同时引用 C-ReD 与 Chinese-BERT-wwm 系列研究；条目在 [references.bib](references.bib)。软件引用信息见 [CITATION.cff](CITATION.cff)。
