---
title: Wywoływanie programu MapReduce z fabryki danych platformy Azure
description: Dowiedz się, jak przetwarzać dane, uruchamiając programy MapReduce w klastrze usługi Azure HDInsight z fabryki danych platformy Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703150"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Wywoływanie programów MapReduce z fabryki danych
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu działania MapReduce w fabryce danych](../transform-data-using-hadoop-map-reduce.md).


Działanie HDInsight MapReduce w [potoku](data-factory-create-pipelines.md) data factory wykonuje programy MapReduce na [własną](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klaster HDInsight oparty na systemie Windows/Linux. W tym artykule opiera się na [działaniach transformacji danych,](data-factory-data-transformation-activities.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Skompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu.  

## <a name="introduction"></a>Wprowadzenie
Potok w fabryce danych platformy Azure przetwarza dane w połączonych usługach magazynu przy użyciu połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano użycie działania mapy HDInsightReduce.

Zobacz [Świnia](data-factory-pig-activity.md) i [gałąź,](data-factory-hive-activity.md) aby uzyskać szczegółowe informacje na temat uruchamiania skryptów Pig/Hive w klastrze HDInsight opartym na systemie Windows/Linux z potoku przy użyciu działań HIInsight Pig i Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON dla hdinsight MapReduce aktywność
W definicji JSON dla działania HDInsight: 

1. Ustaw **typ** **działania** na **HDInsight**.
2. Określ nazwę klasy **classname** właściwości.
3. Określ ścieżkę do pliku JAR, w tym nazwę pliku dla **właściwości jarFilePath.**
4. Określ połączone usługi, która odwołuje się do usługi Azure Blob Storage, który zawiera plik JAR dla **jarLinkedService** właściwości.   
5. Określ wszelkie argumenty programu MapReduce w sekcji **argumenty.** W czasie wykonywania zostanie wyświetlonych kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z MapReduce argumenty, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (-s, --input, --output itp., są opcje natychmiast po ich wartości).

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
   Za pomocą programu HDInsight MapReduce Activity można uruchomić dowolny plik jar MapReduce w klastrze HDInsight. W poniższej przykładowej definicji JSON potoku działania HDInsight jest skonfigurowany do uruchamiania pliku Mahout JAR.

## <a name="sample-on-github"></a>Przykład w usłudze GitHub
Przykład do korzystania z programu HDInsight MapReduce Activity można pobrać z: [Próbki fabryki danych w usłudze GitHub.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample)  

## <a name="running-the-word-count-program"></a>Uruchamianie programu Liczba wyrazów
Potok w tym przykładzie uruchamia program Word Count Map/Reduce w klastrze usługi Azure HDInsight.   

### <a name="linked-services"></a>Połączone usługi
Najpierw utworzysz połączona usługa, aby połączyć usługę Azure Storage, która jest używana przez klaster usługi Azure HDInsight z fabryką danych platformy Azure. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zastąpić **nazwy konta** i **klucza konta** nazwą i kluczem usługi Azure Storage. 

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

#### <a name="azure-hdinsight-linked-service"></a>Usługa połączona usługi Azure HDInsight
Następnie utworzysz połączona usługa, aby połączyć klaster usługi Azure HDInsight z fabryką danych platformy Azure. Jeśli skopiujesz/wklejasz następujący kod, zamień **nazwę klastra HDInsight** na nazwę klastra HDInsight i zmień nazwy użytkownika i wartości haseł.   

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
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Należy określić wyjściowy zestaw danych dla działania mapy HDInsight. Ten zestaw danych jest tylko fikcyjny zestaw danych, który jest wymagany do kierowania harmonogram potoku.  

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
Potok w tym przykładzie ma tylko jedno działanie, które jest typu: HDInsightMapReduce. Niektóre z ważnych właściwości w JSON są: 

| Właściwość | Uwagi |
|:--- |:--- |
| type |Typ musi być ustawiony na **HDInsightMapReduce**. |
| Classname |Nazwa klasy to: **wordcount** |
| jarFilePath |Ścieżka do pliku jar zawierającego klasę. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zmienić nazwy klastra. |
| jarLinkedSługa |Usługa linked Usługi Azure Storage zawierająca plik jar. Ta połączona usługa odnosi się do magazynu skojarzonego z klastrem HDInsight. |
| Argumenty |Program wordcount przyjmuje dwa argumenty, dane wejściowe i dane wyjściowe. Plik wejściowy jest plikiem davinci.txt. |
| frequency/interval |Wartości dla tych właściwości są zgodne z wyjściowym zestawem danych. |
| linkedServiceName |odnosi się do usługi połączonej HDInsight, którą utworzono wcześniej. |

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

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Zobacz też
* [Aktywność gałęzi](data-factory-hive-activity.md)
* [Aktywność świń](data-factory-pig-activity.md)
* [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

