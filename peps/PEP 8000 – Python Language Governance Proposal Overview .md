# PEP 8000 – Python Language Governance Proposal Overview  
PEP 8000 – Python 语言治理提案概述

> 原文地址： https://peps.python.org/pep-8000/

Author:  作者：

Barry Warsaw <barry at python.org>

Status:  状态：

Final  最终

Type:  类型：

Informational  信息性

Topic:  主题：

[Governance  治理](../topic/governance/)

Created:  创建：

24-Aug-2018  2018-08-24

---

Table of Contents  目录

-   [Abstract  摘要](#abstract)
-   [Copyright  版权](#copyright)

## [Abstract  摘要](#abstract)

This PEP provides an overview of the selection process for a new model of Python language governance in the wake of [Guido’s retirement](https://mail.python.org/pipermail/python-committers/2018-July/005664.html). Once the governance model is selected, it will be codified in [PEP 13](../pep-0013/ "PEP 13 – Python Language Governance").  
本 PEP 概述了 Guido 退休后选择新的 Python 语言治理模式的流程。一旦治理模式选定，它将被编入 PEP 13。

Here is a list of PEPs related to the governance model selection process. PEPs in the lower 8000s describe the general process for selecting a governance model.  
以下是与治理模式选择流程相关的 PEP 列表。8000 系列下方的 PEP 描述了选择治理模式的一般流程。

-   [PEP 8001](../pep-8001/ "PEP 8001 – Python Governance Voting Process") - Python Governance Voting Process  
    PEP 8001 - Python 治理投票流程
    
    This PEP describes how the vote for the new governance model will be conducted. It outlines the voting method, timeline, criteria for participation, and explicit list of eligible voters.  
    本 PEP 描述了新治理模型的投票方式。它概述了投票方法、时间表、参与标准以及合格投票者的明确名单。
    
-   [PEP 8002](../pep-8002/ "PEP 8002 – Open Source Governance Survey") - Open Source Governance Survey  
    PEP 8002 - 开源治理调查
    
    Surveys will be conducted of governance models for similar open source and free software projects, and summaries of these models will be outlined in this PEP. These surveys will serve as useful barometers for how such projects can be successfully governed, and may serve as inspiration for Python’s own governance model. Python is unique, so it’s expected that it will have its own spin on governance, rather than directly adopting any of those surveyed.  
    将对类似的开源和自由软件项目的治理模型进行问卷调查，并在本 PEP 中概述这些模型的总结。这些调查将作为衡量此类项目如何成功治理的有用指标，并可能为 Python 自身的治理模型提供灵感。Python 是独一无二的，因此预计它将有自己的治理方式，而不是直接采用调查中的任何一种模型。
    

PEPs in the 801Xs describe the actual proposals for Python governance. It is expected that these PEPs will cover the broad scope of governance, and that differences in details (such as the size of a governing council) will be covered in the same PEP, rather than in potentially vote-splitting individual PEPs.  
801X 系列的 PEP 描述了 Python 治理的实际提案。预计这些 PEP 将涵盖治理的广泛范围，而细节上的差异（例如治理委员会的大小）将在同一 PEP 中涵盖，而不是在可能造成投票分裂的单独 PEP 中。

-   [PEP 8010](../pep-8010/ "PEP 8010 – The Technical Leader Governance Model") - The Technical Leader Governance Model  
    PEP 8010 - 技术领导者治理模式
    
    This PEP proposes a continuation of the singular technical project leader model. Also within scope is whether an advisory council aids or supports the BDFL. This PEP does _not_ name either the next BDFL, nor members of such an advisory council. For that, see PEP 13.  
    本 PEP 提议继续采用单一技术项目负责人模式。同时，本 PEP 也涵盖了顾问委员会是否协助或支持 BDFL 的范围。本 PEP 并未指明下一任 BDFL 或顾问委员会的成员。具体内容请参阅 PEP 13。
    
-   [PEP 8011](../pep-8011/ "PEP 8011 – Python Governance Model Lead by Trio of Pythonistas") - Python Governance Model Lead by Trio of Pythonistas  
    PEP 8011 - 由三位 Python 专家领导的 Python 治理模式
    
    This PEP describes a new model of Python governance lead by a Trio of Pythonistas (TOP). It describes the role and responsibilities of the Trio. This PEP does _not_ name members of the Trio. For that, see [PEP 13](../pep-0013/ "PEP 13 – Python Language Governance").  
    本 PEP 描述了一种由三位 Python 专家（TOP）领导的 Python 治理新模式。它阐述了三位专家的角色和职责。本 PEP 并未指明三位专家的成员。具体内容请参阅 PEP 13。
    
-   [PEP 8012](../pep-8012/ "PEP 8012 – The Community Governance Model") - The Community Governance Model  
    PEP 8012 - 社区治理模型
    
    This is a placeholder PEP for a new model of Python governance based on consensus and voting, without the role of a centralized singular leader or a governing council. It describes how, when, and why votes are conducted for decisions affecting the Python language. It also describes the criteria for voting eligibility.  
    这是一个关于基于共识和投票的全新 Python 治理模型的占位符 PEP，不涉及中央单一领导者或治理委员会的角色。它描述了如何、何时以及为何进行投票来决定影响 Python 语言的决策，同时也描述了投票资格的标准。
    
-   [PEP 8013](../pep-8013/ "PEP 8013 – The External Council Governance Model") - The External Governance Model  
    PEP 8013 - 外部治理模型
    
    This PEP describes a new model of Python governance based on an external council who are responsible for ensuring good process. Elected by the core development team, this council may reject proposals that are not sufficiently detailed, do not consider all affected users, or are not appropriate for the upcoming release. This PEP does _not_ name members of such a council. For that, see [PEP 13](../pep-0013/ "PEP 13 – Python Language Governance").  
    该 PEP 描述了一种基于外部委员会的 Python 治理模型，该委员会负责确保良好流程。由核心开发团队选举产生，该委员会可能会拒绝那些不够详细、未考虑所有受影响用户或不适合即将发布的版本的提案。该 PEP 并未指明此类委员会的成员。具体内容请参见 PEP 13。
    
-   [PEP 8014](../pep-8014/ "PEP 8014 – The Commons Governance Model") - The Commons Governance Model  
    PEP 8014 - 公共治理模型
    
    This PEP describes a new model of Python governance based on a council of elders who are responsible for ensuring a PEP is supported by a sufficient majority of the Python community before being accepted. Unlike some of the other governance PEPs it explicitly does _not_ specify who has voting rights and what a majority vote consists of. In stead this is determined by the council of elders on a case-by-case basis.  
    本 PEP 描述了一种基于长老理事会的新型 Python 治理模型，该理事会负责确保在 PEP 被接受之前，它得到了 Python 社区足够多数人的支持。与其他一些治理 PEP 不同，它明确不指定谁拥有投票权以及多数票的构成。相反，这一点由长老理事会根据具体情况进行决定。
    
-   [PEP 8015](../pep-8015/ "PEP 8015 – Organization of the Python community") - Organization of the Python community  
    PEP 8015 - Python 社区的组织结构
    
    This PEP formalizes the current organization of the Python community and proposes 3 main changes: formalize the existing concept of “Python teams”; give more autonomy to Python teams; replace the BDFL (Guido van Rossum) with a new “Python board” of 3 members which has limited roles, mostly decide how a PEP is approved (or rejected).  
    本 PEP 规范了当前 Python 社区的组织结构，并提出了三项主要变更：正式化现有的“Python 团队”概念；赋予 Python 团队更多自主权；用一个新的由 3 名成员组成的“Python 委员会”取代 BDFL（吉多·范罗苏姆），该委员会具有有限的角色，主要决定 PEP 的批准（或拒绝）方式。
    
-   [PEP 8016](../pep-8016/ "PEP 8016 – The Steering Council Model") - The Steering Council Model  
    PEP 8016 - 指导委员会模型
    
    This PEP proposes a model of Python governance based around a steering council. The council has broad authority, which they seek to exercise as rarely as possible; instead, they use this power to establish standard processes, like those proposed in the other 801x-series PEPs. This follows the general philosophy that it’s better to split up large changes into a series of small changes that can be reviewed independently: instead of trying to do everything in one PEP, we focus on providing a minimal-but-solid foundation for further governance decisions.  
    本 PEP 提议了一种基于指导委员会的 Python 治理模型。该委员会拥有广泛的权力，他们希望尽可能少地行使这些权力；相反，他们使用这些权力来建立标准流程，例如在其他 801x 系列 PEP 中提出的过程。这遵循了这样一种普遍的哲学：将大的变更分解为一系列小的、可以独立审查的变更会更好：我们不是试图在一个 PEP 中做所有事情，而是专注于为未来的治理决策提供一个最小但坚实的基础。
    

Additional governance models may be added before the final selection.  
在最终选择之前，可能会添加其他治理模型。

## [Copyright  版权](#copyright)

This document has been placed in the public domain.  
这份文档已置于公共领域。

---

Source: [https://github.com/python/peps/blob/main/peps/pep-8000.rst](https://github.com/python/peps/blob/main/peps/pep-8000.rst)  
来源：https://github.com/python/peps/blob/main/peps/pep-8000.rst

Last modified: [2025-02-01 08:55:40 GMT](https://github.com/python/peps/commits/main/peps/pep-8000.rst)  
最后修改时间：2025-02-01 08:55:40 GMT