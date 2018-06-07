---
title: Przekształcanie danych za pomocą działania Pig w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używasz działania Pig w fabryce danych Azure do wykonywania skryptów usługi Pig w klastrze usługi HDInsight na — żądanie/swój własny.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d65851f73ca3cc0bc85caff39b3a322900b78843
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622520"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania Pig w fabryce danych Azure
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą działania Pig w fabryce danych w wersji 2](../transform-data-using-hadoop-pig.md).


Działanie HDInsight Pig w fabryce danych [potoku](data-factory-create-pipelines.md) wykonuje zapytania Pig na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastrów HDInsight opartych na systemie Windows/Linux. W tym artykule opiera się na [działań przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) i wykonaj samouczka: [kompilacji swój pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```JSON
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
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
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```
## <a name="syntax-details"></a>Szczegóły składni
| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania |Yes |
| description |Tekst opisujący działanie służy do |Nie |
| type |HDinsightPig |Yes |
| dane wejściowe |Co najmniej jeden dane wejściowe, używane na potrzeby działania Pig |Nie |
| wyjścia |Co najmniej jeden dane wyjściowe, generowane przez działanie Pig |Yes |
| linkedServiceName |Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych |Yes |
| Skrypt |Określ wbudowanego skryptu Pig |Nie |
| Ścieżka skryptu |Umieść skrypt programu Pig w magazynie obiektów blob platformy Azure, a następnie podaj ścieżkę do pliku. Użyj właściwości 'script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz wartość dla odwołania do skryptu Pig |Nie |

## <a name="example"></a>Przykład
Zastanówmy się przykładem gier dzienniki analizy, w której chcesz zidentyfikować czasu poświęconego przez graczy granie w gry uruchomiony przez firmę.

Następujący przykładowy dziennik gier jest plików rozdzielanych przecinkami (,). Zawiera ona następujące pola — ProfileID, SessionStart, czas trwania, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Wieprzowa skryptu** przetwarzania tych danych:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Aby wykonać ten skrypt programu Pig w potoku fabryki danych, wykonaj następujące czynności:

1. Tworzenie połączonej usługi, aby zarejestrować [klaster obliczeniowy własne HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfiguruj [klastra obliczeniowego HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Umożliwia wywołanie tej połączonej usługi **HDInsightLinkedService**.
2. Utwórz [połączona usługa](data-factory-azure-blob-connector.md) Aby skonfigurować połączenie z magazynu obiektów Blob Azure, w którym znajdują się dane. Umożliwia wywołanie tej połączonej usługi **StorageLinkedService**.
3. Utwórz [zestawów danych](data-factory-create-datasets.md) wskazujący dane wejściowe i danych wyjściowych. Teraz wywołać zestawu danych wejściowych **PigSampleIn** i zestawie danych wyjściowych **PigSampleOut**.
4. Skopiuj zapytanie Pig w pliku magazynu obiektów Blob Azure, które są skonfigurowane w kroku #2. Jeśli magazynu Azure, który jest hostem danych jest inny niż ten, który znajduje się plik zapytania, Utwórz oddzielne połączoną usługą magazynu Azure. Odwoływać się do połączonej usługi w konfiguracji działania. Użyj ** scriptPath ** należy określić ścieżkę do pliku skryptu pig i **scriptLinkedService**. 
   
   > [!NOTE]
   > Można też podać przy użyciu wbudowanego skryptu Pig w definicji działania **skryptu** właściwości. Jednak nie zaleca tego podejścia jako wszystkie znaki specjalne w wymaga skryptu, aby użyć znaków ucieczki i może powodować problemy debugowania. Najlepszym rozwiązaniem jest wykonaj krok #4.
   > 
   > 
5. Tworzenie potoku z działaniem HDInsightPig. To działanie przetwarza dane wejściowe, uruchamiając skrypt programu Pig w klastrze usługi HDInsight.

    ```JSON   
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
        ]
      }
    } 
    ```
6. Wdróż potok. Zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu, aby uzyskać szczegółowe informacje. 
7. Monitorowanie potoku korzystanie z widoków monitorowania i zarządzania fabryki danych. Zobacz [monitorowanie i zarządzanie nimi potoki fabryki danych](data-factory-monitor-manage-pipelines.md) artykułu, aby uzyskać szczegółowe informacje.

## <a name="specifying-parameters-for-a-pig-script"></a>Określanie parametrów dla skryptu Pig
Rozważmy następujący przykład: gier dzienniki są codziennie pozyskanych w magazynie obiektów Blob Azure i przechowywane w folderze partycjonowanej na podstawie daty i godziny. Chcesz parametryzacja skrypt programu Pig i przekazać do lokalizacji folderu wejściowych dynamicznie w czasie wykonywania i również utworzyć różne dane wyjściowe z datę i godzinę.

Aby użyć sparametryzowany skrypt programu Pig, wykonaj następujące czynności:

* Zdefiniuj parametry w **definiuje**.

    ```JSON  
    {
        "name": "PigActivitySamplePipeline",
          "properties": {
        "activities": [
            {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                      {
                        "name": "PigSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "PigSampleOut"
                      }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      }
                },
                   "scheduler": {
                      "frequency": "Day",
                      "interval": 1
                }
              }
        ]
      }
    }
    ```  
* Skrypt programu Pig, dotyczą parametry, używając "**$parameterName**" jak pokazano w poniższym przykładzie:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Zobacz też
* [Działanie gałęzi](data-factory-hive-activity.md)
* [Działania MapReduce](data-factory-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

