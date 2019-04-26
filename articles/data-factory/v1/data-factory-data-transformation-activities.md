---
title: 'Przekształcanie danych: Proces i Transformuj dane | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak przekształcić danych lub przetwarzać dane w usłudze Azure Data Factory przy użyciu usługi Hadoop, Machine Learning lub Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b1a99f2872a69e01232c69a73f36319552429ca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486604"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Przesyłanie strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedura składowana](data-factory-stored-proc-activity.md)
> * [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe platformy .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Omówienie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [działania przekształcania danych w usłudze Data Factory](../transform-data.md).

W tym artykule opisano działania dotyczące przekształcania danych w usłudze Azure Data Factory służy do przekształcania, a następnie przetwarza danych pierwotnych w prognozy i szczegółowych informacji. Wykonuje działanie przekształcania w środowisku komputerowym, takimi jak klaster usługi Azure HDInsight czy Azure Batch. Zawiera łącza do artykułów o szczegółowe informacje na temat każdego działania przekształcania.

Usługa Data Factory obsługuje następujące działania przekształcania danych, które mogą być dodawane do [potoki](data-factory-create-pipelines.md) albo indywidualnie lub łączyć je z innymi działaniami.

> [!NOTE]
> Przewodnik z instrukcjami krok po kroku, zobacz [jest tworzony potok z przekształcenie programu Hive](data-factory-build-your-first-pipeline.md) artykułu.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Działanie HDInsight Hive
Działanie HDInsight Hive w potoku usługi fabryka danych wykonuje zapytania programu Hive na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działania programu Hive](data-factory-hive-activity.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu. 

## <a name="hdinsight-pig-activity"></a>Działanie HDInsight Pig
Działanie HDInsight Pig w potoku usługi fabryka danych wykonuje zapytania Pig na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działania technologii Pig](data-factory-pig-activity.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu. 

## <a name="hdinsight-mapreduce-activity"></a>Działanie HDInsight MapReduce
Działanie HDInsight MapReduce w potoku usługi fabryka danych wykonuje programów MapReduce na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działania technologii MapReduce](data-factory-map-reduce.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu.

## <a name="hdinsight-streaming-activity"></a>Działanie HDInsight Streaming
Działania przesyłania strumieniowego HDInsight w potoku usługi fabryka danych wykonuje programy przesyłania strumieniowego usługi Hadoop na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działanie HDInsight Streaming](data-factory-hadoop-streaming-activity.md) szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
Działanie HDInsight Spark w potoku usługi fabryka danych wykonuje programów platformy Spark w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz [wywoływanie programów platformy Spark w usłudze Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Usługi Machine Learning działań
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci web Azure Machine Learning do analizy predykcyjnej. Za pomocą [działanie wykonywania wsadowego](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) w potoku usługi Azure Data Factory można wywołać usługę internetową Machine Learning w celu prognozowania na danych w usłudze batch.

Wraz z upływem czasu modele predykcyjne w usłudze Machine Learning do oceny eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Po zakończeniu ponownego trenowania, chcesz zaktualizować usługi internetowej przyznawania ocen retrained modelu uczenia maszynowego. Możesz użyć [działanie aktualizacji zasobu](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) zaktualizować usługę sieci web przy użyciu nowo trenowanego modelu.  

Zobacz [działań usługi Machine Learning użyj](data-factory-azure-ml-batch-execution-activity.md) szczegółowe informacje na temat tych działań usługi Machine Learning. 

## <a name="stored-procedure-activity"></a>Działanie procedury przechowywanej
Działanie procedury składowanej programu SQL Server w potoku usługi fabryka danych umożliwia wywoływanie procedury składowanej w jednym z następujących magazynów danych: Azure SQL Database, Azure SQL Data Warehouse, bazy danych SQL Server w przedsiębiorstwie lub Maszynie wirtualnej platformy Azure. Zobacz [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) artykuł, aby uzyskać szczegółowe informacje.  

## <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Działania języka U-SQL usługi Data Lake Analytics uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Zobacz [działania języka U-SQL usługi Data Analytics](data-factory-usql-activity.md) artykuł, aby uzyskać szczegółowe informacje. 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Jeśli zachodzi potrzeba Przekształcanie danych w taki sposób, że nie jest obsługiwana przez usługę Data Factory, możesz utworzyć niestandardowe działanie za pomocą własnej logiki przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klaster usługi HDInsight na platformie Azure. Zobacz [korzystanie z działań niestandardowych](data-factory-use-custom-activities.md) artykuł, aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowego
Tworzenie połączonej usługi dla środowiska obliczeniowego i następnie użyć połączonej usługi, podczas definiowania działanie przekształcania. Istnieją dwa rodzaje środowisk obliczeniowych obsługiwanych przez usługę Data Factory. 

1. **Na żądanie**:  W takim środowisku komputerowym jest w pełni zarządzana przez usługę Data Factory. Zostanie automatycznie utworzony przez usługę Data Factory przed zadanie jest przesyłane do przetwarzania danych i usuwane, gdy zadanie jest ukończone. Można skonfigurować i kontrolować ustawienia szczegółowe środowisko obliczeniowe na żądanie na potrzeby wykonywania zadań zarządzania klastrem i uruchamianie akcji. 
2. **Skorzystaj z własnych**: W takim przypadku można zarejestrować własne środowisko przetwarzania danych (na przykład klaster HDInsight) jako połączonej usługi w usłudze Data Factory. Środowiskiem obliczeniowym jest zarządzany przez użytkownika, a usługa Data Factory używa go do wykonywania działań. 

Zobacz [usługi połączone usługi Compute](data-factory-compute-linked-services.md) artykuł, aby dowiedzieć się więcej na temat usług obliczeniowych obsługiwanych przez usługę Data Factory. 

## <a name="summary"></a>Podsumowanie
Usługa Azure Data Factory obsługuje następujące działania przekształcania danych i środowisk obliczeniowych dla działań. Działania przekształcania, można dodawać do potoków pojedynczo lub powiązane z innymi działaniami.

| Działanie przekształcania danych | Środowisko obliczeniowe |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Przesyłanie strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning działania: Wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |Maszyna wirtualna platformy Azure |
| [Procedura składowana](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse lub SQL Server |
| [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |Usługa HDInsight [Hadoop] lub usługa Azure Batch |

