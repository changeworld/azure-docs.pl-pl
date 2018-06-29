---
title: Przekształcanie danych za pomocą działania usługi Hadoop Hive w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używasz działania Hive w fabryce danych Azure do uruchamiania zapytań Hive w klastrze usługi HDInsight na — żądanie/swój własny.
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
ms.openlocfilehash: fc7c2c49de582a413b49d31c4b4e062d81e5e6ae
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051328"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania usługi Hadoop Hive w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-hive-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-hive.md)

Działanie HDInsight Hive w fabryce danych [potoku](concepts-pipelines-activities.md) wykonuje zapytania Hive na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra usługi HDInsight. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](introduction.md) i [samouczek: przekształcania danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| type                | Dla gałęzi działania typu działania jest HDinsightHive | Yes      |
| linkedServiceName   | Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| scriptLinkedService | Odwołanie do połączonej usługi magazynu Azure są używane do przechowywania skryptu Hive. Jeśli nie określisz tej połączonej usługi, usługi połączonej magazynu Azure, zdefiniowane w połączonej usłudze HDInsight jest używany. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanych w magazynie Azure odwołuje się element scriptLinkedService. Nazwa pliku jest rozróżniana wielkość liter. | Yes      |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) określony przez element scriptLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty           | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz wartość dla odwołania do skryptu Hive. | Nie       |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)

