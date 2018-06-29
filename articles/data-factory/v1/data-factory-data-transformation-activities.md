---
title: 'Przekształcenie danych: Proces & przekształcenia danych | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak do przekształcania danych lub przetwarzania danych w fabryce danych Azure przy użyciu platformy Hadoop, uczenie maszynowe lub usługi Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: afa00a81738563ec3c5b4b168f533b732c263825
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045623"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształć dane w fabryce danych Azure
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Przesyłanie strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedura składowana](data-factory-stored-proc-activity.md)
> * [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Przegląd
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [działań przekształcania danych w fabryce danych](../transform-data.md).

W tym artykule opisano działania przekształcania danych w fabryce danych Azure, można użyć do przekształcania, przetwarzania danych pierwotnych do przewidywania i szczegółowych informacji. Wykonuje działanie transformacji w środowisku komputerowym, takich jak klaster Azure HDInsight lub partii zadań Azure. Zawiera łącza do artykułów z szczegółowe informacje o każdym działaniu transformacji.

Fabryka danych obsługuje następujące działania przekształcania danych, które mogą zostać dodane do [potoki](data-factory-create-pipelines.md) albo indywidualnie lub powiązane z innym działaniem.

> [!NOTE]
> Aby uzyskać wskazówki krok po kroku instrukcje, zobacz [utworzyć potok transformację Hive](data-factory-build-your-first-pipeline.md) artykułu.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Działanie HDInsight Hive
HDInsight Hive działania w potoku fabryki danych wykonuje zapytań programu Hive samodzielnie lub w klastrze systemu Windows/Linux-based HDInsight na żądanie. Zobacz [Hive działania](data-factory-hive-activity.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania. 

## <a name="hdinsight-pig-activity"></a>Działanie HDInsight Pig
HDInsight Pig działania w potoku fabryki danych wykonuje zapytania Pig samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [działania Pig](data-factory-pig-activity.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania. 

## <a name="hdinsight-mapreduce-activity"></a>Działania HDInsight MapReduce
HDInsight MapReduce działania w potoku fabryki danych wykonuje programy MapReduce samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [działania MapReduce](data-factory-map-reduce.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-streaming-activity"></a>Działanie usługi HDInsight przesyłania strumieniowego
HDInsight działaniu przesyłania strumieniowego w potoku fabryki danych wykonuje przesyłania strumieniowego usługi Hadoop programy samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [działaniu przesyłania strumieniowego HDInsight](data-factory-hadoop-streaming-activity.md) szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
HDInsight Spark działania w potoku fabryki danych wykonuje programy Spark w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz [programy Spark wywołania z fabryki danych Azure](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning działań
Fabryka danych Azure umożliwia łatwe tworzenie potoków korzystających z opublikowanych usługi sieci web Azure Machine Learning analizy predykcyjnej. Przy użyciu [działanie wykonywania wsadowego](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) w potoku fabryki danych Azure, można wywołać usługi sieci web usługi Machine Learning w celu tworzenia prognoz danych w partii.

Wraz z upływem czasu modeli predykcyjnych w uczeniu maszynowym oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Po wykonaniu ponownego trenowania chcesz zaktualizować usługę sieci web oceniania retrained modelu uczenia maszynowego. Można użyć [działanie aktualizacji zasobu](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) aktualizowania usługi sieci web przy użyciu nowo trenowanego modelu.  

Zobacz [uczenia maszynowego Użyj działania](data-factory-azure-ml-batch-execution-activity.md) szczegółowe informacje o tych działań uczenia maszynowego. 

## <a name="stored-procedure-activity"></a>Działanie procedury przechowywanej
Działania procedury składowanej SQL Server w potoku fabryki danych służy do wywołania procedury przechowywanej w jednym z następujących magazynów danych: baza danych SQL Azure, Magazyn danych SQL Azure, bazy danych serwera SQL w przedsiębiorstwie lub maszynie Wirtualnej platformy Azure. Zobacz [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) artykułu, aby uzyskać szczegółowe informacje.  

## <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Data Lake Analytics U-SQL działanie uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Zobacz [działanie U-SQL analizy danych](data-factory-usql-activity.md) artykułu, aby uzyskać szczegółowe informacje. 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Do przekształcania danych w taki sposób, który nie jest obsługiwany przez fabrykę danych należy można tworzyć niestandardowe działania na własną logikę przetwarzania danych i użyj działania w potoku. Można skonfigurować niestandardowe działania .NET przy użyciu usługi partia zadań Azure lub klaster Azure HDInsight. Zobacz [skorzystać z działań niestandardowych](data-factory-use-custom-activities.md) artykułu, aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowe
Można utworzyć połączonej usługi dla środowiska obliczeniowego, a następnie użyć połączonej usługi, podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowe obsługiwane przez fabryki danych. 

1. **Na żądanie**: W tym przypadku środowiska komputerowego pełni zarządza fabryki danych. Została ona utworzona automatycznie przez usługi fabryka danych przed zadania jest przesłane do przetwarzania danych i usuwane po zakończeniu zadania. Można skonfigurować i sterować szczegółowych ustawień środowiska obliczeń na żądanie do wykonywania zadań zarządzania klastrem i uruchamianie akcji. 
2. **Bring Your Own**: W tym przypadku własne środowisko przetwarzania danych (na przykład klaster usługi HDInsight) może być rejestrowany jako połączonej usługi z fabryki danych. Środowiska komputerowego jest zarządzany przez użytkownika i używa go do wykonania działania usługi fabryka danych. 

Zobacz [obliczeniowe połączonych usług](data-factory-compute-linked-services.md) artykułu, aby dowiedzieć się więcej o usługach obliczeniowe obsługiwane przez fabryki danych. 

## <a name="summary"></a>Podsumowanie
Fabryka danych Azure obsługuje następujące działania przekształcania danych i środowisk obliczeniowych dla działań. Działania transformacji można dodawać do potoki pojedynczo lub powiązane z innym działaniem.

| Działanie przekształcania danych | Środowisko obliczeniowe |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Przesyłanie strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |Maszyna wirtualna platformy Azure |
| [Procedura składowana](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse lub SQL Server |
| [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |Usługa HDInsight [Hadoop] lub usługa Azure Batch |

