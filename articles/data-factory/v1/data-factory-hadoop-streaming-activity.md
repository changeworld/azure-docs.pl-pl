---
title: Przekształcanie danych przy użyciu aktywności przesyłania strumieniowego usługi Hadoop — Azure
description: Dowiedz się, jak za pomocą działania przesyłania strumieniowego Hadoop w fabryce danych platformy Azure do przekształcania danych przez uruchamianie programów przesyłania strumieniowego Hadoop w klastrze HDInsight na żądanie/własne.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703365"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności przesyłania strumieniowego usługi Hadoop w fabryce danych platformy Azure
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu aktywności przesyłania strumieniowego Hadoop w fabryce danych](../transform-data-using-hadoop-streaming.md).


Za pomocą działania HDInsightStreamingActivity wywołać zadanie przesyłania strumieniowego Hadoop z potoku usługi Azure Data Factory. Poniższy fragment kodu JSON pokazuje składnię używania funkcji HDInsightStreamingActivity w pliku JSON potoku. 

Aktywność przesyłania strumieniowego HDInsight w [potoku](data-factory-create-pipelines.md) usługi Data Factory wykonuje programy przesyłania strumieniowego Hadoop [na własny](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klaster HDInsight oparty na systemie Windows/Linux. W tym artykule opiera się na [działaniach transformacji danych,](data-factory-data-transformation-activities.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Skompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Próbka JSON
Klaster HDInsight jest automatycznie wypełniany przykładowymi programami (wc.exe i cat.exe) oraz danymi (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster HDInsight jest nazwą samego klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektu blob skojarzone będzie myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Pamiętaj o następujących kwestiach:

1. Ustaw **nazwę linkedServiceName** na nazwę połączonej usługi, która wskazuje klaster HDInsight, na którym jest uruchamiane zadanie mapowania strumieniowego.
2. Ustaw typ działania na **HDInsightStreaming**.
3. Dla właściwości **mapera** określ nazwę pliku wykonywalnego mapera. W tym przykładzie cat.exe jest plikiem wykonywalnym mapera.
4. Dla właściwości **reduktora** określ nazwę pliku wykonywalnego reduktora. W tym przykładzie wc.exe jest wykonywalnym reduktorem.
5. Dla właściwości typ **wejściowy,** określ plik wejściowy (w tym lokalizację) dla mapera. W przykładzie: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample jest kontenerem obiektów blob, example/data/Gutenberg jest folderem, a davinci.txt jest obiektem blob.
6. Dla właściwości typu **wyjściowego** określ plik wyjściowy (w tym lokalizację) reduktora. Dane wyjściowe zadania przesyłania strumieniowego Hadoop jest zapisywany do lokalizacji określonej dla tej właściwości.
7. W sekcji **ścieżkach plików** określ ścieżki dla plików wykonywalnych mapera i reduktora. W przykładzie: "adfsample/example/apps/wc.exe", adfsample jest kontenerem obiektów blob, example/apps jest folderem, a wc.exe jest plikiem wykonywalnym.
8. Dla **fileLinkedService** właściwości, określ usługę połączone usługi Azure Storage, która reprezentuje magazyn platformy Azure, który zawiera pliki określone w filePaths sekcji.
9. Dla **argumentów** właściwości, należy określić argumenty dla zadania przesyłania strumieniowego.
10. Właściwość **getDebugInfo** jest elementem opcjonalnym. Gdy jest ustawiona na Błąd, dzienniki są pobierane tylko na niepowodzenie. Gdy jest ustawiona na Zawsze, dzienniki są zawsze pobierane niezależnie od stanu wykonania.

> [!NOTE]
> Jak pokazano w przykładzie, należy określić wyjściowy zestaw danych dla działania przesyłania strumieniowego Hadoop dla **danych wyjściowych** właściwości. Ten zestaw danych jest tylko fikcyjny zestaw danych, który jest wymagany do kierowania harmonogram potoku. Nie trzeba określać żadnych danych wejściowych zestawu danych dla działania dla **inputs** właściwości.  
> 
> 

## <a name="example"></a>Przykład
Potok w tym instruktażu uruchamia program Map/Reduce z licznika wyrazów w klastrze usługi Azure HDInsight. 

### <a name="linked-services"></a>Połączone usługi
#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Najpierw utworzysz połączona usługa, aby połączyć usługę Azure Storage, która jest używana przez klaster usługi Azure HDInsight z fabryką danych platformy Azure. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zastąpić nazwy konta i klucza konta nazwą i kluczem usługi Azure Storage. 

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
Następnie utworzysz połączona usługa, aby połączyć klaster usługi Azure HDInsight z fabryką danych platformy Azure. Jeśli skopiujesz/wklejasz następujący kod, zamień nazwę klastra HDInsight na nazwę klastra HDInsight i zmień nazwy użytkownika i wartości haseł. 

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
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Należy określić wyjściowy zestaw danych dla działania przesyłania strumieniowego HDInsight. Ten zestaw danych jest tylko fikcyjny zestaw danych, który jest wymagany do kierowania harmonogram potoku. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok w tym przykładzie ma tylko jedno działanie, które jest typu: **HDInsightStreaming**. 

Klaster HDInsight jest automatycznie wypełniany przykładowymi programami (wc.exe i cat.exe) oraz danymi (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster HDInsight jest nazwą samego klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektu blob skojarzone będzie myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Zobacz też
* [Aktywność gałęzi](data-factory-hive-activity.md)
* [Aktywność świń](data-factory-pig-activity.md)
* [Działanie mapreduce](data-factory-map-reduce.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

