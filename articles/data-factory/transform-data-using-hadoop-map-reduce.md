---
title: Przekształcanie danych za pomocą działania MapReduce z Hadoop w usłudze fabryka danych Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie przetwarzania danych, uruchamiając MapReduce z Hadoop programy na klaster Azure HDInsight z fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: cb7009d0e7f31b2f503ac51d378fd117fff9f9b2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049955"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania MapReduce z Hadoop w usłudze fabryka danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-map-reduce.md)
> * [Bieżąca wersja](transform-data-using-hadoop-map-reduce.md)

Działania HDInsight MapReduce w fabryce danych [potoku](concepts-pipelines-activities.md) wywołuje MapReduce program na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra usługi HDInsight. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](introduction.md) i wykonaj samouczka: [samouczek: przekształcania danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

Zobacz [Pig](transform-data-using-hadoop-pig.md) i [Hive](transform-data-using-hadoop-hive.md) szczegółowe informacje o uruchamianiu Pig/Hive skryptów w usłudze HDInsight klastra z potokiem przy użyciu działań HDInsight Pig i Hive. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
          "-SampleHadoopJobArgument1"
        ],
        "defines": {
          "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szczegóły składni

| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nazwa działania                     | Yes      |
| description       | Tekst opisujący działanie służy do | Nie       |
| type              | Dla działania MapReduce typ działania jest HDinsightMapReduce | Yes      |
| linkedServiceName | Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| className         | Nazwa klasy do wykonania         | Yes      |
| jarLinkedService  | Odwołanie do połączonej usługi magazynu Azure są używane do przechowywania plików Jar. Jeśli nie określisz tej połączonej usługi, usługi połączonej magazynu Azure, zdefiniowane w połączonej usłudze HDInsight jest używany. | Nie       |
| jarFilePath       | Podaj ścieżkę do plików Jar przechowywanych w usłudze Azure Storage odwołuje się jarLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Yes      |
| jarlibs           | Ciąg tablicy ścieżki do plików biblioteki Jar odwołuje się zadania przechowywanych w magazynie Azure zdefiniowane w jarLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Nie       |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) określonym przez jarLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty         | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje           | Określ parametry jako pary klucz wartość dla odwołania do skryptu Hive. | Nie       |



## <a name="example"></a>Przykład
Działania MapReduce HDInsight służy do uruchomienia dowolnego pliku jar MapReduce w klastrze usługi HDInsight. W następujących definicji JSON próbki potoku działanie usługi HDInsight jest skonfigurowany do uruchomienia pliku Mahout JAR.

```json   
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
Można określić żadnych argumentów dla programu MapReduce w **argumenty** sekcji. W czasie wykonywania, zobacz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Rozróżnianie argumentów z argumentami MapReduce, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe,--itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
