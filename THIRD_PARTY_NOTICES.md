# Third-party notices

本仓库的MIT仅适用于原创代码与文档，不对第三方数据、模型及依赖重新授权。

| 组件 | 来源 | 核查结果 / 处理 |
| --- | --- | --- |
| 中文基础模型 | https://huggingface.co/hfl/chinese-roberta-wwm-ext | 上游标注Apache-2.0；当前源码发布不附带权重或分词器 |
| Chinese-BERT-wwm项目 | https://github.com/ymcui/Chinese-BERT-wwm | Apache-2.0；公开模型前应保留所需上游许可与通知，并说明微调修改 |
| C-ReD | https://github.com/HeraldofLight/C-ReD | 2026-09-18核查未发现许可证文件；API的license为null；不随仓库分发其数据 |
| 微调权重 | 本项目训练 | 暂不作开放权重许可承诺；等待明确的上游许可依据 |
| Python依赖 | requirements文件所列各项目 | 通过各自官方分发渠道安装，许可分别遵循上游 |

公开可访问、可下载和论文可引用，都不自动等于获得数据复制或再分发许可。参见 [GitHub licensing documentation](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository)。

建议取得C-ReD作者对以下事项的明确说明：本地科研训练是否允许、微调权重是否可公开、权重可采用何种许可、商业使用是否受限、如何保留归属与引用。不得把仅限研究使用的权重许可称为不受限制的开源许可。
