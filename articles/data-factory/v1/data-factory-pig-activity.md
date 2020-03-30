---
title: Przekształcanie danych przy użyciu aktywności świni w fabryce danych platformy Azure
description: Dowiedz się, jak za pomocą działania świni w fabryce danych platformy Azure do uruchamiania skryptów Świnia na żądanie/własnego klastra HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703206"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności świni w fabryce danych platformy Azure
> [!div class="op_single_selector" title1="Działania związane z transformacją"]
> * [Aktywność gałęzi](data-factory-hive-activity.md) 
> * [Aktywność świń](data-factory-pig-activity.md)
> * [Działanie mapreduce](data-factory-map-reduce.md)
> * [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktywność iskra](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działanie niestandardowe platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu aktywności świni w fabryce danych](../transform-data-using-hadoop-pig.md).


Działanie HIInsight Pig w [potoku](data-factory-create-pipelines.md) usługi Data Factory wykonuje zapytania Pig na [własny](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/ Linux oparte na klastrze HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](data-factory-data-transformation-activities.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Skompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

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

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa działania |Tak |
| description |Tekst opisujący, do czego jest używane działanie |Nie |
| type |HDinsightPig |Tak |
| Wejścia |Co najmniej jeden nakłady zużywane przez aktywność świni |Nie |
| Wyjść |Co najmniej jeden produkt wytwarzany w działalności trzody chlewnej |Tak |
| linkedServiceName |Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w usłudze Data Factory |Tak |
| skrypt |Określ skrypt Świnia w linii |Nie |
| scriptPath |Przechowywać skrypt Świergot w magazynie obiektów blob platformy Azure i zapewnić ścieżkę do pliku. Użyj właściwości 'script' lub 'scriptPath'. Oba nie mogą być używane razem. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| Definiuje |Określanie parametrów jako par klucza/wartości do odwoływania się w skrypcie Pig |Nie |

## <a name="example"></a>Przykład
Rozważmy przykład analizy dzienników gier, w których chcesz zidentyfikować czas spędzony przez graczy grających w gry uruchomione przez Twoją firmę.

Poniższy przykładowy dziennik gry jest plikiem oddzielonym przecinkiem (,). Zawiera następujące pola — ProfileID, SessionStart, Duration, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skrypt Świni** do przetwarzania tych danych:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Aby wykonać ten skrypt Świnka w potoku fabryki danych, wykonaj następujące kroki:

1. Utwórz usługę połączony, aby zarejestrować [własny klaster obliczeniowy HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klaster obliczeniowy HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nazwijmy tę połączone usługi **HDInsightLinkedService**.
2. Utwórz [połączony serwis,](data-factory-azure-blob-connector.md) aby skonfigurować połączenie z magazynem obiektów Blob platformy Azure obsługującym dane. Nazwijmy tę połączone usługi **StorageLinkedService**.
3. Utwórz [zestawy danych wskazujące](data-factory-create-datasets.md) dane wejściowe i wyjściowe. Nazwijmy wejściowy zestaw danych **PigSampleIn** i wyjściowy zestaw danych **PigSampleOut**.
4. Skopiuj zapytanie Pig w pliku usługi Azure Blob Storage skonfigurowanym w kroku #2. Jeśli magazyn platformy Azure, który obsługuje dane, różni się od tego, który obsługuje plik kwerendy, utwórz oddzielną usługę połączone usługi Azure Storage. Zapoznaj się z usługą połączone w konfiguracji działania. Użyj **scriptPath,** aby określić ścieżkę do pliku skryptu świni i **scriptLinkedService**. 
   
   > [!NOTE]
   > Można również podać Pig skrypt wbudowany w definicji działania przy użyciu właściwości **skryptu.** Jednak nie zaleca się tego podejścia, ponieważ wszystkie znaki specjalne w skrypcie musi być zmienione i może powodować problemy debugowania. Najlepszym rozwiązaniem jest, aby wykonać krok #4.
   >
   >
5. Utwórz potok za pomocą działania HDInsightPig. To działanie przetwarza dane wejściowe, uruchamiając skrypt Pig w klastrze HDInsight.

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
6. Wdrażanie potoku. Zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykuł, aby uzyskać szczegółowe informacje. 
7. Monitoruj potok przy użyciu widoków monitorowania i zarządzania fabryki danych. Zobacz [monitorowanie i zarządzanie potokami fabryki danych, aby](data-factory-monitor-manage-pipelines.md) uzyskać szczegółowe informacje.

## <a name="specifying-parameters-for-a-pig-script"></a>Określanie parametrów skryptu Świnia
Rozważmy następujący przykład: dzienniki gier są codziennie pozyskiwania do usługi Azure Blob Storage i przechowywane w folderze podzielonym na partycje na podstawie daty i godziny. Chcesz sparametryzować skrypt Pig i dynamicznie przekazać lokalizację folderu wejściowego w czasie wykonywania, a także utworzyć dane wyjściowe na partycje z datą i godziną.

Aby użyć sparametryzowanego skryptu Świnia, wykonaj następujące czynności:

* Zdefiniuj parametry w **zdefiniowaniu**.

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
* W skrypcie świni zapoznaj się z parametrami używającymi '**$parameterName**', jak pokazano w poniższym przykładzie:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Zobacz też
* [Aktywność gałęzi](data-factory-hive-activity.md)
* [Działanie mapreduce](data-factory-map-reduce.md)
* [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
