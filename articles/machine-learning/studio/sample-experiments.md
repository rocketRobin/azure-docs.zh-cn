---
title: "复制机器学习示例试验 - Azure | Microsoft Docs"
description: "了解如何通过 Cortana Intelligence 库和 Microsoft Azure 机器学习使用示例机器学习试验来创建新试验。"
keywords: "机器学习示例, 示例试验, 示例性机器学习"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2017
ms.author: cgronlun
ms.openlocfilehash: f798ac1b46d702dbb96a2384d96f2d14eb3cac0e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="copy-example-experiments-to-create-new-machine-learning-experiments"></a>复制示例试验以创建新的机器学习试验
了解如何从“[Cortana Intelligence 库](https://gallery.cortanaintelligence.com/)”启动示例试验，而不是从头开始创建机器学习试验。 可以使用这些示例构建自己的机器学习解决方案。

此库存有 Microsoft Azure 机器学习团队提供的示例试验，以及由机器学习社区共享的示例。 此外，还可以提出问题或发布有关试验的评论。

若要了解如何使用此库，请观看[适合初学者的数据科学](data-science-for-beginners-the-5-questions-data-science-answers.md)系列中 3 分钟的视频[复制其他人的工作来进行数据科学](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-cortana-intelligence-gallery"></a>在 Cortana Intelligence 库中查找要复制的试验
要查看哪些试验可用，请转到“ [库](https://gallery.cortanaintelligence.com/) ”，并单击页面顶部的“ **试验** ”。

### <a name="find-the-newest-or-most-popular-experiments"></a>查找最新或最受欢迎的试验
在此页上，可以查看**最近添加**的试验，或向下滚动查看**热门**或最新的**热门 Microsoft 试验**。

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>查找满足特定要求的试验
若要浏览所有试验︰

1. 请单击页面顶部的“ **浏览全部** ”。
2. 在左侧“类别”部分的“优化依据”下，选择“试验”以查看库中的所有试验。
3. 可以通过多种方式找到满足要求的试验︰
   * **选择左侧的筛选器。** 例如，若要浏览使用基于 PCA 的异常情况检测算法的试验，可选择“类别”下的“试验”，然后单击“全部显示”。 然后，在“所用算法”下选择“基于 PCA 的异常情况检测”。 <br></br>
     ![选择筛选器](./media/sample-experiments/refine-the-view.png)
   * **使用搜索框。** 例如，若要查找由 Microsoft 提供且与使用双类支持向量机算法的数字识别相关的试验，请在搜索框中输入“数字识别”。 然后，选择筛选器“试验”、“仅限 Microsoft 内容”和“双类支持向量机”：<br></br>
     ![使用搜索框](./media/sample-experiments/search-for-experiments.png)
4. 单击一个试验以了解其更多相关信息。
5. 若要运行和/或修改试验，请在试验的页面上单击“ **在工作室中打开** ”。 <br></br>

    ![示例试验](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > 首次在机器学习工作室中打开一个试验时，可以免费尝试，也可以购买 Azure 订阅。 [了解机器学习工作室免费试用版和付费服务版](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>使用示例作为模板，创建新的试验
还可以使用库中的示例作为模板在机器学习工作室中创建新试验。

1. 使用 Microsoft 帐户凭据登录到[工作室](https://studio.azureml.net)，并单击“新建”以创建新试验。
2. 浏览示例内容并单击其中一个。

使用该示例试验作为模板，在机器学习工作室工作区中创建一个新试验。

## <a name="next-steps"></a>后续步骤
* [从各种源导入数据](import-data.md)
* [适用于机器学习的 R 语言快速入门教程](r-quickstart.md)
* [部署机器学习 Web 服务](publish-a-machine-learning-web-service.md)
