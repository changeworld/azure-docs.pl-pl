---
title: Przekształcanie danych za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcić danych lub przetwarzać dane w usłudze Azure Data Factory przy użyciu usługi Hadoop, Machine Learning lub Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: afd1944006a08811075e8af8b1a641d00ee3c352
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112818"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Przesyłanie strumieniowe usługi Hadoop](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedura składowana](transform-data-using-stored-procedure.md)
> * [Język U-SQL usługi Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Niestandardowe platformy .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Przegląd
W tym artykule opisano działania dotyczące przekształcania danych w usłudze Azure Data Factory służy do przekształcania, a następnie przetwarza danych pierwotnych w prognozy i szczegółowych informacji. Wykonuje działanie przekształcania w środowisku komputerowym, takimi jak klaster usługi Azure HDInsight czy Azure Batch. Zawiera łącza do artykułów o szczegółowe informacje na temat każdego działania przekształcania.

Usługa Data Factory obsługuje następujące działania przekształcania danych, które mogą być dodawane do [potoki](concepts-pipelines-activities.md) albo indywidualnie lub łączyć je z innymi działaniami.

## <a name="hdinsight-hive-activity"></a>Działanie HDInsight Hive
Działanie HDInsight Hive w potoku usługi fabryka danych wykonuje zapytania programu Hive na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działania programu Hive](transform-data-using-hadoop-hive.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu. 

## <a name="hdinsight-pig-activity"></a>Działanie HDInsight Pig
Działanie HDInsight Pig w potoku usługi fabryka danych wykonuje zapytania Pig na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [Pig działania](transform-data-using-hadoop-pig.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu. 

## <a name="hdinsight-mapreduce-activity"></a>Działanie HDInsight MapReduce
Działanie HDInsight MapReduce w potoku usługi fabryka danych wykonuje programów MapReduce na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md) artykuł, aby uzyskać szczegółowe informacje o tym działaniu.

## <a name="hdinsight-streaming-activity"></a>Działanie HDInsight Streaming
Działanie HDInsight Streaming w potoku usługi fabryka danych wykonuje programy przesyłania strumieniowego usługi Hadoop na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie. Zobacz [działanie HDInsight Streaming](transform-data-using-hadoop-streaming.md) szczegółowe informacje dotyczące tego działania.

## <a name="hdinsight-spark-activity"></a>Działanie HDInsight Spark
Działanie HDInsight Spark w potoku usługi fabryka danych wykonuje programów platformy Spark w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz [wywoływanie programów platformy Spark w usłudze Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Usługi Machine Learning działań
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci web Azure Machine Learning do analizy predykcyjnej. Za pomocą [Batch Execution, działanie](transform-data-using-machine-learning.md) w potoku usługi Azure Data Factory można wywołać usługę internetową Machine Learning w celu prognozowania na danych w usłudze batch.

Wraz z upływem czasu modele predykcyjne w usłudze Machine Learning do oceny eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Po zakończeniu ponownego trenowania, chcesz zaktualizować usługi internetowej przyznawania ocen retrained modelu uczenia maszynowego. Możesz użyć [działanie aktualizacji zasobu](update-machine-learning-models.md) zaktualizować usługę sieci web przy użyciu nowo trenowanego modelu.  

Zobacz [działań usługi Machine Learning użyj](transform-data-using-machine-learning.md) szczegółowe informacje na temat tych działań usługi Machine Learning. 

## <a name="stored-procedure-activity"></a>Działanie procedury przechowywanej
Aby wywołać procedurę składowaną w jednym z następujących magazynów danych służy działanie procedury składowanej programu SQL Server w potoku usługi Data Factory: Azure SQL Database, Azure SQL Data Warehouse, bazy danych SQL Server w przedsiębiorstwie lub Maszynie wirtualnej platformy Azure. Zobacz [działania procedura składowana](transform-data-using-stored-procedure.md) artykuł, aby uzyskać szczegółowe informacje.  

## <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Działania usługi Data Lake Analytics U-SQL uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Zobacz [działanie analiz danych U-SQL](transform-data-using-data-lake-analytics.md) artykuł, aby uzyskać szczegółowe informacje. 

## <a name="custom-activity"></a>Działanie niestandardowe
Jeśli zachodzi potrzeba Przekształcanie danych w taki sposób, że nie jest obsługiwana przez usługę Data Factory, możesz utworzyć niestandardowe działanie za pomocą własnej logiki przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klaster usługi HDInsight na platformie Azure. Zobacz [korzystanie z działań niestandardowych](transform-data-using-dotnet-custom-activity.md) artykuł, aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowego
Tworzenie połączonej usługi dla środowiska obliczeniowego i następnie użyć połączonej usługi, podczas definiowania działanie przekształcania. Istnieją dwa rodzaje środowisk obliczeniowych obsługiwanych przez usługę Data Factory. 

- **Na żądanie**: W tym przypadku środowiskiem obliczeniowym jest w pełni zarządzana przez usługę Data Factory. Zostanie automatycznie utworzony przez usługę Data Factory przed zadanie jest przesyłane do przetwarzania danych i usuwane, gdy zadanie jest ukończone. Można skonfigurować i kontrolować ustawienia szczegółowe środowisko obliczeniowe na żądanie na potrzeby wykonywania zadań zarządzania klastrem i uruchamianie akcji. 
- **Bring Your Own**: W tym przypadku należy zarejestrować własne środowisko przetwarzania danych (na przykład klaster HDInsight) jako połączonej usługi w usłudze Data Factory. Środowiskiem obliczeniowym jest zarządzany przez użytkownika, a usługa Data Factory używa go do wykonywania działań. 

Zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykuł, aby dowiedzieć się więcej na temat usług obliczeniowych obsługiwanych przez usługę Data Factory. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące samouczki, na przykład za pomocą działania przekształcenia: [samouczek: Przekształcanie danych przy użyciu platformy Spark](tutorial-transform-data-spark-powershell.md)
