---
title: Przekształcanie danych przy użyciu działania usługi Hadoop Streaming
description: Wyjaśnia, jak używać działania przesyłania strumieniowego usługi Hadoop w Azure Data Factory do przekształcania danych przez uruchamianie programów przesyłania strumieniowego Hadoop w klastrze usługi Hadoop.
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
ms.openlocfilehash: 01237218e30859015b1c8f0a5adeebd8b9bdf7eb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893794"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania usługi Hadoop streaming w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-streaming.md)

Działanie przesyłania strumieniowego usługi HDInsight w [potoku](concepts-pipelines-activities.md) Data Factory wykonuje programy przesyłania strumieniowego Hadoop [](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) we własnym lub [lokalnym klastrze usługi](compute-linked-services.md#azure-hdinsight-linked-service) HDInsight. W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowym do Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](introduction.md) i wykonaj [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Przykład JSON
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
| name              | Nazwa działania                     | Tak      |
| description       | Tekst opisujący działanie używanego działania | Nie       |
| type              | Dla działania przesyłania strumieniowego Hadoop typem działania jest HDInsightStreaming | Tak      |
| linkedServiceName | Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| wzor            | Określa nazwę pliku wykonywalnego mapowania | Tak      |
| zmniejszenie           | Określa nazwę pliku wykonywalnego redukcji | Tak      |
| połączenie          | Określa nazwę pliku wykonywalnego łączenia | Nie       |
| fileLinkedService | Odwołanie do połączonej usługi Azure Storage służącej do przechowywania programów mapowania, łączenia i narzędzi do ograniczania do wykonania. Jeśli nie określisz tej połączonej usługi, zostanie użyta połączona usługa Azure Storage zdefiniowana w połączonej usłudze HDInsight. | Nie       |
| filePath          | Podaj tablicę ścieżek do programów mapowania, łączenia i redukcji przechowywanych w usłudze Azure Storage, która jest określona przez fileLinkedService. W ścieżce jest rozróżniana wielkość liter. | Tak      |
| wejście             | Określa ścieżkę WASB do pliku wejściowego dla mapowania. | Tak      |
| output            | Określa ścieżkę WASB do pliku wyjściowego dla funkcji zmniejszającej. | Tak      |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanej przez klaster HDInsight (lub) określonej przez elementu scriptlinkedservice. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie       |
| arguments         | Określa tablicę argumentów zadania usługi Hadoop. Argumenty są przesyłane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| defines           | Określ parametry jako pary klucz/wartość dla odwołania w skrypcie Hive. | Nie       | 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego Machine Learning](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
