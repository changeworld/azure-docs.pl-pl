---
title: Narzędzia dołączone do DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Lista narzędzi dostępnych w obrazach systemu Windows i Ubuntu DSVM
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803943"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Jakie narzędzia są dostępne na platformie Azure Data Science Virtual Machine?

Poniżej zamieszczono aktualną listę narzędzi uwzględnionych w Data Science Virtual Machine oraz linki do zrozumienia sposobu konfiguracji poszczególnych narzędzi.


| **Narzędzie**                                                           | **Maszyna DSVM z systemem Windows** | **DSVM systemu Linux** | **Uwagi dotyczące użycia** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) z preinstalowanymi popularnymi pakietami   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [Używanie języka R w DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Wersja Developer zawiera: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [kolekcję funkcji revoscaler/biblioteki revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel i Distributed High-Performance Platform (R i Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nowe, najnowocześniejsze algorytmy uczenia maszynowego firmy Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R i Python operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus z aktywację udostępnioną: Excel, Word i PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 i 3,5 z preinstalowanymi popularnymi pakietami    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) z preinstalowanymi popularnymi pakietami języka Julia                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [Korzystanie z Julia na DSVM](./dsvm-languages.md#julia) |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-Data-platforms # SQL-Server-2016-Developer-edition.md) |
| Narzędzia bazy danych                                                       |  SQL Server Management Studio <br/> Usługi integracji programu SQL Server<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Sterowniki ODBC/JDBC|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (narzędzie do wykonywania zapytań), <br />  BCP, SQLCMD <br />  Sterowniki ODBC/JDBC| |
| Skalowalna analiza w bazie danych za pomocą usługi SQL Server Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Serwer Jupyter Notebook](https://jupyter.org/) z następującymi jądrami:                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Przykłady Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przykłady języka R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przykłady w języku Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przykłady Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przykłady pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Tylko Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (serwer notesu wieloużytkownikom)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (serwer notesu wieloużytkownikom) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Tylko Ubuntu) | |
| Narzędzia programistyczne, środowisk IDE i edytory kodu:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (wersja Community)](https://www.visualstudio.com/community/) z dodatkiem git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)i [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Program Visual Studio 2019 na DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Visual Studio Code w DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio pulpit na DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Serwer RStudio w DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Platformy PyCharm itd na DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno na DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim i Emacs: |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 git i git bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| Zestawy SDK umożliwiające dostęp do platformy Azure i pakietu usług Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI pulpicie DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Narzędzia do przenoszenia i zarządzania danymi: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Eksplorator usługi Azure Storage |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [AzCopy na DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse na DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Adlcopy na DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB narzędzia do migracji danych](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB w DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft zarządzanie danymi Gateway](https://msdn.microsoft.com/library/dn879362.aspx): przenoszenie danych między środowiskiem lokalnym a chmurą |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM użycie](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 narzędzia wiersza polecenia systemu UNIX/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Przechodzenie do szczegółów platformy Apache](https://drill.apache.org) na potrzeby eksploracji danych |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Przechodzenie do szczegółów na platformie Apache DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Narzędzia uczenia maszynowego: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integrację z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Zestaw SDK usługi Azure ML](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost na DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit na DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Weka na DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Tylko Ubuntu) | [LightGBM na DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Tylko Ubuntu) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [musujące wody](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Tylko Ubuntu) | [H20 na DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Narzędzia uczenia głębokiego, które działają na procesorze GPU lub procesorze CPU: |  |  | Kliknij nazwę poniższego narzędzia, aby uzyskać więcej informacji na temat użycia |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span>(Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>Ubuntu | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe i Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t [-3](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow obsługujący](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [cuda, cuDNN, NVIDIA Driver](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
