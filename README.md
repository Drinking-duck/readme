# readme
4.25实践周任务
2026 春季学期实践周 README

一、实践周完成内容
本次实践周主要围绕以下三个方向展开：
- 毕设推进
- 专业学习
- 求职准备（部分）
本周重点完成了毕业设计中的音频大模型实验平台搭建、批量实验脚本开发、ASR（自动语音识别）评测以及 CER / JSR 指标测算流程设计。同时阅读并学习了多模态音频大模型相关论文与中文 ASR 常用评测方法。

二、毕设推进

1. Qwen2-Audio 本地部署与调试
本周首先在 AutoDL 租用 GPU 服务器，并完成了 Qwen2-Audio-7B-Instruct 模型的本地部署。
部署过程中主要完成：
- transformers 环境配置
- CUDA 与 torch 兼容检查
- 本地模型权重加载
- 多模态 Processor 调用
- 音频输入格式适配
- 
期间遇到多个问题，例如：

（1）模型类加载错误
初期使用：
AutoModelForCausalLM
导致：
Unrecognized configuration class Qwen2AudioConfig
后修改为：
Qwen2AudioForConditionalGeneration
成功解决。

（2）音频采样率错误
模型要求输入采样率为 16000Hz，但部分音频为 24000Hz。
后续采用 librosa.load(audio_path, sr=16000) 统一重采样解决。

（3）Processor 输入参数问题
初期错误使用：
audios=[audio_path]
导致 processor 忽略音频输入。
后改为 waveform 输入方式并成功完成音频推理。

2. 批量实验脚本开发
为了完成大规模实验，本周开发了完整的批量实验脚本，实现：
- 自动递归遍历子文件夹
- 自动识别分类标签
- 批量 ASR 转写
- 大模型指令执行
- CSV 自动保存
- 自动统计实验结果
- 实验结束邮件通知
实验结果保存格式如下：
audio_file | label | transcription | model_response
同时实现了：
- 自动保存分类 CSV
- 自动统计平均响应长度
- 自动记录实验耗时

3. CER 评测流程设计
为了后续论文实验评测，本周进一步完成了：
（1）Ground Truth 对齐
将：
- 原始 prompt
- category
与模型转写结果进行自动对齐。

（2）中文 ASR 文本标准化
考虑到中文 ASR 中：
- 简繁体混杂
- 标点差异
- 空格差异
会严重影响 CER。
因此增加：
- OpenCC 简繁转换
- 去标点
- 去空格
- 全角半角统一
- 中文数字归一化
最终生成标准化文本列。
（3）CER 自动测算
基于 jiwer.cer 实现：
- 每条数据 CER
- 每个 category 平均 CER
- 每个文件平均 CER
最终自动汇总生成：
all_results_CER.csv
用于论文实验分析。

三、专业学习
本周主要学习内容为：
1. 多模态音频大模型

重点学习：
- Qwen2-Audio
- Whisper
- Audio Flamingo
等音频大模型相关论文。
主要了解：
- 音频编码方式
- 多模态对齐方法
- 指令跟随能力
- ASR 与 LLM 的结合方式

2. 中文 ASR 常见评测指标
重点学习：
CER（Character Error Rate）
CER = (S + D + I) / N
其中：
- S：替换
- D：删除
- I：插入
- N：原始字符数
同时学习了中文 ASR 常见文本预处理流程。

3. Jailbreak Success Rate（JSR）
学习了音频攻击场景中的：
- 越狱成功率
- 指令执行率
- 语义相似度评测
并尝试采用 Sentence-BERT 语义相似度进行自动化 JSR 测算。

四、遇到的问题与解决方法

1. 模型始终返回固定回复
问题：
    模型对所有音频均返回：I cannot access files on your device
原因：
    音频未正确传入 processor。
解决：
    采用 waveform 输入方式并修正 chat template。

2. 输出格式不稳定
问题：
    模型有时输出：
这段音频的内容是……
    有时输出 JSON 格式。
解决：
    增加异常处理逻辑与文本解析 fallback。

3. CER 虚高
问题：
    模型输出存在繁体中文，导致 CER 偏高。
解决：
    使用 OpenCC 统一转换为简体中文后再计算 CER。

五、实践总结
    本次实践周中，我主要围绕毕业设计中的音频大模型实验平台展开工作，完成了从模型部署、批量实验、结果保存到评测指标计算的一整套流程。相比之前仅能进行单条音频测试，本周已经实现了支持数千条音频数据自动化处理的实验框架，为后续论文实验提供了重要基础。
    在模型部署过程中，我进一步熟悉了 HuggingFace Transformers 的多模态模型调用方式，并对 Qwen2-Audio 的输入格式、Processor 机制以及音频预处理流程有了更深入的理解。同时，通过实际调试，也积累了较多 GPU 环境配置、CUDA 兼容以及大模型推理优化的经验。
    此外，在实验评测方面，我系统学习了中文 ASR 常见的 CER 指标及其计算方式，并结合中文场景特点，对文本进行了简繁转换、去标点、数字归一化等标准化处理，使实验结果更加科学可靠。同时还初步尝试了基于语义相似度的 JSR 测算方法，为后续音频攻击与越狱实验评估打下了基础。

总体来看，本次实践周不仅推进了毕业设计的核心实验内容，也提升了我在大模型工程实践、数据处理以及实验评测方面的综合能力，对后续论文撰写和实验开展具有重要意义。
