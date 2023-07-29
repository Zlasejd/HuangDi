# HuangDi（original name: zhongjing）-该名字源自中医古籍《黄帝内经》
介绍：首先在 [Ziya-LLaMA-13B-V1](https://huggingface.co/IDEA-CCNL/Ziya-LLaMA-13B-v1)基线模型的基础上加入中医教材、中医各类网站数据等语料库，训练出一个具有中医知识理解力的预训练语言模型（pre-trained model），之后在此基础上通过海量的中医古籍指令对话数据及通用指令数据进行有监督微调（SFT），使得模型具备中医古籍知识问答能力。<br>
# 训练数据
## 继续预训练数据（纯文本语料）
包含两部分：①中医教材数据：收集“十三五”规划所有中医教材共22本，约17.9MB。②在线中医网站数据：爬取中医世家、民间医学网等在线中医网站及知识库，共收集文本数据约220MB。
## 通用指令微调数据
[Alpaca-GPT4](https://github.com/Instruction-Tuning-with-GPT-4/GPT-4-LLM) 52k 中文
## 中医古籍指令对话数据
### 语料库来源
以《中华医典》数据库为语料来源，约338MB，由两部分组成：①非结构化的“古籍文本”：涵盖了886本标点符号及内容完整的中医古籍。②结构化的“古籍辞典”：包含“名医”、“名言”、“名词”、“名著”等六大类，由中医学界诸多知名学者对中医古籍内容知识进一步系统提炼整理，是中医古籍内容精华最为直接的集中体现。<br>
### 构建指令微调对话数据集
通过知识引导的指令数据生成和指令数据质量优化两个阶段，最终获得504372个单轮对话数据。<br>
(1)知识引导的指令数据生成<br>
让ChatGPT基于对该段中医古籍的知识内容理解，模拟用户与AI，通过自问自答的方式，生成逻辑关系相关的若干问题和答案，从而保证对话数据的准确性和可靠性。<br>
(2)指令数据质量优化<br>
尽管基于知识引导使得生成的指令数据基于特定领域，并且与所提供的无监督文本内容相关，避免了模型内部“已有知识”的干扰。然而这种方法难以对数据质量进行监督和控制，也难以保证指令数据的多样性和难度，这可能导致大模型对指令数据集的过度拟合。为了解决这个问题，我们在现有指令数据集的基础上，通过指令数据过滤-指令数据整合两个阶段对数据进行二次优化。
#### 中医古籍指令数据种类概览（共504372条对话数据）
对话类型  |数量 |对话类型 |数量   
 ---- | ----- |-----|------
 医经理论  | 40410 |温病类  | 10288
 诊法治法  | 14184 |综合医书  | 86413  
 本草方书  | 144993|临证各科  | 56872
 针灸推拿灸  | 31009|中医问诊  | 76765
 伤寒金匮  | 29602|预防养生  | 13836
# 训练细节
## 实验参数

超参数  | 继续预训练  | 有监督微调
 ---- | ----- | ------  
 learning_rate  | 6e-5 | 3e-4 
 lora_rank  | 16 | 16   
 num_train_epochs  | 5 | 6  
 per_device_train_batch_size  | 8 | 16 
 per_device_eval_batch_size  | - | 16   
 gradient_accumulation_steps  | 4 | 4
 lr_scheduler_type  | cosine | cosine 
 logging_steps  | 1000 | 100   
 save_steps  | 2000 | 5000  
 train_runtime  | 296767.9767 | 160986.1262 
 eval_runtime  | - | 296.5641   
 train_loss  | 1.456 | 1.245
 eval_loss  | - | 1.258
# 模型下载-LoRA权重
模型名称  |大小|下载地址 
 ---- | ----- |-----
 HuangDi | 25.05MB |[百度网盘](https://pan.baidu.com/s/1yLjzeRSW8YB6x4SarU-QyA?pwd=i7g6 )
# 模型效果
应用示例  |Input|target 
 ---- | ----- |-----
 医经理论|中医中的脾胃有什么关系？|黄帝: 脾胃是中医理论中的两大器官，它们之间存在着密切的联系。脾主运化，负责消化饮食；胃主纳腐熟，负责消化食物。脾胃功能失调会导致身体出现各种问题，包括腹泻、便秘、贫血等。因此，保持脾胃健康对于人体健康至关重要。
 诊法治法|如何判断一个人阴阳失调？|黄帝: 阴阳失调是中医理论中的重要概念，它指的是身体内部阴阳两种力量之间的不平衡。通常来说，判断一个人是否存在阴阳失调需要从以下几方面入手：1.观察外貌特征：阴虚、阳盛型的患者通常容易出现脸色苍白、瘦弱等症状；而阳虚、阴盛型的患者则可能表现为面色红润、肥胖等情况。2.检查舌苔：中医认为舌质和舌苔可以反映脏腑功能的变化。例如，阴虚型患者的舌质可能较薄，舌苔可能较厚；而阳虚型患者的舌质可能较厚，舌苔可能较薄。3.询问病史：了解患者过去是否有过类似疾病或者长期服用某些药物也可以帮助诊断阴阳失调。4.检测体温：阴虚型患者往往会出现低热、寒战等症状，而阳虚型患者则可能出现高热、汗多等情况。
本草方书|薄荷的功效？|黄帝: 薄荷是一种常见的中草药，其主要成分包括芳香油、芳香醇、芳香酸等。它具有清热解毒、止咳平喘、祛痰止痛等功效。常用于治疗感冒、头痛、喉咙痛、流感等病症。此外，薄荷还可以促进血液循环，增强免疫力，预防心血管疾病等。
中医问诊|中医针灸可以治疗肚子疼么？哪些穴位可以治疗?|黄帝: 是的，中医认为肚子疼常常与脾胃功能失调有关。中医针灸作为一种传统的中医治疗方法，对于治疗肚子疼也有很好的效果。在中医理论上，不同的肚子疼痛症状和病因可以采用不同的穴位进行刺激来缓解。例如，对于脾虚型肚子疼，可以选择脾俞、大椎等穴位；而对于肝郁型肚子疼，则可以选择太冲、足三里等穴位。此外，还有一些特定的穴位组合，如神阙穴加气海穴或三阴交穴等，也被广泛应用于治疗肚子疼痛。   
# 项目参与者
本项目由南京大学信息管理学院（黄奇老师课题组）及郑州大学人工智能学院合作完成。
# 致谢
本项目的开放过程中，获得了以下项目的帮助，在此表示感谢。<br>
1.https://huggingface.co/IDEA-CCNL/Ziya-LLaMA-13B-v1<br>
2.https://github.com/ymcui/Chinese-LLaMA-Alpaca<br>
3.https://github.com/huggingface/peft<br>
# 局限性和使用限制
本项目内容仅供用于学术研究，不得用于其他会对社会带来危害的用途。使用涉及第三方代码的部分时，请严格遵循相应的开源协议。<br>

本项目中使用的数据由ChatGPT生成，未经严格验证，可能会存在错误内容，在使用时请注意甄别。<br>

模型生成的内容受模型计算、随机性和量化精度损失等因素影响，本项目无法对其准确性作出保证。本项目中的模型输出并非专业中医古籍咨询结果，可能会包含错误内容。<br>

如需商业用途可联系：1049506221@qq.com
# 引用
如果您使用了本项目的数据或者代码，或者认为本项目对您的研究有帮助，请引用本项目。

    @misc{zhang2023huangdi,
      title={HuangDi: 中医古籍生成式大语言模型的构建研究},
      author={Jundong Zhang and Songhua Yang },
      year={2023},
      url={https://github.com/Zlasejd/HuangDi}
    }  
