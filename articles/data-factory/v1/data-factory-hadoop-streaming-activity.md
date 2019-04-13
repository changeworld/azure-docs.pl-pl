---
title: Przekształcanie danych za pomocą działania przesyłania strumieniowego usługi Hadoop — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania przesyłania strumieniowego usługi Hadoop w usłudze Azure data factory do przekształcania danych, uruchamiając programy przesyłania strumieniowego usługi Hadoop na — żądanie/swój własny klaster HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: dd00c0a2998009ce6c39ca19abb25a2548682cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523226"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania przesyłania strumieniowego usługi Hadoop w usłudze Azure Data Factory
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą usługi Hadoop, przesyłanie strumieniowe działania w usłudze Data Factory](../transform-data-using-hadoop-streaming.md).


Można użyć działania HDInsightStreamingActivity wywołania zadania przesyłania strumieniowego usługi Hadoop z potoku usługi Azure Data Factory. Poniższy fragment kodu JSON przedstawiono składnię za pomocą HDInsightStreamingActivity w pliku JSON potoku. 

Działania przesyłania strumieniowego HDInsight w usłudze Data Factory [potoku](data-factory-create-pipelines.md) wykonuje programy przesyłania strumieniowego usługi Hadoop na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight z systemem Windows/Linux. W tym artykule opiera się na [działania przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i wykonać instrukcje z samouczka: [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Przykładowy kod JSON
Klaster HDInsight zostanie automatycznie wypełniona programów przykład (wc.exe i cat.exe) i danych (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster HDInsight jest nazwą klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektów blob skojarzony będzie myhdicluster. 

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

1. Ustaw **linkedServiceName** do nazwy połączonej usługi, która wskazuje usługi HDInsight cluster uruchamiania zadania mapreduce przesyłania strumieniowego.
2. Ustaw typ działania, aby **HDInsightStreaming**.
3. Aby uzyskać **mapowania** właściwości, określ nazwę pliku wykonywalnego mapowania. W tym przykładzie cat.exe jest mapowania pliku wykonywalnego.
4. Aby uzyskać **reduktor** właściwości, określ nazwę pliku wykonywalnego reduktor. W tym przykładzie wc.exe jest reduktor pliku wykonywalnego.
5. Aby uzyskać **wejściowych** właściwość typu, określ plik wejściowy (w tym lokalizację) dla mapowania. W przykładzie: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample jest kontener obiektów blob, przykład/data/Gutenberg to folder, a davinci.txt jest obiekt blob.
6. Aby uzyskać **dane wyjściowe** właściwość typu, określ plik wyjściowy (w tym lokalizację) reduktor. Dane wyjściowe zadania przesyłania strumieniowego usługi Hadoop jest zapisywany w lokalizacji określonej dla tej właściwości.
7. W **filePaths** sekcji, określ ścieżki dla plików wykonywalnych mapowania i reduktor. W przykładzie: "adfsample/example/apps/wc.exe" adfsample jest kontener obiektów blob, przykładowe/aplikacje jest folder i wc.exe jest plik wykonywalny.
8. Aby uzyskać **fileLinkedService** właściwości, określ połączoną usługę Azure Storage reprezentujący magazynu platformy Azure, który zawiera pliki określone w sekcji filePaths.
9. Aby uzyskać **argumenty** właściwości określenie argumentów dla zadania przesyłania strumieniowego.
10. **Getdebuginfo —** właściwości jest elementem opcjonalnym. Jeśli ustawiono błędów, dzienniki są pobierane tylko w przypadku niepowodzenia. Jeśli ustawiono zawsze, dzienniki będą pobierane zawsze niezależnie od stanu wykonywania.

> [!NOTE]
> Jak pokazano w przykładzie, podajesz wyjściowy zestaw danych w działaniu przesyłania strumieniowego usługi Hadoop dla **generuje** właściwości. Ten zestaw danych jest po prostu fikcyjnego zestawu danych, który jest wymagany do harmonogramu potoku. Nie należy określić wszystkie wejściowy zestaw danych dla działania dotyczące **dane wejściowe** właściwości.  
> 
> 

## <a name="example"></a>Przykład
Potok w tym przewodniku uruchamia program Map/Reduce przesyłania strumieniowego wyrazów w klastrze Azure HDInsight. 

### <a name="linked-services"></a>Połączone usługi
#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Najpierw utworzysz połączoną usługę służącą do połączenia magazynu platformy Azure, w którym jest używany przez klaster usługi Azure HDInsight w usłudze Azure data factory. Jeśli kopiujesz/wklejasz następujący kod, nie zapomnij zastąpić nazwę konta i klucz konta za pomocą nazwy i klucza usługi Azure Storage. 

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
Następnie utworzysz połączoną usługę służącą do łączenie klastra usługi Azure HDInsight w usłudze Azure data factory. Jeśli kopiujesz/wklejasz następujący kod, zastąp nazwę klastra HDInsight z nazwą klastra usługi HDInsight, a następnie zmień wartości nazwy i hasła użytkownika. 

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
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Wyjściowy zestaw danych należy określić dla działania przesyłania strumieniowego HDInsight. Ten zestaw danych jest po prostu fikcyjnego zestawu danych, który jest wymagany do harmonogramu potoku. 

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
Potok w tym przykładzie ma tylko jedno działanie, którego typ jest: **HDInsightStreaming**. 

Klaster HDInsight zostanie automatycznie wypełniona programów przykład (wc.exe i cat.exe) i danych (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster HDInsight jest nazwą klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektów blob skojarzony będzie myhdicluster.  

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
* [Działanie technologii hive](data-factory-hive-activity.md)
* [Działania technologii pig](data-factory-pig-activity.md)
* [Działania technologii MapReduce](data-factory-map-reduce.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

