---
title: Przekształcanie danych za pomocą działaniu przesyłania strumieniowego usługi Hadoop - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używasz działaniu przesyłania strumieniowego usługi Hadoop w fabryce danych Azure do przekształcania danych, uruchamiając programy przesyłania strumieniowego usługi Hadoop w klastrze usługi HDInsight na — żądanie/swój własny.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e405f99388f699b3f1e88b9e84f52f9a4380228c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621976"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działaniu przesyłania strumieniowego usługi Hadoop w usłudze fabryka danych Azure
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą usługi Hadoop przesyłania strumieniowego działania w fabryce danych w wersji 2](../transform-data-using-hadoop-streaming.md).


Można użyć działania HDInsightStreamingActivity wywołania zadania przesyłania strumieniowego usługi Hadoop z potoku fabryki danych Azure. Poniższy fragment kodu JSON przedstawiono składnię przy użyciu HDInsightStreamingActivity w pliku JSON potoku. 

HDInsight działaniu przesyłania strumieniowego w fabryce danych [potoku](data-factory-create-pipelines.md) wykonuje programy przesyłania strumieniowego usługi Hadoop na [własne](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastrów HDInsight opartych na systemie Windows/Linux. W tym artykule opiera się na [działań przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

> [!NOTE] 
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) i wykonaj samouczka: [kompilacji swój pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Przykładowy kod JSON
Klaster usługi HDInsight jest wypełniane automatycznie przy użyciu programów przykład (wc.exe i cat.exe) i data (davinci.txt). Domyślnie nazwa kontenera, w którym jest używane przez klaster usługi HDInsight jest nazwą klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektów blob skojarzony byłoby myhdicluster. 

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

1. Ustaw **linkedServiceName** do nazwy połączonej usługi, która wskazuje z HDInsight klastra uruchamiania przesyłania strumieniowego zadania mapreduce.
2. Ustaw typ działanie, aby **HDInsightStreaming**.
3. Aby uzyskać **mapowania** właściwości, określ nazwę pliku wykonywalnego mapowania. W tym przykładzie cat.exe jest mapowania pliku wykonywalnego.
4. Aby uzyskać **reduktor** właściwości, określ nazwę pliku wykonywalnego reduktor. W tym przykładzie wc.exe jest reduktor pliku wykonywalnego.
5. Aby uzyskać **wejściowych** typu właściwości, określ plik wejściowy (w tym o lokalizacji) dla mapowania. W tym przykładzie: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample jest kontenera obiektów blob, przykład/data/Gutenberg jest folder i davinci.txt jest obiektu blob.
6. Aby uzyskać **dane wyjściowe** typu właściwości, określ plik wyjściowy (w tym o lokalizacji) dla reduktor. Dane wyjściowe zadania przesyłania strumieniowego usługi Hadoop jest zapisany w lokalizacji określonej dla tej właściwości.
7. W **filePaths** sekcji, określ ścieżki dla plików wykonywalnych mapowania i reduktor. W tym przykładzie: "adfsample/example/apps/wc.exe" adfsample jest kontenera obiektów blob, przykład/aplikacji jest folder i wc.exe jest plikiem wykonywalnym.
8. Aby uzyskać **fileLinkedService** właściwości, określ połączoną usługą magazynu Azure reprezentujący magazynu Azure, który zawiera pliki określone w sekcji filePaths.
9. Dla **argumenty** właściwości, określ argumenty dla zadania przesyłania strumieniowego.
10. **GetDebugInfo** właściwości to opcjonalny element. Jeśli ustawiono awarii, dzienniki są pobierane tylko w przypadku awarii. Gdy ma wartość Always, dzienniki są zawsze pobierane niezależnie od stanu wykonywania.

> [!NOTE]
> Jak pokazano w przykładzie, podajesz wyjściowy zestaw danych w działaniu przesyłania strumieniowego usługi Hadoop dla **generuje** właściwości. Ten zestaw danych jest tylko fikcyjny zestawu danych, który jest wymagany do kierowania harmonogram potoku. Nie należy określić wszystkie wejściowy zestaw danych działania dla **dane wejściowe** właściwości.  
> 
> 

## <a name="example"></a>Przykład
Potoku, w tym przewodniku uruchamia program mapy/Zmniejsz przesyłania strumieniowego wyrazów w klastrze usługi Azure HDInsight. 

### <a name="linked-services"></a>Połączone usługi
#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Najpierw należy utworzyć połączonej usługi, aby połączyć magazyn Azure, który jest używany przez klaster Azure HDInsight z fabryką danych Azure. Jeśli użytkownik skopiuj/Wklej następujący kod, pamiętaj zastąpić nazwę konta i klucz konta o nazwie i klucza magazynu Azure. 

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
Następnie można utworzyć połączonej usługi do połączenia z klastrem usługi HDInsight Azure z fabryką danych Azure. Jeśli użytkownik skopiuj/Wklej następujący kod, zastąp nazwą klastra usługi HDInsight nazwą klastra usługi HDInsight i zmień wartości nazwy i hasła użytkownika. 

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
Potok, w tym przykładzie nie przyjmuje żadnych danych wejściowych. Podajesz wyjściowy zestaw danych w działaniu przesyłania strumieniowego usługi HDInsight. Ten zestaw danych jest tylko fikcyjny zestawu danych, który jest wymagany do kierowania harmonogram potoku. 

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
Potok, w tym przykładzie jest tylko jedno działanie, które jest typu: **HDInsightStreaming**. 

Klaster usługi HDInsight jest wypełniane automatycznie przy użyciu programów przykład (wc.exe i cat.exe) i data (davinci.txt). Domyślnie nazwa kontenera, w którym jest używane przez klaster usługi HDInsight jest nazwą klastra. Na przykład jeśli nazwa klastra jest myhdicluster, nazwa kontenera obiektów blob skojarzony byłoby myhdicluster.  

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
* [Działanie gałęzi](data-factory-hive-activity.md)
* [Działanie pig](data-factory-pig-activity.md)
* [Działania MapReduce](data-factory-map-reduce.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

