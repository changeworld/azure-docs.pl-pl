---
title: Przekształcanie danych przy użyciu działania programu Hive — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania programu Hive w usłudze Azure data factory w celu uruchomienia zapytania programu Hive na na — żądanie/swój własny klaster HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4b622a5925aebd140fed2ac74eaf7cc186803b90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236154"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania programu Hive w usłudze Azure Data Factory 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie technologii hive](data-factory-hive-activity.md) 
> * [Działania technologii pig](data-factory-pig-activity.md)
> * [Działania technologii MapReduce](data-factory-map-reduce.md)
> * [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działania platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą działania programu Hive w usłudze Data Factory](../transform-data-using-hadoop-hive.md).

Działanie HDInsight Hive w usłudze Data Factory [potoku](data-factory-create-pipelines.md) wykonuje zapytania programu Hive na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight z systemem Windows/Linux. W tym artykule opiera się na [działania przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonać instrukcje z samouczka: [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

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
| description |Tekst opisujący przeznaczenie działania |Nie |
| type |HDinsightHive |Yes |
| dane wejściowe |Dane wejściowe, używane przez działanie programu Hive |Nie |
| wyjścia |Dane wyjściowe generowane przez działanie programu Hive |Yes |
| linkedServiceName |Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory |Yes |
| skrypt |Określ wbudowany skrypt programu Hive |Nie |
| Ścieżka skryptu |Store skryptu programu Hive w usłudze Azure blob storage i podaj ścieżkę do pliku. Użyj właściwości 'obszarami script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz/wartość do odwoływania się do skryptu programu Hive przy użyciu "hiveconf" |Nie |

## <a name="example"></a>Przykład
Rozważmy przykład dzienników gier, analizy, w której chcesz zidentyfikować czas spędzony przez użytkowników granie w gry uruchomiony przez Twoją firmę. 

Następujący dziennik jest przykładowy dziennik gier, który jest przecinek (`,`) z wartościami rozdzielanymi i zawiera następujące pola — Identyfikator profilu, SessionStart, czas trwania, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Wykonanie skryptu technologii Hive** przetworzyć te dane:

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

Aby wykonać ten skrypt programu Hive w potoku usługi Data Factory, musisz wykonać następujące czynności

1. Tworzenie połączonej usługi, aby zarejestrować [HDInsight własny klaster obliczeniowy](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klastra obliczeniowego HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nadajmy tej połączonej usługi "HDInsightLinkedService".
2. Tworzenie [połączoną usługę](data-factory-azure-blob-connector.md) skonfigurować połączenie usługi Azure Blob storage, w którym znajdują się dane. Nadajmy tej połączonej usługi "StorageLinkedService"
3. Tworzenie [zestawów danych](data-factory-create-datasets.md) wskazującej dane wejściowe i dane wyjściowe. Nazwiemy "HiveSampleIn" zestaw wejściowy i wyjściowy zestaw danych "HiveSampleOut"
4. Kopiuj zapytanie Hive jako plik w usłudze Azure Blob Storage skonfigurowane w kroku #2. Jeśli magazyn do obsługi danych różni się od hostuje tego pliku zapytania, Utwórz oddzielne połączoną usługę Azure Storage i się do niego odwoływać w działaniu. Użyj **scriptPath** można określić ścieżkę do pliku zapytania hive i **element scriptLinkedService** do określania magazynu platformy Azure, który zawiera plik skryptu. 
   
   > [!NOTE]
   > Możesz też podać wbudowany skrypt programu Hive w definicji działania, za pomocą **skryptu** właściwości. Firma Microsoft nie zaleca się tej metody jako wszystkie znaki specjalne w skrypcie w obrębie dokumentu JSON musi być poprzedzone znakiem zmiany znaczenia i może spowodować problemy debugowania. Najlepszym rozwiązaniem jest przejdź do kroku #4.
   > 
   > 
5. Tworzenie potoku za pomocą działania HDInsightHive. Działania procesów/przekształcenia danych.

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
6. Wdróż potok. Zobacz [tworzenia potoków](data-factory-create-pipelines.md) artykuł, aby uzyskać szczegółowe informacje. 
7. Monitorowanie potoku przy użyciu widoków zarządzania i monitorowania fabryki danych. Zobacz [monitorowanie potoków usługi Data Factory oraz zarządzanie nimi](data-factory-monitor-manage-pipelines.md) artykuł, aby uzyskać szczegółowe informacje. 

## <a name="specifying-parameters-for-a-hive-script"></a>Określanie parametrów skryptu programu Hive
W tym przykładzie dzienników gier są codziennie pozyskane do usługi Azure Blob Storage i są przechowywane w folderze, na partycje za pomocą daty i godziny. Chcesz zdefiniować parametry skryptu programu Hive i dynamicznie przekazywania lokalizacji folderu danych wejściowych podczas wykonywania, a także generuje danych wyjściowych, podzielone na partycje z datą i godziną.

Aby użyć sparametryzowanych skryptu programu Hive, wykonaj następujące czynności

* Określ parametry w **definiuje**.

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
* W skrypcie programu Hive można znaleźć za pomocą parametru **${hiveconf:parameterName}**. 
  
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
* [Działania technologii pig](data-factory-pig-activity.md)
* [Działania technologii MapReduce](data-factory-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

