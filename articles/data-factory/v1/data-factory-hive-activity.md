---
title: Przekształcanie danych za pomocą działania Hive - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używasz działania Hive w fabryce danych Azure do uruchamiania zapytań Hive w klastrze usługi HDInsight na — żądanie/swój własny.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6826d87e5613ce4892e9fd839c66ec26282ad188
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622129"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania Hive w fabryce danych Azure 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie gałęzi](data-factory-hive-activity.md) 
> * [Działanie pig](data-factory-pig-activity.md)
> * [Działania MapReduce](data-factory-map-reduce.md)
> * [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działania niestandardowe .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą działania gałęzi w fabryce danych w wersji 2](../transform-data-using-hadoop-hive.md).

Działanie HDInsight Hive w fabryce danych [potoku](data-factory-create-pipelines.md) wykonuje zapytania Hive na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastrów HDInsight opartych na systemie Windows/Linux. W tym artykule opiera się na [działań przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) i wykonaj samouczka: [kompilacji swój pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Szczegóły składni
| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania |Yes |
| description |Tekst opisujący działanie służy do |Nie |
| type |HDinsightHive |Yes |
| dane wejściowe |Dane wejściowe używane na potrzeby działania gałęzi |Nie |
| wyjścia |Dane wyjściowe, generowane przez działanie gałęzi |Yes |
| linkedServiceName |Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych |Yes |
| Skrypt |Określ wbudowanego skryptu Hive |Nie |
| Ścieżka skryptu |Przechowywanie skryptu Hive w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości 'script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz wartość dla odwołania do skryptu Hive za pomocą "hiveconf" |Nie |

## <a name="example"></a>Przykład
Zastanówmy się przykładem gier dzienniki analizy, w której chcesz zidentyfikować czasu poświęconego przez użytkowników granie w gry uruchomiony przez firmę. 

Następujące dziennik jest przykładowy dziennik gier, czyli przecinkami (`,`) oddzielone i zawiera następujące pola — ProfileID, SessionStart, czas trwania, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Wykonanie skryptu technologii Hive** przetwarzania tych danych:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Do wykonania tego skryptu Hive w potoku fabryki danych, należy wykonać następujące czynności

1. Tworzenie połączonej usługi, aby zarejestrować [klaster obliczeniowy własne HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfiguruj [klastra obliczeniowego HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Umożliwia wywołanie tej połączonej usługi "HDInsightLinkedService".
2. Utwórz [połączona usługa](data-factory-azure-blob-connector.md) Aby skonfigurować połączenie z magazynu obiektów Blob Azure, w którym znajdują się dane. Umożliwia wywołanie tej połączonej usługi "StorageLinkedService"
3. Utwórz [zestawów danych](data-factory-create-datasets.md) wskazujący dane wejściowe i danych wyjściowych. Umożliwia wywołanie "HiveSampleIn" w zestawie danych wejściowych i wyjściowy zestaw danych "HiveSampleOut"
4. Kopiuj zapytanie Hive jako plik do magazynu obiektów Blob Azure skonfigurowane w kroku #2. Jeśli magazynu do obsługi danych jest inna niż wersja tego pliku zapytania hosting, Utwórz oddzielne połączoną usługą magazynu Azure i odwołuje się do niego w działaniu. Użyj **scriptPath** do określenia ścieżki do pliku zapytania hive i **scriptLinkedService** do określenia magazynu Azure, który zawiera plik skryptu. 
   
   > [!NOTE]
   > Można też podać przy użyciu wbudowanego skryptu Hive w definicji działania **skryptu** właściwości. Nie zaleca tego podejścia jako wszystkie znaki specjalne w skrypcie w potrzeb dokumentu JSON można użyć znaków ucieczki i może powodować problemy debugowania. Najlepszym rozwiązaniem jest wykonaj krok #4.
   > 
   > 
5. Utworzyć potok z działaniem HDInsightHive. Działanie procesów/przekształcenia danych.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Wdróż potok. Zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu, aby uzyskać szczegółowe informacje. 
7. Monitorowanie potoku korzystanie z widoków monitorowania i zarządzania fabryki danych. Zobacz [monitorowanie i zarządzanie nimi potoki fabryki danych](data-factory-monitor-manage-pipelines.md) artykułu, aby uzyskać szczegółowe informacje. 

## <a name="specifying-parameters-for-a-hive-script"></a>Określanie parametrów dla skryptu Hive
W tym przykładzie gier dzienniki są codziennie pozyskanych w magazynie obiektów Blob Azure i są przechowywane w folderze na partycje za pomocą daty i godziny. Chcesz parametryzacja skryptu Hive i przekazać do lokalizacji folderu wejściowych dynamicznie w czasie wykonywania i również utworzyć różne dane wyjściowe z datę i godzinę.

Aby użyć skryptu Hive sparametryzowane, wykonaj następujące czynności

* Zdefiniuj parametry w **definiuje**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Skryptu Hive odwoływać się do parametru przy użyciu **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Zobacz też
* [Działanie pig](data-factory-pig-activity.md)
* [Działania MapReduce](data-factory-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

