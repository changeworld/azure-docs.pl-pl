---
title: Przekształcanie danych za pomocą działania usługi Hadoop, Pig w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używasz działania Pig w fabryce danych Azure do wykonywania skryptów usługi Pig w klastrze usługi HDInsight na — żądanie/swój własny.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 5e1329c10336613f8ce5555235ab49db1cfad5bb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania usługi Hadoop, Pig w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-pig-activity.md)
> * [Wersja 2 — wersja zapoznawcza](transform-data-using-hadoop-pig.md)

Działanie HDInsight Pig w fabryce danych [potoku](concepts-pipelines-activities.md) wykonuje zapytania Pig na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra usługi HDInsight. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [Pig działania w wersji 1](v1/data-factory-pig-activity.md).

Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](introduction.md) i [samouczek: przekształcania danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Szczegóły składni

| Właściwość            | Opis                              | Wymagane |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nazwa działania                     | Yes      |
| description         | Tekst opisujący działanie służy do | Nie       |
| type                | Dla gałęzi działania typu działania jest HDinsightPig | Yes      |
| linkedServiceName   | Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| scriptLinkedService | Odwołanie do połączonej usługi magazynu Azure są używane do przechowywania skrypt programu Pig do wykonania. Jeśli nie określisz tej połączonej usługi, usługi połączonej magazynu Azure, zdefiniowane w połączonej usłudze HDInsight jest używany. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanych w magazynie Azure odwołuje się element scriptLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Nie       |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) określony przez element scriptLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| Argumenty           | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz wartość dla odwołania do skryptu Pig. | Nie       |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
