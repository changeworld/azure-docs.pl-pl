---
title: Przekształcanie danych za pomocą działania platformy Spark w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcać dane, uruchamiając programy platformy Spark w potoku usługi fabryka danych Azure przy użyciu działania platformy Spark.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: cdf4dba3996668b3c9fe31df10050ff2cbff6cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387829"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania platformy Spark w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-spark.md)
> * [Bieżąca wersja](transform-data-using-spark.md)

Działania platformy Spark w usłudze Data Factory [potoku](concepts-pipelines-activities.md) uruchamia program platformy Spark na [własne](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych. Gdy używasz usługi połączone platformy Spark na żądanie, Data Factory automatycznie tworzy klaster platformy Spark na potrzeby możesz just in time do przetwarzania danych, a następnie usuwa klaster, po zakończeniu przetwarzania. 

> [!IMPORTANT]
> Działania platformy Spark nie obsługuje klastry HDInsight Spark, korzystających z usługi Azure Data Lake Store jako magazynu głównego.

## <a name="spark-activity-properties"></a>Właściwości działania platformy Spark
Oto przykład definicji JSON działania platformy Spark:    

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

W poniższej tabeli opisano właściwości JSON używanych w definicji JSON:

| Właściwość              | Opis                              | Wymagane |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nazwa działania w potoku.    | Yes      |
| description           | Tekst opisujący, co działanie robi.  | Nie       |
| type                  | Typ działania jest HDInsightSpark działania platformy Spark. | Yes      |
| linkedServiceName     | Nazwa HDInsight Spark połączonej usługi, na którym działa program platformy Spark. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| SparkJobLinkedService | Usługi Azure Storage połączone usługi, która ma platformy Spark, zależności, dzienniki i plik zadania.  Jeśli nie określisz wartości dla tej właściwości, jest używany Magazyn skojarzony z klastrem HDInsight. Wartość tej właściwości można tylko połączoną usługę Azure Storage. | Nie       |
| rootPath              | Kontener obiektów Blob platformy Azure i folder zawierający plik platformy Spark. Nazwa pliku jest rozróżniana wielkość liter. Zobacz strukturę folderów sekcji (dalej) Aby uzyskać szczegółowe informacje o strukturze tego folderu. | Yes      |
| entryFilePath         | Względna ścieżka do folderu głównego kodu lub pakietu platformy Spark. Plik w języku Python lub pliku JAR, musi być pliku wejściowego. | Yes      |
| className             | Główna klasa platformy Java lub Spark aplikacji      | Nie       |
| argumenty             | Lista argumentów wiersza polecenia do programu platformy Spark. | Nie       |
| proxyUser             | Konto użytkownika do personifikacji, aby wykonać program platformy Spark | Nie       |
| sparkConfig           | Określ wartości dla właściwości konfiguracji aparatu Spark wymienione w temacie: [Platforma Spark jest Konfiguracja — właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie       |
| getDebugInfo          | Określa, kiedy Spark pliki dziennika są kopiowane do usługi Azure storage używany przez klaster HDInsight (lub) określony przez sparkJobLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie       |

## <a name="folder-structure"></a>Struktura folderów
Zadania Spark są bardziej extensible niż zadania programów Pig/Hive. W przypadku zadań platformy Spark, możesz podać wiele zależności takich jak jar pakietów (umieszczona w ścieżce klas w języku java), pliki języka python (umieszczona na PYTHONPATH) i innych plików.

Utwórz następującą strukturę folderów w usłudze Azure Blob storage przywoływany przez usługę HDInsight połączone. Następnie przekaż pliki zależne do odpowiednich podfolderach w folderze głównym, reprezentowane przez **właściwość entryFilePath**. Na przykład Przekaż pliki języka python do podfolderu pyFiles i pliki jar do podfolderu plikach JAR w folderze głównym. W czasie wykonywania usługa Data Factory oczekuje następującą strukturę folderów w usłudze Azure Blob storage:     

| Ścieżka                  | Opis                              | Wymagane | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | Ścieżka katalogu głównego zadania Spark w połączonej usługi storage | Yes      | Folder |
| &lt;zdefiniowane przez użytkownika &gt; | Ścieżka do pliku wejściowego zadania Spark | Yes      | Plik   |
| . / jars                | Wszystkie pliki w tym folderze są przekazywane i umieszczane w ścieżce klasy java klastra | Nie       | Folder |
| . / pyFiles             | Wszystkie pliki w tym folderze są przekazywane i umieszczane w PYTHONPATH klastra | Nie       | Folder |
| . / plików               | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczego funkcji wykonawczej | Nie       | Folder |
| . / archiwa            | Wszystkie pliki w tym folderze znajdują się bez kompresji | Nie       | Folder |
| . / dzienniki                | Folder, który zawiera dzienników z klastra Spark. | Nie       | Folder |

Oto przykład dla magazynu zawierającego dwa pliki zadania Spark w usłudze Azure Storage Blob odwołuje się usługa HDInsight połączone.

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
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
