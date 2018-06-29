---
title: Wywołaj Program MapReduce z fabryki danych Azure
description: Informacje o sposobie przetwarzania danych, uruchamiając programy MapReduce na klaster Azure HDInsight z fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: edbef08eaa100248368d7f0b23171f15b52ec56a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050951"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Wywoływanie programów MapReduce z fabryki danych
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
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [Przekształcanie danych za pomocą działania MapReduce w fabryce danych](../transform-data-using-hadoop-map-reduce.md).


Działania HDInsight MapReduce w fabryce danych [potoku](data-factory-create-pipelines.md) wykonuje programy MapReduce na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastrów HDInsight opartych na systemie Windows/Linux. W tym artykule opiera się na [działań przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) i wykonaj samouczka: [kompilacji swój pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu.  

## <a name="introduction"></a>Wprowadzenie
Potok w fabryce danych Azure przetwarza dane w usługach magazynu połączone, przy użyciu obliczeniowego połączonej usługi. Zawiera sekwencję działań, gdzie każde działanie wykonuje operację przetwarzania specyficznego dla. W tym artykule opisano, za pomocą usługi HDInsight działania MapReduce.

Zobacz [Pig](data-factory-pig-activity.md) i [Hive](data-factory-hive-activity.md) szczegółowe informacje o uruchamianiu Pig/Hive skryptów w usłudze HDInsight opartych na systemie Windows/Linux klastra z potokiem przy użyciu działań HDInsight Pig i Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON dla działania HDInsight MapReduce
W definicji JSON dla działania usługi HDInsight: 

1. Ustaw **typu** z **działania** do **HDInsight**.
2. Określ nazwę klasy dla **className** właściwości.
3. Określ ścieżkę do pliku JAR, łącznie z nazwą pliku dla **jarFilePath** właściwości.
4. Określ połączonej usługi, która odwołuje się do magazynu obiektów Blob Azure, który zawiera plik JAR dla **jarLinkedService** właściwości.   
5. Określ wszelkie argumenty dla programu MapReduce w **argumenty** sekcji. W czasie wykonywania, zobacz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Rozróżnianie argumentów z argumentami MapReduce, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe,--itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości).

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
Działania MapReduce HDInsight służy do uruchomienia dowolnego pliku jar MapReduce w klastrze usługi HDInsight. W następujących definicji JSON próbki potoku działanie usługi HDInsight jest skonfigurowany do uruchomienia pliku Mahout JAR.

## <a name="sample-on-github"></a>Przykładem w witrynie GitHub
Możesz pobrać przykład dotyczące używania działania MapReduce HDInsight z: [próbek fabryki danych w serwisie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Uruchomienie programu wyrazów
Potok, w tym przykładzie uruchamia program wyrazów mapy/Zmniejsz w klastrze usługi Azure HDInsight.   

### <a name="linked-services"></a>Połączone usługi
Najpierw należy utworzyć połączonej usługi, aby połączyć magazyn Azure, który jest używany przez klaster Azure HDInsight z fabryką danych Azure. Jeśli użytkownik skopiuj/Wklej następujący kod, pamiętaj zastąpić **nazwa konta** i **klucz konta** z nazwy i klucza magazynu Azure. 

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
Następnie można utworzyć połączonej usługi do połączenia z klastrem usługi HDInsight Azure z fabryką danych Azure. Jeśli użytkownik skopiuj/Wklej następujący kod, Zastąp **nazwy klastra usługi HDInsight** z nazwą klastra usługi HDInsight, a zmiany wartości nazwy i hasła użytkownika.   

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
Potok, w tym przykładzie nie przyjmuje żadnych danych wejściowych. Wyjściowy zestaw danych można określić dla działania MapReduce HDInsight. Ten zestaw danych jest tylko fikcyjny zestawu danych, który jest wymagany do kierowania harmonogram potoku.  

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
Potok, w tym przykładzie jest tylko jedno działanie, które jest typu: HDInsightMapReduce. Ważne właściwości w formacie JSON, należą: 

| Właściwość | Uwagi |
|:--- |:--- |
| type |Typ musi mieć ustawioną **HDInsightMapReduce**. |
| className |Nazwa klasy jest: **wordcount** |
| jarFilePath |Ścieżka do pliku jar zawierający klasę. Jeśli użytkownik skopiuj/Wklej następujący kod, nie zapomnij zmienić nazwę klastra. |
| jarLinkedService |Usługa Azure Storage połączone usługi, który zawiera plik jar. Tej połączonej usługi odwołuje się do magazynu, który jest skojarzony z klastrem usługi HDInsight. |
| argumenty |Wordcount program przyjmuje dwa argumenty wejściowe i wyjściowe. Wejściowy plik jest plikiem davinci.txt. |
| frequency/interval |Wartości tych właściwości zgodne wyjściowego zestawu danych. |
| linkedServiceName |odnosi się do usługi HDInsight połączone, którą ma zostać utworzony wcześniej. |

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

## <a name="run-spark-programs"></a>Uruchamianie programów Spark
Możesz użyć działania MapReduce, aby uruchomić programy platformy Spark w klastrze usługi HDInsight Spark. Zobacz [Wywoływanie programów platformy Spark z usługi Azure Data Factory](data-factory-spark.md).  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Zobacz też
* [Działanie gałęzi](data-factory-hive-activity.md)
* [Działanie pig](data-factory-pig-activity.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

