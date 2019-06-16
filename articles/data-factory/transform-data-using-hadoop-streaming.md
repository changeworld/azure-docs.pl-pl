---
title: Przekształcanie danych za pomocą działania przesyłania strumieniowego usługi Hadoop w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Opis sposobu używania działania przesyłania strumieniowego usługi Hadoop w usłudze Azure Data Factory do przekształcania danych, uruchamiając programy przesyłania strumieniowego usługi Hadoop w klastrze platformy Hadoop.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 0d8267f1cd65f78d5e98ae9d288d5fa5c4214420
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848252"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania przesyłania strumieniowego usługi Hadoop w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-streaming.md)

Działania przesyłania strumieniowego HDInsight w usłudze Data Factory [potoku](concepts-pipelines-activities.md) wykonuje programy przesyłania strumieniowego usługi Hadoop na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych.

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Przykładowy kod JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
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

| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nazwa działania                     | Yes      |
| description       | Tekst opisujący przeznaczenie działania | Nie       |
| type              | Działania przesyłania strumieniowego usługi Hadoop typ działania jest HDInsightStreaming | Yes      |
| linkedServiceName | Odwołanie do klastra HDInsight zarejestrowany jako połączonej usługi w usłudze Data Factory. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| mapowania            | Określa nazwę pliku wykonywalnego mapowania | Yes      |
| Reduktor           | Określa nazwę pliku wykonywalnego reduktor | Tak      |
| funkcja łączenia          | Określa nazwę pliku wykonywalnego łączenia | Nie       |
| fileLinkedService | Odwołanie do połączonej usługi magazynu platformy Azure używane do przechowywania mapowania, łączenia i reduktor programy do wykonania. Jeśli nie określisz ta połączona usługa połączona usługa Azure Storage zdefiniowane w połączonej usługi HDInsight jest używany. | Nie       |
| filePath          | Podaj tablicę ścieżka do mapowania, łączenia, i fileLinkedService odwołuje się programy reduktor przechowywanych w usłudze Azure Storage. W ścieżce jest rozróżniana wielkość liter. | Tak      |
| wejście             | Ścieżka WASB do pliku wejściowego do mapowania. | Tak      |
| wyjście            | Ścieżka WASB do pliku wyjściowego dla reduktor. | Tak      |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanego przez klaster HDInsight (lub) określonej za pomocą elementu scriptLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| arguments         | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia w odniesieniu do każdego zadania. | Nie       |
| defines           | Określ parametry jako pary klucz/wartość do odwoływania się do skryptu programu Hive. | Nie       | 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
