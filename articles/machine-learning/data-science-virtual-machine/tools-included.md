---
title: Narzędzia dostępne w środowisku DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Lista narzędzi dostępnych w obrazach systemu Windows i Ubuntu DSVM
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 70fa1bd2f33d3a6e58b12b9d2da617e8129c8da9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299724"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Jakie narzędzia są dostępne na platformie Azure Data Science Virtual Machine?

Poniżej zamieszczono aktualną listę narzędzi uwzględnionych w Data Science Virtual Machine oraz linki do zrozumienia sposobu konfiguracji poszczególnych narzędzi.


| **Narzędzie**                                                           | **Maszyna DSVM z systemem Windows** | **DSVM systemu Linux** | **Uwagi dotyczące użycia** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) z preinstalowanymi popularnymi pakietami   |<span class='green-check'>&#9989;</span></br> 3\.4.3                    |<span class='green-check'>&#9989;</span></br> 3\.4.3  | [R na DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Wersja Developer zawiera: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [kolekcję funkcji revoscaler/biblioteki revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel i Distributed High-Performance Platform (R i Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nowe, najnowocześniejsze algorytmy uczenia maszynowego firmy Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R i Python operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus z aktywację udostępnioną: Excel, Word i PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 i 3,5 z preinstalowanymi popularnymi pakietami    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) z preinstalowanymi popularnymi pakietami języka Julia                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia na DSVM](./dsvm-languages.md#julia) |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server w DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Narzędzia bazy danych                                                       |  SQL Server Management Studio <br/> Usługi integracji programu SQL Server<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Sterowniki ODBC/JDBC|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (narzędzie do wykonywania zapytań), <br />  BCP, SQLCMD <br />  Sterowniki ODBC/JDBC| |
| Skalowalna analiza w bazie danych za pomocą usługi SQL Server Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Serwer Jupyter Notebook](https://jupyter.org/) z następującymi jądrami:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Przykłady Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 R |<span class='green-check'>&#9989;</span></br> 3\.4.3 |<span class='green-check'>&#9989;</span></br> 3\.4.3 | [Przykłady języka R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python |<span class='green-check'>&#9989;</span></br> r.3 |<span class='green-check'>&#9989;</span> | [Przykłady w języku Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Przykłady Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przykłady pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Tylko Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (serwer notesu wieloużytkownikom)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (serwer notesu wieloużytkownikom) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Narzędzia programistyczne, środowisk IDE i edytory kodu:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2017 (wersja Community)](https://www.visualstudio.com/community/) z dodatkiem git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)i [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Program Visual Studio na DSVM](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [Visual Studio Code w DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50 XX) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio pulpit na DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Serwer RStudio w DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [Platformy PyCharm itd na DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno na DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Emacs:](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [git](https://git-scm.com/) i git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Program Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.73.55 XX) |<span class='red-x'>&#10060;</span>   |
| Zestawy SDK umożliwiające dostęp do platformy Azure i pakietu usług Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI pulpicie DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Narzędzia do przenoszenia i zarządzania danymi: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Eksplorator usługi Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.10.1) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [AzCopy na DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse na DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy na DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB narzędzia do migracji danych](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB w DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 narzędzia wiersza polecenia systemu UNIX/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Przechodzenie do szczegółów platformy Apache](https://drill.apache.org) na potrzeby eksploracji danych |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Przechodzenie do szczegółów na platformie Apache DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Narzędzia uczenia maszynowego: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integrację z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Zestaw SDK usługi Azure ML](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost na DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit na DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> ppkt | [Weka na DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 | [LightGBM na DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [musujące wody](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 na DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Narzędzia uczenia głębokiego, które działają na procesorze GPU lub procesorze CPU: |  |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe](https://github.com/BVLC/caffe) i [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) i [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) na DSVM |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t [-3](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [Łańcuch na DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [cuda, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, sterownik NVIDIA na DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod na DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Keras na DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK na DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [MXNet na DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet model Server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [Serwer modelu MXNet na DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA System Management Interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI na DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch na DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow na DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow obsługujący](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [TensorFlow na DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano na DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

