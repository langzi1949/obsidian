
#AI  #agent  #大模型

# 1. 首先是学习计划

除了之前的基础4大件之外，还需要学习AI相关的课程

## 1.1 分阶段：
1. 重学python
2. AI入门    学习经典的ML课程和深度学习
3. AI工程化      部署一个Web AI服务


## 1.2  核心技术栈

1. 编程语言  ：  python（必须） C++(模型优化)  Rust（可选）
2. 数学    没必要学习那么多了，自己学会『程序员掌握的数学』即可
3. ML基础  ： 机器学习
4. 深度学习：  PyTorch /  TensorFlow / JAX
5. 大模型  ：   LLM微调  、 RAG  、Agent
6. 部署    ： GPU推理    边缘推理等

## 1.3 推荐书籍

1. 《机器学习》  周志华版本
2. 《深度学习》  
3. 《Python机器学习》
4. 《Transformer从原理到实践》

## 1.4 实战路径

![[Pasted image 20251031110602.png]]

## 1.5 一句话总结

想用Python写模型，再用C++加速推理，最后用MLOps管起来。


# 2. 什么是AI Agent?

简单来说，AI Agent就是 <span class="b"> AI Agent是一个具备自主决策、规划和使用工具的能力，能够独立完成复杂任务的AI系统</span>

## 2.1 AI Agent的核心机制

1. **大语言模型（LLM）** :  大脑/推理核心：负责理解目标、分解任务、指定推理路径。  相当于人的思考、理解指令
2. **规划（Planning）**：  将复杂任务分解为一系列可执行的子任务，并选择执行顺序。  相当于人指定规划、拆解任务
3. **记忆（Memory）**：  短期记忆（当前对话的上下文）和长期记忆（知识库、向量数据库），用于知识和经验积累。 相当于人的学习、记忆、回顾历史
4. **工具（Tool Use）**： 行动能力，调用外部API，运行代码，搜索网页，访问数据库等，以拓展LLM能力。 相当于人使用工具
5. **行动、观察（Action、Observation）**：执行计划中的步骤，并从环境中获取反馈，将反馈传回给LLM进行下一步推理。相当于人的执行任务、检查结果
## 2.2 AI Agent的工作流程

一个经典的**ReAct**流程，Reason - Act - Observe  循环的流程图

```
graph TD
    A[客户/用户发起请求] --> B{Agent 接收指令};
    B --> C{LLM (Agent 大脑) 进行推理和规划};
    C -- 步骤分解/判断 --> D{需要外部工具?};

    %% 循环路径
    D -- 是 (需要行动) --> E[行动: LLM选择并调用外部工具/API];
    E --> F[观察: 接收外部工具的执行结果];
    F --> C; %% 观察结果反馈给LLM，进行下一轮推理

    %% 结束路径
    D -- 否 (任务完成/无需工具) --> G[LLM 总结结果];
    G --> H[Agent 向客户/用户返回最终结果];
```

![[Pasted image 20251031140052.png]]

详细步骤分析

1. 步骤1：感知与目标设定（客户->Agent）
	1. 执行者：客户、用户
	2. 动作：用户发起一个负责开放式的请求 ，比如：『帮我查询一下2025年Q3我们公司的销售数据，并总结一份PDF』
	3. Agent状态：Agent接收该非结构化的请求，并将其转化为一个内部任务目标
2. 步骤2：推理和规划 （Agent内部的LLM）
	1. 执行者： LLM
	2. 动作：LLM根据指定和其自身拥有的知识
		1. 分解任务： a. 查询2025年Q3的公司销售数据  b. 销售数据  c. 生成PDF
		2. 制定计划：决定第一步需要调用哪个工具 （例如：数据库查询工具）
	3. Agent状态：生成下一轮要执行的行动Action指令
3. 步骤3：行动与工具调用（Agent -> 外部工具）
	1. 执行者：Agent的执行模块
	2. 动作：Agent根据LLM的指令，调用相应的外部工具
4. 步骤4：观察与反馈（外部工具 -> Agent内部LLM）
	1. 执行者：外部工具
	2. 动作：外部的工具将执行工具，将结果返回Agent
	3. 循环：Agent将这个观察结果反馈给LLM，流程回到步骤2，LLM进行下一轮推理
5. 步骤5：完成与总结（LLM -> Agent -> 客户）
	1. 执行者： LLM（Agent -> 客户）
	2. 动作：当所有子任务执行完毕，LLM意识到目标已达成，它将最终结果进行整理和格式化
	3. Agent格式：Agent将格式化后的最终结果返回给最初发起请求的客户

## 2.3 AI Agent的主要应用领域

AI Agent的目标是实现**工作流的自动化**。

1. 营销与销售
2. 客户服务
3. 软件开发
4. 数据分析与研究
5. 个人助理

## 2.4 相关技术栈

现代AI Agent目前分为三个层次：**模型层**、**编排层（框架）** 以及**应用层（数据与工具）**

1. 模型层。这个是驱动AI Agent进行推理和规划的核心
	1. 闭源模型，比如OpenAI 等
	2. 开源模型  比如Llama3 、Deepseek等
2. 编排层。 这些框架提供了构建Agent所需的结构、记忆和工具集成能力。
	1. LangChain    目前主流的框架。
	2. LangGraph   主要是专注于图结构和多步骤循环，构建复杂的、有状态的Agent工作流

#  3. 什么是RAG？

RAG (Retrieval-Augmented-Generation)  ------- 检索增强生成
它解决了LLM的两个核心缺陷
1. 知识截止日期：LLM的知识只停留在其训练数据的截止日期之前。
2. 幻觉     ：   LLM有时会一本正经的胡说八道

RAG的核心思想就是： ==在LLM生成回答之前，先将外部权威的、最新的知识库中的检索相关信息，然后将这些信息作为上下文一起喂给LLM，引导它生成基于事实的准确答案。==


RAG是一种将**信息检索系统**和**文本生成模型**结合起来的架构。
1. 检索Retrieval阶段： 系统根据用户的问题，从公司的私有文档、数据库或者最新的网页中，找到相关的信息或者『证据』
2. 增强Augmented阶段：系统将用户的原始问题和检索到的证据一起打包成一个超级提示词，发送给LLM
3. 生成Generation阶段：LLM严格根据提供的证据来组织，总结和生成最终答案

## 3.1 RAG的工作流程

RAG的工作流程主要分为两个环节：数据准备环节（一次性或者定期）和查询环节（实时）

```
graph TD
    A[客户/用户发起查询 (Q)] --> B{RAG 系统接收查询};
    
    subgraph 检索增强环节 (RAG)
        B --> C[步骤 1: 向量化查询 (Q')];
        C --> D[步骤 2: 向量相似度搜索];
        D --> E[步骤 3: 提取相关上下文/证据 (D)];
        E --> F[步骤 4: 构建增强 Prompt];
        F --> G[步骤 5: LLM 根据 Prompt 生成答案];
    end
    
    G --> H[RAG 系统向客户返回最终答案];

    style A fill:#f9f,stroke:#333
    style H fill:#f9f,stroke:#333
    style G fill:#ccf,stroke:#333
```


# 5. 几个AI模型的benchmark

1. [https://www.swebench.com/](https://www.swebench.com/)
2. [imarena benchmark](https://lmarena.ai/)
3. [基于费用和准确率平衡的benchmark](https://aider.chat/docs/leaderboards/)
4. [LiveCode Benchmark](https://livecodebench.github.io/leaderboard.html)
5. 比较好的一个模型API  [openrouter](https://openrouter.ai/)

# 6. Java转AI的可行路径
### 🗺️ 第一阶段：核心理论与语言基础 (60% 理论)

您的 $\text{Java}$ 工程能力是巨大的资产，但 $\text{AI}$ 的核心在数学和算法。

#### 1. 弥补数学和统计学知识

* **线性代数：** 矩阵、向量、特征值，理解数据如何在空间中表示和变换（如神经网络的计算基础）。
* **概率论与数理统计：** 概率分布、贝叶斯定理、假设检验，理解模型的不确定性和评估方法。
* **微积分：** 尤其是**梯度**和**导数**，理解反向传播 ($\text{Backpropagation}$) 和优化器的原理。

#### 2. 学习 Python 及其科学计算库

尽管 $\text{Java}$ 在 $\text{AI}$ 部署中有用，但**模型训练和研究**几乎都依赖 $\text{Python}$。

* **$\text{Python}$ 基础：** 快速掌握 $\text{Python}$ 语法和特性。
* **科学计算核心库：** **$\text{NumPy}$** (处理数组和矩阵)、**$\text{Pandas}$** (数据处理和分析)、**$\text{Matplotlib}$/$\text{Seaborn}$** (数据可视化)。

#### 3. 掌握机器学习 (ML) 基础

* **入门算法：** 线性回归、逻辑回归、决策树、聚类 ($\text{K}$-$\text{Means}$)。
* **核心概念：** 欠拟合/过拟合、偏差/方差权衡、交叉验证、模型评估指标 ($\text{Accuracy}$、$\text{Precision}$、$\text{Recall}$、$\text{F1}$ $\text{Score}$)。
* **框架：** 熟练使用 **$\text{Scikit}$-$\text{learn}$** 完成传统的 $\text{ML}$ 任务。

---

### 🚀 第二阶段：深入深度学习与工程实践 (60% 实践)

这是进入现代 $\text{AI}$ 领域的关键，重点是深度学习 ($\text{DL}$) 模型。

#### 1. 深度学习核心

* **主流框架：** 掌握 **$\text{PyTorch}$** 或 **$\text{TensorFlow}$** 至少一个。$\text{PyTorch}$ 在研究领域更受欢迎，但 $\text{TensorFlow}$ 在工业界部署方面有优势。
* **网络结构：** 理解**全连接网络** ($\text{FNN}$)、**卷积神经网络** ($\text{CNN}$，用于图像)、**循环神经网络** ($\text{RNN}$ / $\text{LSTM}$ / $\text{GRU}$，用于序列数据)。

#### 2. 专注于 MLOps 或 LLM/Agent

作为 $\text{Java}$ 工程师，您天然适合从事 **$\text{ML}$ 模型的生产化部署** ($\text{MLOps}$) 或最新的 **大模型应用开发**。

| 领域 | 描述 | $\text{Java}$ 技术栈关联 |
| :--- | :--- | :--- |
| **MLOps & 部署** | 模型训练、版本控制、服务化部署、性能监控。 | 使用 $\text{Spring}$ $\text{Boot}$ 或 $\text{Quarkus}$ **封装 $\text{Python}$ 模型**，通过 $\text{REST}$ $\text{API}$ 提供推理服务。 |
| **$\text{LLM}$ 应用** | 构建基于大语言模型 ($\text{LLM}$) 的应用，如检索增强生成 ($\text{RAG}$) 和 $\text{AI}$ $\text{Agent}$。 | **$\text{LangChain4J}$** 或 **$\text{Spring}$ $\text{AI}$**：这些库允许您在纯 $\text{Java}$ 环境中调用、编排和管理各种大模型。 |

#### 3. $\text{Java}$ $\text{AI}$ 部署框架

发挥您的 $\text{Java}$ 优势，专注于使用 $\text{Java}$ 库进行**模型推理**和**应用集成**：

* **$\text{Deep}$ $\text{Java}$ $\text{Library}$ ($\text{DJL}$):** 亚马逊开源的深度学习库，与引擎无关，允许 $\text{Java}$ 开发者轻松进行模型推理和 $\text{CV}$/$\text{NLP}$ 任务。
* **$\text{Eclipse}$ $\text{Deeplearning4J}$ ($\text{DL4J}$):** 强大的纯 $\text{Java}$ 深度学习框架，可用于训练和部署。
* **$\text{ONNX}$/$\text{PMML}$：** 学习如何将 $\text{Python}$ 训练的模型转换为这些中间格式，然后使用 $\text{Java}$ 运行时（如 $\text{ONNX}$ $\text{Runtime}$）进行加载和推理。

---

### ✅ 总结路径

1.  **打好地基：** 补习数理基础 $\rightarrow$ 掌握 $\text{Python}$ 和 $\text{NumPy}$。
2.  **入门 $\text{ML}$：** 熟悉 $\text{Scikit}$-$\text{learn}$ 常用算法。
3.  **转入 $\text{DL}$：** 学习 $\text{PyTorch}$ 或 $\text{TensorFlow}$。
4.  **发挥优势：** 专注于使用 **$\text{DJL}$** 或 **$\text{LangChain4J}$** 在 $\text{Java}$ 后端中集成和部署 $\text{AI}$ 模型。
