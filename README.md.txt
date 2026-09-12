学术论文阅读助手｜犀牛鸟Hy3实战任务一
基于 Hy3 大模型原生提示词工程构建的AI应用，面向科研人员，实现英文论文文本结构化信息抽取。

> ⚠️本项目为犀牛鸟实战任务一实现，**不使用MCP，不调用任何外部Python工具**，全部功能依靠Prompt实现。
1. 场景选择理由
科研人员在阅读外文论文摘要、片段时，需要快速梳理关键词、研究对象、实验方法、核心结论与研究局限。
普通自由对话输出文本松散，不利于整理文献笔记。本应用通过固定提示词输出标准化JSON结构化结果，方便科研人员快速做文献整理、笔记归档。
 2. 应用功能
输入英文论文摘要或者段落文本，输出标准化JSON，包含5个字段：
- `keywords`：领域专业关键词，优先提取复合专业术语
- `research_object`：研究对象与研究载体
- `research_method`：论文采用的实验手段、分析方法
- `main_conclusion`：原文给出的核心研究结论
- `limitation`：文中明确提到的研究局限与不足
3. 使用方法
1. 打开WorkBuddy（Hy3客户端），新建空白对话窗口，避免上下文污染
2. 复制本仓库 `prompt.md` 中的完整提示词模板
3. 将模板中 `{{user_input_paper_text}}` 替换成需要解析的英文论文文本
4. 将全部内容复制粘贴发送，模型直接返回JSON结构化结果
示例输入
Postharvest decay limits the shelf‑life of fresh blueberries. This research investigates the preservation effect of chitosan edible coating combined with tea polyphenols. Physiological indicators including weight loss rate and fruit firmness are measured during storage. The composite coating effectively inhibits mold growth and extends storage time. The disadvantage is that high concentration coating will cause slight surface discoloration of fruit.
示例输出
```json
{"keywords":["postharvest decay","shelf-life","fresh blueberry","chitosan edible coating","tea polyphenols","composite coating","weight loss rate","fruit firmness","mold growth","preservation","storage time","surface discoloration"],"research_object":"Shelf-life extension and preservation of fresh blueberries using chitosan edible coating combined with tea polyphenols","research_method":["Composite edible coating treatment (chitosan + tea polyphenols)","Concentration-gradient experimental design","Measurement of weight loss rate during storage","Measurement of fruit firmness during storage","Visual/physiological observation of mold growth","Storage-period preservation evaluation"],"main_conclusion":["The chitosan-tea polyphenol composite coating effectively inhibits mold growth on fresh blueberries","The composite coating extends the storage/shelf-life time of fresh blueberries","The coating demonstrates a clear preservative effect as shown by reduced weight loss and maintained fruit firmness"],"limitation":["High-concentration coating causes slight surface discoloration of the fruit"]}
4. 评测方案设计依据
按照任务要求自定义评测体系，共设置 5 个评估维度，每项 1‑5 分，5 分为最优：
专业术语正确性：关键词、专业名词准确，不混入无关注释，无编造术语
事实准确性：输出严格忠于原文，重点防范大模型幻觉，不能脑补原文不存在信息
信息完备性：原文关键的对象、方法、结论、局限完整提取，无重要信息丢失
信息精简度：输出无大量冗余无效词汇
输出格式合规性：返回合法 JSON；原文无对应内容时数组返回空数组[]
(维度设计理由：学术类应用，事实准确性是最高优先级；同时要求输出 JSON 可以被下游程序解析，因此加入格式合规性维度。)
评测样本集共 6 组 Case，为人工模拟多学科 SCI 文本。
覆盖场景：完整标准摘要、简短实验片段、带噪声干扰文本、信息残缺文本、长论述文本；学科覆盖环境科学、食品科学、海洋生态、材料化学、公共卫生、水资源。
评测方式：半人工评测，对照原始输入逐条人工打分。
相关文件：
评测输入样本集：eval_dataset.md
模型全部输出记录：eval_output.md
打分表、Case 归因、能力边界：eval_result.md
5. 评测汇总结论
当输入为信息完整的标准英文摘要，模型表现较好，平均分可达 4.6‑4.8，能够稳定完成结构化抽取。
当输入为只有实验操作、没有给出结果的片段文本，容易出现幻觉虚构结论，平均分下降至 3.6‑3.8。
现存问题：输出偶尔混入中文注释；对于原文不存在局限性的样本，无法稳定返回空数组；会出现字段之间信息重复。
6. 模型能力边界
✅ 适合场景：输入为完整 SCI 英文摘要，包含明确研究对象、实验方法、结论、局限性。
⚠️ 不适合场景：
1. 仅采样 / 实验操作记录，没有给出研究结果的零散片段，极易产生幻觉编造结论；
2. 对输出纯净度有极高要求的自动化解析场景，存在插入中文注释的不稳定现象。
7. Demo 演示
仓库内 demo.mp4（时长小于 2 分钟），演示流程：新建 WorkBuddy 对话，粘贴 Prompt 与论文文本，获取 JSON 结构化输出。