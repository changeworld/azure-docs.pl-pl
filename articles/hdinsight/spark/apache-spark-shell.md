---
title: Używanie interaktywnej powłoki iskry w usłudze Azure HDInsight
description: Interaktywna powłoka Spark shell zapewnia proces odczytu i wykonywania-drukowania do uruchamiania poleceń Platformy Spark po jednym na raz i wyświetlanie wyników.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162807"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Uruchom Apache Spark z powłoki iskry

Interaktywna [powłoka Apache Spark](https://spark.apache.org/) Shell zapewnia środowisko REPL (pętla odczytu i drukowania) do uruchamiania poleceń Platformy Spark po jednym naraz i wyświetlania wyników. Ten proces jest przydatny do tworzenia i debugowania. Platforma Spark udostępnia jedną powłokę dla każdego z obsługiwanych języków: Scala, Python i R.

## <a name="run-an-apache-spark-shell"></a>Uruchamianie powłoki iskry Apache

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark zapewnia powłoki dla Scala (iskrzenie powłoki) i Python (pyspark). W sesji SSH wprowadź *jedno* z następujących poleceń:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Jeśli zamierzasz użyć dowolnej opcjonalnej konfiguracji, upewnij się, że najpierw przejrzysz [wyjątek OutOfMemoryError dla Apache Spark](./apache-spark-troubleshoot-outofmemory.md).

1. Kilka podstawowych przykładowych poleceń. Wybierz odpowiedni język:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Kwerenda z plikiem CSV. Zwróć uwagę na `spark-shell` poniższy język działa dla i `pyspark`.

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Kwerenda pliku CSV i przechowywania wyników w zmiennej:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Wyświetl wyniki:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Zakończ

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession i SparkContext wystąpienia

Domyślnie po uruchomieniu powłoki Spark, wystąpienia SparkSession i SparkContext są automatycznie tworzone dla Ciebie.

Aby uzyskać dostęp do instancji `spark`SparkSession, wprowadź . Aby uzyskać dostęp do instancji `sc`SparkContext, wprowadź .

## <a name="important-shell-parameters"></a>Ważne parametry powłoki

Polecenie Powłoka`spark-shell`iskry ( lub `pyspark`) obsługuje wiele parametrów wiersza polecenia. Aby wyświetlić pełną listę parametrów, uruchom `--help`powłokę Iskry za pomocą przełącznika . Niektóre z tych parametrów `spark-submit`mogą dotyczyć tylko , które powłoki Spark zawija.

| switch | description | przykład |
| --- | --- | --- |
| --master MASTER_URL | Określa główny adres URL. W hdinsight ta wartość `yarn`jest zawsze . | `--master yarn`|
| --słoiki JAR_LIST | Oddzielona przecinkami lista lokalnych słoików do uwzględnienia na ścieżkach klasy sterownika i wykonawcy. W usłudze HDInsight ta lista składa się ze ścieżek do domyślnego systemu plików w usłudze Azure Storage lub Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pakiety MAVEN_COORDS | Oddzielona przecinkami lista współrzędnych maven słoików do uwzględnienia na ścieżkach klasy sterownika i wykonawcy. Przeszukuje lokalne repozytorium maven, a następnie maven central, a następnie wszelkie dodatkowe repozytoria zdalne określone za pomocą `--repositories`. Format współrzędnych to *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LISTA | Tylko w języku Python lista plików .zip, .egg lub .py rozdzielonych przecinkami jest umieszczana na pythonpath. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Następne kroki

- Zobacz [Wprowadzenie do platformy Apache Spark w usłudze Azure HDInsight, aby](apache-spark-overview.md) uzyskać omówienie.
- Zobacz [Tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md) do pracy z klastrami platformy Spark i SparkSQL.
- Zobacz [Co to jest Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) do pisania aplikacji, które przetwarzają dane strumieniowe za pomocą platformy Spark.
