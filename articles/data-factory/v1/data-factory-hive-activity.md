---
title: Przekształcanie danych przy użyciu aktywności hive — Azure
description: Dowiedz się, jak za pomocą działania gałęzi w fabryce danych platformy Azure do uruchamiania zapytań hive w klastrze HDInsight na żądanie/własne.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703353"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności gałęzi w fabryce danych platformy Azure 
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu aktywności gałęzi w fabryce danych](../transform-data-using-hadoop-hive.md).

Działanie hive hdinsight w [potoku](data-factory-create-pipelines.md) fabryki danych wykonuje zapytania hive na [własny](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux oparte na klastrze HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](data-factory-data-transformation-activities.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Skompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

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
| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa działania |Tak |
| description |Tekst opisujący, do czego jest używane działanie |Nie |
| type |HDinsightHive (HDinsight) |Tak |
| Wejścia |Dane wejściowe używane przez działanie Gałąź |Nie |
| Wyjść |Produkty wytwarzane przez działanie Hive |Tak |
| linkedServiceName |Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w usłudze Data Factory |Tak |
| skrypt |Określanie skryptu gałęzi w linii przewodowej |Nie |
| scriptPath |Magazyn gałęzi w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości 'script' lub 'scriptPath'. Oba nie mogą być używane razem. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary kluczy i wartości do odwoływania się w skrypcie gałęzi za pomocą 'hiveconf' |Nie |

## <a name="example"></a>Przykład
Rozważmy przykład analizy dzienników gier, w której chcesz zidentyfikować czas spędzony przez użytkowników grających w gry uruchomione przez Twoją firmę. 

Poniższy dziennik jest przykładowym dziennikiem gry, który jest oddzielony przecinkiem (`,`) i zawiera następujące pola — ProfileID, SessionStart, Duration, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Skrypt **gałęzi** do przetwarzania tych danych:

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

Aby wykonać ten skrypt gałęzi w potoku usługi Data Factory, należy wykonać następujące czynności:

1. Utwórz usługę połączony, aby zarejestrować [własny klaster obliczeniowy HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klaster obliczeniowy HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nazwijmy tę połączone usługi "HDInsightLinkedService".
2. Utwórz [połączony serwis,](data-factory-azure-blob-connector.md) aby skonfigurować połączenie z magazynem obiektów Blob platformy Azure obsługującym dane. Nazwijmy tę połączone usługi "StorageLinkedService"
3. Utwórz [zestawy danych wskazujące](data-factory-create-datasets.md) dane wejściowe i wyjściowe. Nazwijmy wejściowy zestaw danych "HiveSampleIn" i wyjściowy zestaw danych "HiveSampleOut"
4. Skopiuj kwerendę hive jako plik do usługi Azure Blob Storage skonfigurowaną w #2 kroku. jeśli magazyn do hostowania danych różni się od tego, który hostuje ten plik zapytania, utwórz oddzielną usługę linked Azure Storage i odwołaj się do niej w działaniu. Użyj **scriptPath,** aby określić ścieżkę do pliku zapytania gałęzi i **scriptLinkedService,** aby określić magazyn platformy Azure, który zawiera plik skryptu. 
   
   > [!NOTE]
   > Można również podać wbudowany skrypt hive w definicji działania przy użyciu właściwości **skryptu.** Nie zaleca się tego podejścia, ponieważ wszystkie znaki specjalne w skrypcie w dokumencie JSON musi zostać zmienione i może spowodować problemy debugowania. Najlepszym rozwiązaniem jest, aby wykonać krok #4.
   > 
   > 
5. Utwórz potok z hdinsightHive działania. Działanie przetwarza/przekształca dane.

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
6. Wdrażanie potoku. Zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykuł, aby uzyskać szczegółowe informacje. 
7. Monitoruj potok przy użyciu widoków monitorowania i zarządzania fabryki danych. Zobacz [monitorowanie i zarządzanie potokami fabryki danych, aby](data-factory-monitor-manage-pipelines.md) uzyskać szczegółowe informacje. 

## <a name="specifying-parameters-for-a-hive-script"></a>Określanie parametrów skryptu gałęzi
W tym przykładzie dzienniki gier są codziennie pozyskiwania do usługi Azure Blob Storage i są przechowywane w folderze podzielonym na partycje z datą i godziną. Chcesz sparametryzować skrypt hive i dynamicznie przekazywać lokalizację folderu wejściowego w czasie wykonywania, a także tworzyć dane wyjściowe na partycje z datą i godziną.

Aby użyć sparametryzowanego skryptu Hive, wykonaj następujące czynności:

* Zdefiniuj parametry w **zdefiniowaniu**.

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
* W skrypcie gałęzi należy odwołać się do parametru przy użyciu **funkcji ${hiveconf:parameterName}**. 
  
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
* [Aktywność świń](data-factory-pig-activity.md)
* [Działanie mapreduce](data-factory-map-reduce.md)
* [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

