---
title: Przekształć dane przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do przekształcania danych lub przetwarzania danych w fabryce danych Azure przy użyciu platformy Hadoop, uczenie maszynowe lub usługi Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 4915e8aebf110b5ae799f409472b43b986a18b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształć dane w fabryce danych Azure
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Przesyłanie strumieniowe usługi Hadoop](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedura składowana](transform-data-using-stored-procedure.md)
> * [Język U-SQL usługi Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Niestandardowe .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Przegląd
W tym artykule opisano działania przekształcania danych w fabryce danych Azure, można użyć do przekształcania, przetwarzania danych pierwotnych do przewidywania i szczegółowych informacji. Wykonuje działanie transformacji w środowisku komputerowym, takich jak klaster Azure HDInsight lub partii zadań Azure. Zawiera łącza do artykułów z szczegółowe informacje o każdym działaniu transformacji.

Fabryka danych obsługuje następujące działania przekształcania danych, które mogą zostać dodane do [potoki](concepts-pipelines-activities.md) albo indywidualnie lub powiązane z innym działaniem.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [przekształcania działań w fabryce danych w wersji 1](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>Działanie HDInsight Hive
HDInsight Hive działania w potoku fabryki danych wykonuje zapytań programu Hive samodzielnie lub w klastrze systemu Windows/Linux-based HDInsight na żądanie. Zobacz [Hive działania](transform-data-using-hadoop-hive.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania. 

## <a name="hdinsight-pig-activity"></a>Działanie HDInsight Pig
HDInsight Pig działania w potoku fabryki danych wykonuje zapytania Pig samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [wieprzowa działania](transform-data-using-hadoop-pig.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania. 

## <a name="hdinsight-mapreduce-activity"></a>Działania HDInsight MapReduce
HDInsight MapReduce działania w potoku fabryki danych wykonuje programy MapReduce samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [działania MapReduce](transform-data-using-hadoop-map-reduce.md) artykułu, aby uzyskać szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-streaming-activity"></a>Działanie usługi HDInsight przesyłania strumieniowego
HDInsight przesyłania strumieniowego działania w potoku fabryki danych wykonuje przesyłania strumieniowego usługi Hadoop programy samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie. Zobacz [działaniu przesyłania strumieniowego HDInsight](transform-data-using-hadoop-streaming.md) szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-spark-activity"></a>Działanie Spark w usłudze HDInsight
HDInsight Spark działania w potoku fabryki danych wykonuje programy Spark w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz [programy Spark wywołania z fabryki danych Azure](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning działań
Fabryka danych Azure umożliwia łatwe tworzenie potoków korzystających z opublikowanych usługi sieci web Azure Machine Learning analizy predykcyjnej. Przy użyciu [działanie wykonywania wsadowego](transform-data-using-machine-learning.md) w potoku fabryki danych Azure, można wywołać usługi sieci web usługi Machine Learning w celu tworzenia prognoz danych w partii.

Wraz z upływem czasu modeli predykcyjnych w uczeniu maszynowym oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Po wykonaniu ponownego trenowania chcesz zaktualizować usługę sieci web oceniania retrained modelu uczenia maszynowego. Można użyć [działanie aktualizacji zasobu](update-machine-learning-models.md) aktualizowania usługi sieci web przy użyciu nowo trenowanego modelu.  

Zobacz [uczenia maszynowego Użyj działania](transform-data-using-machine-learning.md) szczegółowe informacje o tych działań uczenia maszynowego. 

## <a name="stored-procedure-activity"></a>Działanie procedury przechowywanej
Działania procedury składowanej SQL Server w potoku fabryki danych służy do wywołania procedury przechowywanej w jednym z następujących magazynów danych: baza danych SQL Azure, Magazyn danych SQL Azure, bazy danych serwera SQL w przedsiębiorstwie lub maszynie Wirtualnej platformy Azure. Zobacz [działania procedura składowana](transform-data-using-stored-procedure.md) artykułu, aby uzyskać szczegółowe informacje.  

## <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Data Lake Analytics U-SQL działanie uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Zobacz [działanie U-SQL analizy danych](transform-data-using-data-lake-analytics.md) artykułu, aby uzyskać szczegółowe informacje. 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Do przekształcania danych w taki sposób, który nie jest obsługiwany przez fabrykę danych należy można tworzyć niestandardowe działania na własną logikę przetwarzania danych i użyj działania w potoku. Można skonfigurować niestandardowe działania .NET przy użyciu usługi partia zadań Azure lub klaster Azure HDInsight. Zobacz [skorzystać z działań niestandardowych](transform-data-using-dotnet-custom-activity.md) artykułu, aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowe
Można utworzyć połączonej usługi dla środowiska obliczeniowego, a następnie użyć połączonej usługi, podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowe obsługiwane przez fabryki danych. 

- **Na żądanie**: W tym przypadku środowiska komputerowego pełni zarządza fabryki danych. Została ona utworzona automatycznie przez usługi fabryka danych przed zadania jest przesłane do przetwarzania danych i usuwane po zakończeniu zadania. Można skonfigurować i sterować szczegółowych ustawień środowiska obliczeń na żądanie do wykonywania zadań zarządzania klastrem i uruchamianie akcji. 
- **Bring Your Own**: W tym przypadku własne środowisko przetwarzania danych (na przykład klaster usługi HDInsight) może być rejestrowany jako połączonej usługi z fabryki danych. Środowiska komputerowego jest zarządzany przez użytkownika i używa go do wykonania działania usługi fabryka danych. 

Zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu, aby dowiedzieć się więcej o usługach obliczeniowe obsługiwane przez fabryki danych. 

## <a name="next-steps"></a>Kolejne kroki
Następujące samouczki przykład za pomocą działania przekształcenia: [samouczek: Przekształcanie danych za pomocą Spark](tutorial-transform-data-spark-powershell.md)