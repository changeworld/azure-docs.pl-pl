---
title: Używanie interaktywnej powłoki Spark w usłudze Azure HDInsight
description: Interaktywna powłoka Spark udostępnia proces odczytu-Execute-Print służący do uruchamiania poleceń platformy Spark po jednym naraz i wyświetlania wyników.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441889"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Uruchamianie Apache Spark z powłoki Spark

Interaktywna powłoka [Apache Spark](https://spark.apache.org/) udostępnia środowisko REPL (Read-Execute-Print) do uruchamiania poleceń platformy Spark po jednym naraz i oglądania wyników. Ten proces jest przydatny do programowania i debugowania. Platforma Spark udostępnia jedną powłokę dla każdego z obsługiwanych języków: Scala, Python i R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Uzyskiwanie do powłoki Apache Spark przy użyciu protokołu SSH

Uzyskaj dostęp do powłoki Apache Spark w usłudze HDInsight, łącząc się z podstawowym węzłem głównym klastra przy użyciu protokołu SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Możesz uzyskać kompletne polecenie SSH dla klastra z Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do okienka dla klastra usługi HDInsight Spark.
3. Wybierz pozycję Secure Shell (SSH).

    ![Okienko usługi HDInsight w Azure Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Skopiuj wyświetlone polecenie SSH i uruchom je w terminalu.

    ![Okienko usługi HDInsight SSH w Azure Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Aby uzyskać szczegółowe informacje na temat używania protokołu SSH do łączenia się z usługą HDInsight, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Uruchamianie powłoki Apache Spark

Platforma Spark udostępnia powłoki dla Scala (Spark-Shell), Python (pyspark) i R (Spark). W sesji SSH w węźle głównym klastra usługi HDInsight wprowadź jedno z następujących poleceń:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Teraz można wprowadzać polecenia platformy Spark w odpowiednim języku.

## <a name="sparksession-and-sparkcontext-instances"></a>Wystąpienia SparkSession i SparkContext

Domyślnie po uruchomieniu powłoki Spark wystąpienia SparkSession i SparkContext są automatycznie tworzone dla Ciebie.

Aby uzyskać dostęp do wystąpienia SparkSession, `spark`wprowadź. Aby uzyskać dostęp do wystąpienia SparkContext, `sc`wprowadź.

## <a name="important-shell-parameters"></a>Ważne parametry powłoki

Polecenie powłoki Spark (`spark-shell`, `pyspark`lub `sparkR`) obsługuje wiele parametrów wiersza polecenia. Aby wyświetlić pełną listę parametrów, Uruchom powłokę Spark za pomocą przełącznika `--help`. Należy zauważyć, że niektóre z tych parametrów mogą być `spark-submit`stosowane tylko do, które są zawijane przez powłokę Spark.

| — przełącznik | description | Przykład |
| --- | --- | --- |
| --Master MASTER_URL | Określa główny adres URL. W usłudze HDInsight ta wartość jest zawsze `yarn`. | `--master yarn`|
| --jars JAR_LIST | Rozdzielana przecinkami lista Jars lokalnego do uwzględnienia w sterownikach i ścieżkach klas wykonywania. W usłudze HDInsight ta lista zawiera ścieżki do domyślnego systemu plików w usłudze Azure Storage lub Data Lake Storage. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS pakietów | Rozdzielana przecinkami lista współrzędnych Maven Jars do uwzględnienia w sterownikach i ścieżkach klas wykonywania. Przeszukuje lokalne repozytorium Maven, a następnie Maven Central, wszelkie dodatkowe repozytoria zdalne określone za `--repositories`pomocą. Format współrzędnych to *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --Lista z LISTą plików | Tylko w przypadku języka Python, rozdzielana przecinkami lista plików zip, jaja lub. PR do umieszczenia w PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [wprowadzenie do Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md) .
- Zobacz [Tworzenie klastra Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md) , aby współpracował z klastrami Spark i SparkSQL.
- Zobacz, [co to jest Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) , aby napisać aplikacje, które przetwarzają dane przesyłane strumieniowo za pomocą platformy Spark.
