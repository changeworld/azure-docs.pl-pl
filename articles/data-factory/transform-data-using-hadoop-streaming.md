---
title: Przekształcanie danych za pomocą działaniu przesyłania strumieniowego usługi Hadoop w usłudze fabryka danych Azure | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak używać działaniu przesyłania strumieniowego usługi Hadoop w fabryce danych Azure do przekształcania danych, uruchamiając programy przesyłania strumieniowego usługi Hadoop dla klastra usługi Hadoop.
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
ms.openlocfilehash: 4c2bf83fec3d8f961a84523365e4a98fe3bf7603
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052371"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działaniu przesyłania strumieniowego usługi Hadoop w usłudze fabryka danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-streaming.md)

HDInsight działaniu przesyłania strumieniowego w fabryce danych [potoku](concepts-pipelines-activities.md) wykonuje programy przesyłania strumieniowego usługi Hadoop na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra usługi HDInsight. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji.

Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](introduction.md) i [samouczek: przekształcania danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

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
| description       | Tekst opisujący działanie służy do | Nie       |
| type              | Typ działania działaniu przesyłania strumieniowego usługi Hadoop jest HDInsightStreaming | Yes      |
| linkedServiceName | Odwołanie do klastra usługi HDInsight zarejestrowany jako połączonej usługi z fabryki danych. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| mapowania            | Określa nazwę pliku wykonywalnego mapowania | Yes      |
| Reduktor           | Określa nazwę pliku wykonywalnego reduktor | Yes      |
| łączenia          | Określa nazwę pliku wykonywalnego łączenia | Nie       |
| fileLinkedService | Odwołanie do połączonej usługi magazynu Azure są używane do przechowywania mapowania, łączenia i reduktor na wykonywanie programów. Jeśli nie określisz tej połączonej usługi, usługi połączonej magazynu Azure, zdefiniowane w połączonej usłudze HDInsight jest używany. | Nie       |
| Ścieżka pliku          | Podaj tablicę ścieżka do mapowania, łączenia, i programy reduktor przechowywanych w magazynie Azure odwołuje się fileLinkedService. W ścieżce jest rozróżniana wielkość liter. | Yes      |
| wejście             | Określa ścieżkę WASB do pliku wejściowego dla mapowania. | Yes      |
| output            | Określa reduktor WASB ścieżka do pliku wyjściowego. | Yes      |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) określony przez element scriptLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |
| argumenty         | Określa tablicę argumentów dla zadania usługi Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje           | Określ parametry jako pary klucz wartość dla odwołania do skryptu Hive. | Nie       | 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
