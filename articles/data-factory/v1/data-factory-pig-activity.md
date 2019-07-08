---
title: Przekształcanie danych przy użyciu działania technologii Pig w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania technologii Pig w usłudze Azure data factory w celu uruchomienia skryptów usługi Pig na na — żądanie/swój własny klaster HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 78ee2c1ce402a29f1a9dfdd29f31daef09134eba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611334"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania technologii Pig w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Działania przekształcania"]
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [Przekształcanie danych przy użyciu działania technologii Pig w usłudze Data Factory](../transform-data-using-hadoop-pig.md).


Działanie HDInsight Pig w usłudze Data Factory [potoku](data-factory-create-pipelines.md) wykonuje zapytania Pig na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight z systemem Windows/Linux. W tym artykule opiera się na [działania przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonać instrukcje z samouczka: [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

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
| description |Tekst opisujący przeznaczenie działania |Nie |
| type |HDinsightPig |Yes |
| inputs |Co najmniej jeden dane wejściowe, używane przez działania technologii Pig |Nie |
| outputs |Co najmniej jeden dane wyjściowe, generowane przez działanie technologii Pig |Yes |
| linkedServiceName |Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory |Yes |
| script |Określ wbudowany skrypt Pig |Nie |
| scriptPath |Store skrypt programu Pig w usłudze Azure blob storage i podaj ścieżkę do pliku. Użyj właściwości 'obszarami script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| defines |Określ parametry jako pary klucz/wartość do odwoływania się do skryptu języka Pig |Nie |

## <a name="example"></a>Przykład
Rozważmy przykład dzienników gier, analizy, w której chcesz zidentyfikować czas spędzony przez graczy granie w gry uruchomiony przez Twoją firmę.

Poniższy przykładowy dziennik gier jest plikiem rozdzielonych przecinkami (,). Zawiera ona następujące pola — Identyfikator profilu, SessionStart, czas trwania, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Pig skryptu** przetworzyć te dane:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Aby wykonać ten skrypt programu Pig w potoku usługi fabryka danych, wykonaj następujące czynności:

1. Tworzenie połączonej usługi, aby zarejestrować [HDInsight własny klaster obliczeniowy](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klastra obliczeniowego HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nadajmy tej połączonej usługi **HDInsightLinkedService**.
2. Tworzenie [połączoną usługę](data-factory-azure-blob-connector.md) skonfigurować połączenie usługi Azure Blob storage, w którym znajdują się dane. Nadajmy tej połączonej usługi **StorageLinkedService**.
3. Tworzenie [zestawów danych](data-factory-create-datasets.md) wskazującej dane wejściowe i dane wyjściowe. Nadajmy wejściowego zestawu danych **PigSampleIn** i wyjściowy zestaw danych **PigSampleOut**.
4. Kopiuj zapytanie Pig, w pliku magazynu obiektów Blob platformy Azure, które są skonfigurowane w kroku #2. Jeśli usługa Azure storage, które hostuje dane jest inny niż ten, który znajduje się plik zapytania, należy utworzyć oddzielne połączoną usługę Azure Storage. Odnoszą się do połączonej usługi w konfiguracji działania. Użyj **scriptPath** do określenia ścieżki do pliku skryptu języka pig i **element scriptLinkedService**. 
   
   > [!NOTE]
   > Możesz też podać wbudowany skrypt Pig w definicji działania, za pomocą **skryptu** właściwości. Jednak firma Microsoft nie zaleca się tej metody jako wszystkie znaki specjalne w skryptu musi być poprzedzone znakiem zmiany znaczenia i może spowodować problemy debugowania. Najlepszym rozwiązaniem jest przejdź do kroku #4.
   >
   >
5. Tworzenie potoku z działaniem HDInsightPig. To działanie przetwarza dane wejściowe, uruchamiając skrypt programu Pig w klastrze HDInsight.

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
6. Wdróż potok. Zobacz [tworzenia potoków](data-factory-create-pipelines.md) artykuł, aby uzyskać szczegółowe informacje. 
7. Monitorowanie potoku przy użyciu widoków zarządzania i monitorowania fabryki danych. Zobacz [monitorowanie potoków usługi Data Factory oraz zarządzanie nimi](data-factory-monitor-manage-pipelines.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="specifying-parameters-for-a-pig-script"></a>Określanie parametrów skryptu Pig
Rozważmy następujący przykład: dzienników gier są przetwarzanych dziennie do usługi Azure Blob Storage i przechowywane w folderze partycjonowane na podstawie daty i godziny. Chcesz zdefiniować parametry skrypt programu Pig i dynamicznie przekazywania lokalizacji folderu danych wejściowych podczas wykonywania, a także generuje danych wyjściowych, podzielone na partycje z datą i godziną.

Aby użyć sparametryzowany skrypt programu Pig, wykonaj następujące czynności:

* Określ parametry w **definiuje**.

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
* Skrypt programu Pig, dotyczą parametrów przy użyciu " **$parameterName**" jak pokazano w poniższym przykładzie:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Zobacz też
* [Działanie technologii hive](data-factory-hive-activity.md)
* [Działania technologii MapReduce](data-factory-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
