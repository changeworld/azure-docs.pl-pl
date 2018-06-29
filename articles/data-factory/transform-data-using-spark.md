---
title: Przekształcanie danych za pomocą działania Spark w usłudze fabryka danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcania danych, korzystając z polecenia programy Spark przy użyciu działania Spark potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: douglasl
ms.openlocfilehash: abe2fabc505f94f19d4b15a406fc59bf6d6e7ac1
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050339"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania Spark w usłudze fabryka danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-spark.md)
> * [Bieżąca wersja](transform-data-using-spark.md)

Działanie Spark w fabryce danych [potoku](concepts-pipelines-activities.md) wykonuje Spark program na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra usługi HDInsight. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji. Gdy używasz usługi Spark połączony na żądanie usługi fabryka danych automatycznie tworzy klaster Spark dla możesz just-in-time do przetwarzania danych, a następnie usuwa klastra po zakończeniu przetwarzania. 

> [!IMPORTANT]
> Działanie Spark nie obsługuje klastrów HDInsight Spark, które używają usługi Azure Data Lake Store jako podstawowy magazyn.

## <a name="spark-activity-properties"></a>Właściwości działania Spark
Oto przykład definicji JSON działania Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

| Właściwość              | Opis                              | Wymagane |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nazwa działania w potoku.    | Yes      |
| description           | Tekst opisujący działanie robi.  | Nie       |
| type                  | Dla działania Spark typ działania jest HDInsightSpark. | Yes      |
| linkedServiceName     | Nazwa usługi HDInsight Spark połączonej usługi, na którym jest uruchomiony Spark program. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| SparkJobLinkedService | Magazyn Azure połączone usługi, która ma Spark plik zadania, zależności i dzienniki.  Jeśli nie określisz wartości dla tej właściwości, Magazyn skojarzony z klastrem usługi HDInsight jest używany. Wartość tej właściwości można tylko połączoną usługą magazynu Azure. | Nie       |
| rootPath              | Kontener obiektów Blob platformy Azure i folder zawierający plik Spark. Nazwa pliku jest rozróżniana wielkość liter. Odwoływać się do struktury folderów sekcji (dalej) Aby uzyskać szczegółowe informacje o strukturze tego folderu. | Yes      |
| entryFilePath         | Ścieżka względna do folderu głównego Spark kodu/pakietu. Plik wejścia musi być plikiem Python lub pliku JAR. | Yes      |
| className             | Klasy głównym aplikacji Java/Spark      | Nie       |
| argumenty             | Lista argumentów wiersza polecenia do programu Spark. | Nie       |
| proxyUser             | Konto użytkownika do personifikacji do wykonania programu Spark | Nie       |
| sparkConfig           | Określ wartości dla właściwości konfiguracji Spark wymienione w temacie: [konfiguracji Spark — właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie       |
| getDebugInfo          | Określa, kiedy Spark pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) został określony przez sparkJobLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |

## <a name="folder-structure"></a>Struktura folderów
Platforma Spark zadania są rozszerzalne więcej niż zadań Pig/Hive. W przypadku zadań Spark, możesz podać wiele zależności takich jak jar pakietów (umieszczona w języku java ścieżki klasy), pliki języka python (dotyczącymi PYTHONPATH) i innych plików.

Utwórz następującą strukturę folderów w magazynie obiektów Blob platformy Azure, odwołuje się usługa HDInsight połączone. Natomiast przekazywanie plików zależnych do odpowiednich podfolderów w folderze głównym reprezentowany przez **entryFilePath**. Na przykład Przekaż python pliki są kopiowane do podfolderu pyFiles i pliki jar do podfolderu słoików folderu głównego. W czasie wykonywania usługi fabryka danych oczekuje następującej struktury folderu w magazynie obiektów Blob platformy Azure:     

| Ścieżka                  | Opis                              | Wymagane | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | Ścieżka katalogu głównego zadania Spark w połączonej usługi magazynu | Yes      | Folder |
| &lt;Zdefiniowane przez użytkownika &gt; | Ścieżka do pliku wpisu zadania Spark | Yes      | Plik   |
| . / jars                | Wszystkie pliki w tym folderze są przekazywane i dotyczącymi klasy java klastra | Nie       | Folder |
| . / pyFiles             | Wszystkie pliki w tym folderze są przekazywane i dotyczącymi PYTHONPATH klastra | Nie       | Folder |
| . / pliki               | Wszystkie pliki w tym folderze są przekazywane i dotyczącymi Moduł wykonujący katalog roboczy | Nie       | Folder |
| . / archiwa            | Wszystkie pliki w tym folderze są nieskompresowanych | Nie       | Folder |
| . / dzienników                | Folder zawierający dzienniki w klastrze Spark. | Nie       | Folder |

Oto przykład do magazynu zawierającego dwa pliki zadania Spark w magazynie obiektów Blob Azure przywoływany przez usługę HDInsight połączone.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
