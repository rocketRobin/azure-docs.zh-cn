---
title: "为 Azure 机器学习服务（预览版）部署模型 | Microsoft Docs"
description: "本完整教程端到端演示如何使用 Azure 机器学习服务（预览版）。 这是有关部署模型的第 3 部分。"
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/27/2017
ms.openlocfilehash: 048d734277f855086a48ad00a52b873adbf419b4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="classifying-iris-part-3-deploy-a-model"></a>“鸢尾花”分类第 3 部分：部署模型
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是由三个部分构成的系列教程的第三部分。 在本教程部分，我们使用 Azure 机器学习服务（预览版）来了解以下操作：

> [!div class="checklist"]
> * 找到模型文件
> * 生成评分脚本和架构文件
> * 准备环境
> * 创建实时 Web 服务
> * 运行实时 Web 服务
> * 检查输出 Blob 数据 

 为方便起见，本教程使用了永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/iris_flower_data_set)。 屏幕截图与 Windows 相关，但 macOS 上的体验几乎相同。

## <a name="prerequisites"></a>先决条件
先完成本系列教程的前两个部分：

1. 遵循[准备数据教程](tutorial-classifying-iris-part-1.md)创建 Azure 机器学习资源，并安装 Azure Machine Learning Workbench 应用程序。

2. 遵循[生成模型教程](tutorial-classifying-iris-part-2.md)在 Azure 机器学习中创建逻辑回归模型。

3. 需安装 Docker 引擎并在本地运行它。 也可以将其部署到 Azure 中的 Azure 容器服务群集。

## <a name="download-the-model-pickle-file"></a>下载模型 pickle 文件
在本教程的前一部分中，已在 Azure Machine Learning Workbench 本地运行了 **iris_sklearn.py** 脚本。 该操作已使用流行的 Python 对象序列化包 **[pickle](https://docs.python.org/2/library/pickle.html)** 序列化了逻辑回归模型。 

1. 启动 **Azure Machine Learning Workbench** 应用程序，打开在本系列教程前一部分中创建的 **myIris** 项目。

2. 打开该项目后，单击 Azure Machine Learning Workbench 左侧工具栏上的“文件”按钮（文件夹图标），以便在项目文件夹中打开文件列表。

3. 选择 **iris_sklearn.py** 文件，Python 代码随即会在 Workbench 中的新文本编辑器选项卡上打开。

4. 查看 **iris_sklearn.py** 文件，确定在哪个位置生成了 pickle 文件。 使用 Ctrl+F 打开查找对话框，并在 Python 代码中找到 **pickle** 一词。

   此代码片段演示 pickle 输出文件是如何生成的。 请注意，输出 pickle 文件在磁盘上命名为 **model.pkl**。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 在前一运行的输出文件中找到模型 pickle 文件。
   
   运行 **iris_sklearn.py** 脚本时，模型文件已写入名为 **model.pkl** 的 **outputs** 文件夹。 此文件夹驻留在选择用于运行脚本的执行环境中，而不是驻留在本地项目文件夹中。 
   
   - 若要找到该文件，请使用 Azure Machine Learning Workbench 应用程序，并单击左侧工具栏上的“运行”按钮（时钟图标）打开“所有运行”列表。  
   - 此时会打开“所有运行”选项卡。 在“运行”表中，选择目标为 **local** 且脚本名称为 **iris_sklearn.py** 的最近运行。 
   - 此时会打开“运行属性”页。 在页的右上角，可以看到“输出”部分。 
   - 通过选中 **model.pkl** 文件旁边的复选框并单击“下载”按钮，下载 pickle 文件。 将该文件保存到项目文件夹的根目录。 后续步骤中需要使用该文件。

   ![下载 pickle 文件](media/tutorial-classifying-iris/download_model.png)

   在[如何读取和写入大型数据文件](how-to-read-write-files.md)一文中详细了解 **outputs** 文件夹。

## <a name="get-scoring-and-schema-files"></a>获取评分和架构文件
若要连同模型文件一起部署 Web 服务，还需要一个评分脚本，以及 Web 服务输入数据的架构（可选）。 评分脚本从当前文件夹加载 **model.pkl** 文件，并使用它来生成新预测的 Iris 类。  

1. 启动 **Azure Machine Learning Workbench** 应用程序，打开在本系列教程前一部分中创建的 **myIris** 项目。

2. 打开该项目后，单击 Azure Machine Learning Workbench 左侧工具栏上的“文件”按钮（文件夹图标），以便在项目文件夹中打开文件列表。

3. 选择 **iris_score.py** 文件。 Python 脚本将会打开。 此文件用作评分文件。

   ![评分文件](media/tutorial-classifying-iris/model_data_collection.png)

4. 若要获取架构文件，请运行该脚本。 在命令栏中选择 **local** 环境和 **iris-score.py** 脚本，然后单击“运行”按钮。 

5. 该脚本在 **outputs** 文件夹中创建一个 JSON 文件，用于捕获模型所需的输入数据架构。

6. 请注意 Machine Learning Workbench 窗口右侧的“作业”窗格。 等待最新的 **iris-score.py** 作业显示绿色的“已完成”状态。 然后单击最新作业运行对应的超链接 **iris-score.py [1]**，查看 **iris-score.py** 运行的运行详细信息。 

7. 在“运行属性”页上的“输出”部分中，选择新建的 **service_schema.json** 文件。 **检查**文件，并单击“下载”。 将该文件保存到项目根文件夹。

8. 返回到在其中打开了脚本 **iris-score.py** 的前一选项卡。 

   请注意，其中使用了数据收集功能从 Web 服务捕获模型输入和预测。 以下要点专门与数据收集相关：

9. 请查看包含模型数据收集功能的文件导入类 ModelDataCollector 顶部的代码：

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. 查看 **init()** 函数中用于实例化 ModelDataCollector 的以下代码行：

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. 查看 **run(input_df)** 函数中用于收集输入和预测数据的以下代码行：

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

现在，可以准备环境来操作化模型。

>[!NOTE]
>部署模型时，需要具有 Azure 订阅的所有者访问权限。

## <a name="prepare-to-operationalize-locally"></a>在本地准备操作化
使用“本地模式”部署在本地计算机上的 Docker 容器中运行。

可以使用“本地模式”进行开发和测试。 若要完成以下步骤来操作化模型，必须在本地运行 Docker 引擎。 可以在命令末尾使用 `-h` 标志获取命令帮助。

>[!NOTE]
>如果本地没有 Docker 引擎，仍可通过在 Azure 中创建用于部署的群集来继续操作。 只是需要确保在完成教程后删除该群集，以免持续产生费用。

1. 在 Azure Machine Learning Workbench 中打开命令行接口，在“文件”菜单中单击“打开命令提示符”。

   命令行提示符会在当前项目文件夹位置 **c:\temp\myIris>** 中打开。

2. 确保已将 Azure 资源提供程序 **Microsoft.ContainerRegistry** 注册到订阅中。 在步骤 3 中创建环境之前，请注册此资源提供程序。 可使用以下命令检查是否已注册该提供程序：
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   应看到如下输出： 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   如果 **Microsoft.ContainerRegistry** 未注册，可使用以下命令注册：
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   注册可能需要几分钟时间，可以使用上述 **az provider list** 命令或以下命令检查注册状态：
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   第三行输出显示 **"registrationState": "Registering"**。 请稍候片刻并重复 show 命令，直到输出显示 **"registrationState": "Registered"**。

3. 创建环境。 需要针对每个环境运行此步骤一次（例如，针对开发环境运行一次，针对生产环境运行一次）。 对于这第一个环境，请使用“本地模式”。 （稍后，可以尝试在以下命令中使用 `-c` 或 `--cluster` 开关以“群集模式”设置环境。）

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   遵照屏幕说明预配一个用于存储 Docker 映像的存储帐户、一个用于列出 Docker 映像的 ACR（Azure 容器注册表），以及一个用于收集遥测数据的 AppInsight 帐户。 如果使用了 `-c` 开关，则也会创建 ACS（Azure 容器服务）群集。
   
   可以参考群集名称识别环境。 位置应该与在 Azure 门户中创建的模型管理帐户的位置相同。

4. 创建模型管理帐户（一次性的设置）  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. 设置模型管理帐户  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. 设置环境。
完成设置后，使用以下命令设置操作化时所需的环境变量。 使用前面在步骤 4 中所用的相同环境名称。 使用完成设置过程时在命令窗口中输出的相同资源组名称。
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. 若要验证是否为本地 Web 服务部署正确配置了操作化环境，请输入以下命令：

   ```azurecli
   az ml env show
   ```

现在，可以创建实时 Web 服务。

>[!NOTE]
>可以重复使用模型管理帐户和环境进行后续的 Web 服务部署。 不必为每个 Web 服务创建它们。 帐户或环境可以有多个与之关联的 Web 服务。

## <a name="create-a-real-time-web-service-in-one-command"></a>以一条命令创建实时 Web 服务
1. 使用以下命令创建实时 Web 服务：

   ```azurecli
   az ml service create realtime -f iris-score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   这会生成稍后可用的 Web 服务 ID。

   可将以下开关与 **az ml service create realtime** 命令结合使用：
   * -n：应用名称，必须全部小写。
   * -f：评分脚本文件名
   * --model-file：模型文件，在本例中是 pickle model.pkl 文件
   * -r：模型的类型，在本例中为 Python 模型
   * --collect-model-data true：启用数据收集

   >[!IMPORTANT]
   >服务名称（也是新的 Docker 映像名称）必须全部小写，否则可能出错。 

2. 运行该命令时，模型和评分文件会上传到在设置环境过程中创建的存储帐户。 部署过程会生成包含模型、架构和评分文件的 Docker 映像，并将其推送到 ACR 注册表：**\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**。 

   然后，会在本地将该映像提取到计算机，并启动基于该映像的 Docker 容器。 如果环境是以群集模式配置的，则 Docker 容器会部署到 ACS Kubernetes 群集。

   在部署过程中，会在本地计算机上创建 Web 服务的 HTTP REST 终结点。 几分钟后，该命令应会完成并返回成功消息，Web 服务已准备好运行！

3. 可以使用 **docker ps** 命令查看正在运行的 Docker 容器：
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-using-separate-commands"></a>使用单独的命令创建实时 Web 服务
除了上面所示的 **az ml service create realtime** 命令以外，还可以单独执行这些步骤。 先注册模型，再生成清单，再生成 Docker 映像，最后创建 Web 服务。 这种循序渐进的方法能够更灵活地控制每个步骤。 此外，可以重复使用前一步骤生成的实体，仅当需要时才重新生成实体。

1. 通过提供 pickle 文件名注册模型。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   此命令生成模型 ID。

2. 创建清单

   若要创建清单，请使用以下命令并提供前一步骤生成的模型 ID 输出：

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris-score.py -r python -i <model ID> -s service_schema.json
   ```
   此命令生成清单 ID。

3. 创建 Docker 映像

   若要创建 Docker 映像，请使用以下命令并提供前一步骤生成的清单 ID 值输出：

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   此命令生成 Docker 映像 ID。
   
4. 创建服务

   若要创建服务，请使用所列的命令并提供前一步骤生成的映像 ID 输出：

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   此命令生成 Web 服务 ID。

现在，可以运行该 Web 服务。

## <a name="run-the-real-time-web-service"></a>运行实时 Web 服务

通过在 **irisapp** Web 服务中馈送一条 JSON 编码的记录（其中包含由四个随机数构成的数组），来测试运行该服务。

1. Web 服务的创建过程包含样本数据。 以本地模式运行时，可以调用 **az ml service show realtime** 命令。 该调用会检索可用于测试服务的示例运行命令。 此外，它还会检索可用于将服务合并到自己的自定义应用中的评分 URL：

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. 若要测试服务，请执行返回的服务运行命令。

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   输出为 **"2"**，即预测的类。 （你的结果可能不同。） 

3. 如果想要从 CLI 外部运行服务，需要获取用于身份验证的密钥：

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>查看在 Azure Blob 存储中收集的数据

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 找到自己的存储帐户。 为此，请单击“更多服务”

3. 在搜索框中键入“存储帐户”，按 **Enter**

4. 在“存储帐户”搜索页中，选择与环境匹配的“存储帐户”资源。 

   > [!TIP]
   > 若要确定要使用的存储帐户，请打开 Azure Machine Learning Workbench，选择正在处理的项目，并通过“文件”菜单打开命令行提示符。在命令行提示符下，键入 `az ml env show -v` 并检查 *storage_account* 值。 这是存储帐户的名称

5. “存储帐户”页打开后，请单击左侧列表中的“容器”项。 找到名为 **modeldata** 的容器。 
 
   如果看不到任何数据，可能需要在发出第一个 Web 服务请求之后，最长等待 10 分钟才能看到数据开始传播到存储帐户。

   数据通过以下容器路径流入 Blob：

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. 可以从 Azure Blob 使用此数据。 可以借助多种工具来使用 Microsoft 软件和开源工具，例如：

   - Azure ML Workbench：通过将 csv 文件添加为数据源，在 Azure ML Workbench 中打开 csv 文件。 
   - Excel：将日常 csv 文件作为电子表格打开。
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)：使用从 Blob 中 csv 数据提取的数据创建图表。
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started)：将 csv 数据加载到 hive 表中，并直接对 Blob 执行 SQL 查询。
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview)：使用大部分 csv 数据创建数据帧。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>后续步骤
由三个部分构成的系列教程的本第三部分已介绍如何使用 Azure 机器学习服务来执行以下操作：
> [!div class="checklist"]
> * 找到模型文件
> * 生成评分脚本和架构文件
> * 准备环境
> * 创建实时 Web 服务
> * 运行实时 Web 服务
> * 检查输出 Blob 数据 

现已成功地在各种计算环境中运行训练脚本、创建模型、序列化模型，并通过基于 Docker 的 Web 服务将模型操作化。 

接下来，可以执行高级数据准备：
> [!div class="nextstepaction"]
> [高级数据准备](tutorial-bikeshare-dataprep.md)

