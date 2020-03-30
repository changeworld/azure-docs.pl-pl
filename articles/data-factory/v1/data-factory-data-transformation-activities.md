---
title: 'Transformacja danych: przetwarzanie & przekształcanie danych '
description: Dowiedz się, jak przekształcać dane lub przetwarzać dane w usłudze Azure Data Factory przy użyciu usługi Hadoop, Machine Learning lub Usługi Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703387"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Data Factory
> [!div class="op_single_selector"]
> * [Gałęzi](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce (Mapa)](data-factory-map-reduce.md)  
> * [Połączenia strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Uczenie maszynowe](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedura składowana](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Niestandardowe usługi .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Omówienie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [działania przekształcania danych w fabryce danych](../transform-data.md).

W tym artykule opisano działania przekształcania danych w usłudze Azure Data Factory, których można użyć do przekształcania i przetwarzania nieprzetworzonych danych w prognozy i szczegółowe informacje. Działanie transformacji jest wykonywane w środowisku obliczeniowym, takim jak klaster usługi Azure HDInsight lub usługa Azure Batch. Zawiera łącza do artykułów ze szczegółowymi informacjami na temat każdego działania transformacji.

Usługa Fabryka danych obsługuje następujące działania przekształcania danych, które można dodać do [potoków](data-factory-create-pipelines.md) indywidualnie lub w łańcuchu z innym działaniem.

> [!NOTE]
> Aby uzyskać instruktaż z instrukcjami krok po kroku, zobacz [Tworzenie potoku z artykułu transformacja hive.](data-factory-build-your-first-pipeline.md)  
> 
> 

## <a name="hdinsight-hive-activity"></a>Aktywność gałęzi HDInsight
Działanie hive hdinsight w potoku fabryki danych wykonuje zapytania hive na własny lub na żądanie Windows/Linux oparte na klastrze HDInsight. Zobacz [aktywność hive](data-factory-hive-activity.md) artykuł, aby uzyskać szczegółowe informacje na temat tego działania. 

## <a name="hdinsight-pig-activity"></a>Aktywność HDInsight Pig
Działanie HIInsight Pig w potoku usługi Data Factory wykonuje zapytania Pig na własny lub na żądanie Windows/ Linux oparte na klastrze HDInsight. Zobacz [artykuł aktywność świni,](data-factory-pig-activity.md) aby uzyskać szczegółowe informacje na temat tej działalności. 

## <a name="hdinsight-mapreduce-activity"></a>Mapa HDInsightZrozuchomić aktywność
Działanie HDInsight MapReduce w potoku data factory wykonuje programy MapReduce na własną lub na żądanie klaster HDInsight oparty na systemie Windows/Linux. Zobacz [MapReduce activity](data-factory-map-reduce.md) artykuł, aby uzyskać szczegółowe informacje na temat tego działania.

## <a name="hdinsight-streaming-activity"></a>Aktywność przesyłania strumieniowego hdinsight
Aktywność przesyłania strumieniowego HDInsight w potoku usługi Data Factory wykonuje programy przesyłania strumieniowego Hadoop na własny lub na żądanie klaster HDInsight oparty na systemie Windows/Linux. Zobacz [HDInsight streaming działania,](data-factory-hadoop-streaming-activity.md) aby uzyskać szczegółowe informacje na temat tego działania.

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
Działanie programu HDInsight Spark w potoku usługi Data Factory wykonuje programy Platformy Spark we własnym klastrze HDInsight. Aby uzyskać szczegółowe informacje, zobacz [Wywoływanie programów Spark z usługi Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Działania związane z uczeniem maszynowym
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web usługi Azure Machine Learning do analizy predykcyjnej. Za pomocą [działania wykonywania wsadowego](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) w potoku usługi Azure Data Factory, można wywołać usługę sieci web uczenia maszynowego, aby prognoz na danych w partii.

Z biegiem czasu modele predykcyjne w eksperymentach oceniania uczenia maszynowego muszą zostać przeszkolone przy użyciu nowych wejściowych zestawów danych. Po zakończeniu przekwalifikowania chcesz zaktualizować usługę sieci web oceniania za pomocą ponownie przeszkolonego modelu uczenia maszynowego. Za pomocą [działania aktualizuj zasób](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) można zaktualizować usługę sieci web za pomocą nowo przeszkolonego modelu.  

Zobacz [Korzystanie z działań uczenia maszynowego,](data-factory-azure-ml-batch-execution-activity.md) aby uzyskać szczegółowe informacje na temat tych działań uczenia maszynowego. 

## <a name="stored-procedure-activity"></a>Działanie procedury składowanej
Za pomocą działania procedury przechowywanej programu SQL Server w potoku usługi Data Factory można wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure SQL Data Warehouse, BAZA DANYCH PROGRAMU SQL Server w przedsiębiorstwie lub maszyna wirtualna platformy Azure. Zobacz [artykule Działanie procedury składowanej,](data-factory-stored-proc-activity.md) aby uzyskać szczegółowe informacje.  

## <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Działanie U-SQL usługi Data Lake Analytics uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Zobacz [data analytics U-SQL activity](data-factory-usql-activity.md) article for details. 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Jeśli trzeba przekształcić dane w sposób, który nie jest obsługiwany przez fabrykę danych, można utworzyć działanie niestandardowe z własną logiką przetwarzania danych i użyć działania w potoku. Niestandardowe działanie platformy .NET można skonfigurować do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight. Zobacz Użyj artykułu [działania niestandardowe,](data-factory-use-custom-activities.md) aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowe
Utwórz usługę połączony dla środowiska obliczeniowego, a następnie użyj połączonej usługi podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowych obsługiwanych przez fabrykę danych. 

1. **Na żądanie:** W tym przypadku środowisko obliczeniowe jest w pełni zarządzane przez fabrykę danych. Jest on automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania do przetwarzania danych i usunięciem po zakończeniu zadania. Można skonfigurować i kontrolować szczegółowe ustawienia środowiska obliczeniowego na żądanie do wykonywania zadań, zarządzania klastrami i akcji boottrappingu. 
2. **Bring Your Own:** W takim przypadku można zarejestrować własne środowisko obliczeniowe (na przykład klaster HDInsight) jako połączona usługa w fabryce danych. Środowisko obliczeniowe jest zarządzane przez Ciebie i usługa Data Factory używa go do wykonywania działań. 

Zobacz artykuł [Compute Linked Services,](data-factory-compute-linked-services.md) aby dowiedzieć się więcej o usługach obliczeniowych obsługiwanych przez fabrykę danych. 

## <a name="summary"></a>Podsumowanie
Usługa Azure Data Factory obsługuje następujące działania przekształcania danych i środowiska obliczeniowe dla działań. Działania transformacji można dodać do potoków indywidualnie lub łańcuchem z innym działaniem.

| Działanie przekształcania danych | Środowisko obliczeniowe |
|:--- |:--- |
| [Gałęzi](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce (Mapa)](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Połączenia strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |Maszyna wirtualna platformy Azure |
| [Procedura składowana](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse lub SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [Dotnet](data-factory-use-custom-activities.md) |Usługa HDInsight [Hadoop] lub usługa Azure Batch |

