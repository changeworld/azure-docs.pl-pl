---
title: Wywoływanie programów MapReduce z usługi Azure Data Factory
description: Dowiedz się, jak przetwarzać dane, uruchamiając programów MapReduce w klastrze usługi HDInsight platformy Azure z usługi Azure data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 715c595f7a8757842ddf10de1c5d5c0a905e9d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824222"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Wywoływanie programów MapReduce z usługi Data Factory
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [Przekształcanie danych przy użyciu działania technologii MapReduce w usłudze Data Factory](../transform-data-using-hadoop-map-reduce.md).


Działanie HDInsight MapReduce w usłudze Data Factory [potoku](data-factory-create-pipelines.md) wykonuje programów MapReduce na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight z systemem Windows/Linux. W tym artykule opiera się na [działania przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonać instrukcje z samouczka: [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu.  

## <a name="introduction"></a>Wprowadzenie
Potok w fabryce danych Azure przetwarza danych w usługach połączonego magazynu za pomocą usług obliczeniowych połączone. Zawiera ona sekwencja działań, w którym każde działanie wykonuje operację przetwarzania specyficznego. W tym artykule opisano, przy użyciu działania technologii MapReduce w HDInsight.

Zobacz [Pig](data-factory-pig-activity.md) i [Hive](data-factory-hive-activity.md) Aby uzyskać szczegółowe informacje o uruchamianiu programów Pig/Hive skrypty na Windows/Linux-based HDInsight klastra z potoku przy użyciu działań HDInsight Pig i Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>Kod JSON działanie HDInsight MapReduce
W definicji JSON działania HDInsight: 

1. Ustaw **typu** z **działania** do **HDInsight**.
2. Określ nazwę klasy dla **className** właściwości.
3. Określ ścieżkę do pliku JAR, łącznie z nazwą pliku dla **jarFilePath** właściwości.
4. Określanie połączonej usługi, która odwołuje się do usługi Azure Blob Storage, który zawiera plik JAR dla **jarLinkedService** właściwości.   
5. Określ wszelkie argumenty dla programu MapReduce w **argumenty** sekcji. W czasie wykonywania, widzisz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z argumentami MapReduce, należy wziąć pod uwagę przy użyciu opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe, — itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Działania technologii MapReduce w HDInsight służy do uruchamiania dowolnego pliku jar MapReduce w klastrze usługi HDInsight. W poniższej definicji JSON przykładowe potoku działanie HDInsight jest skonfigurowany do uruchomienia pliku Mahout JAR.

## <a name="sample-on-github"></a>Przykład w witrynie GitHub
Możesz pobrać przykład przy użyciu działania technologii MapReduce HDInsight: [Przykłady fabryki danych w witrynie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Uruchamianie programu wyrazów
Potok w tym przykładzie uruchamia program wyrazów Map/Reduce w klastrze Azure HDInsight.   

### <a name="linked-services"></a>Połączone usługi
Najpierw utworzysz połączoną usługę służącą do połączenia magazynu platformy Azure, w którym jest używany przez klaster usługi Azure HDInsight w usłudze Azure data factory. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zastąpić **nazwa konta** i **klucz konta** przy użyciu nazwy i klucza usługi Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Usługa Azure HDInsight połączone
Następnie utworzysz połączoną usługę służącą do łączenie klastra usługi Azure HDInsight w usłudze Azure data factory. Jeśli kopiujesz/wklejasz następujący kod, Zastąp **nazwy klastra HDInsight** nazwą klastra HDInsight i zmień wartości nazwy i hasła użytkownika.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Zestawy danych
#### <a name="output-dataset"></a>Wyjściowy zestaw danych
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Wyjściowy zestaw danych należy określić dla działania MapReduce HDInsight. Ten zestaw danych jest po prostu fikcyjnego zestawu danych, który jest wymagany do harmonogramu potoku.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok w tym przykładzie ma tylko jedno działanie, którego typ jest: HDInsightMapReduce. Ważne właściwości w kodzie JSON, należą: 

| Właściwość | Uwagi |
|:--- |:--- |
| type |Typ musi być równa **HDInsightMapReduce**. |
| className |Nazwa klasy jest: **wordcount** |
| jarFilePath |Ścieżka do pliku jar z klasą. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zmienić nazwę klastra. |
| jarLinkedService |Połączona usługa Azure Storage, który zawiera plik jar. Ta połączona usługa odnosi się do magazynu, który jest skojarzony z klastrem HDInsight. |
| arguments |Wordcount program przyjmuje dwa argumenty wejściowe i wyjściowe. Wejściowy plik jest plikiem davinci.txt. |
| frequency/interval |Wartości tych właściwości są zgodne wyjściowy zestaw danych. |
| linkedServiceName |odnosi się do usługi połączonej HDInsight, z którą została wcześniej utworzona. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Uruchamianie programów platformy Spark
Możesz użyć działania MapReduce, aby uruchomić programy platformy Spark w klastrze usługi HDInsight Spark. Zobacz [Wywoływanie programów platformy Spark z usługi Azure Data Factory](data-factory-spark.md).  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Zobacz też
* [Działanie technologii hive](data-factory-hive-activity.md)
* [Działania technologii pig](data-factory-pig-activity.md)
* [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

