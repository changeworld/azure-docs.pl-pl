---
title: Użyj powłoki interakcyjne Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Interakcyjne powłoki Spark udostępnia proces odczytu wykonania Drukuj polecenia Spark co w czasie i wyświetlać wyniki.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: maxluk
ms.openlocfilehash: d2b65980516a7ae1857711f2e58d9cd0a8e8ec9a
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="run-spark-from-the-spark-shell"></a>Uruchom Spark z poziomu powłoki Spark

Interakcyjne powłoki Spark udostępnia środowisko (odczyt wykonania Drukuj pętli) REPL dla polecenia Spark co w czasie i wyświetlać wyniki. Ten proces przydaje się do rozwoju i debugowanie. Platforma Spark zapewnia jeden powłoki dla każdej z obsługiwanych języków: Scala, Python i R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Uzyskanie dostępu do powłoki Spark przy użyciu protokołu SSH

Dostęp do powłoki Spark w usłudze HDInsight, łącząc się z podstawowym węzła głównego klastra przy użyciu protokołu SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Pełne polecenie SSH dla klastra można pobrać z portalu Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do okienka dla klastra Spark w usłudze HDInsight.
3. Wybierz Secure Shell (SSH).

    ![W okienku usługi HDInsight w portalu Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Skopiuj wyświetlone polecenie SSH i uruchom go w terminalu.

    ![Okienko HDInsight SSH w portalu Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Szczegółowe informacje dotyczące nawiązywania połączenia z usługi HDInsight przy użyciu protokołu SSH, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Uruchom powłokę Spark

Platforma Spark zapewnia powłok Scala (spark powłoka), Python (pyspark) i R (sparkR). W sesji SSH na węzła głównego klastra usługi HDInsight wprowadź jedno z następujących poleceń:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Teraz możesz wprowadzić Spark polecenia w odpowiednim języku.

## <a name="sparksession-and-sparkcontext-instances"></a>Wystąpienia SparkSession i SparkContext

Domyślnie podczas uruchamiania powłoki Spark wystąpień SparkSession i SparkContext są automatycznie tworzone automatycznie.

Aby uzyskać dostęp do wystąpienia SparkSession, wprowadź `spark`. Aby uzyskać dostęp do wystąpienia SparkContext, wprowadź `sc`.

## <a name="important-shell-parameters"></a>Parametry ważne powłoki

Polecenia powłoki Spark (`spark-shell`, `pyspark`, lub `sparkR`) obsługuje wiele parametrów wiersza polecenia. Aby zapoznać się z listą parametrów, uruchom powłoki Spark przy użyciu przełącznika `--help`. Należy pamiętać, że niektóre z tych parametrów mogą dotyczyć `spark-submit`, który opakowuje powłoki Spark.

| Przełącznik | description | Przykład |
| --- | --- | --- |
| --wzorca MASTER_URL | Określa głównego adresu URL. W usłudze HDInsight, ta wartość jest zawsze `yarn`. | `--master yarn`|
| --jars JAR_LIST | Rozdzielana przecinkami lista lokalnych słoików, aby uwzględnić w ścieżki klas sterownik i moduł wykonujący. W usłudze HDInsight składa się lista ścieżek do domyślnego systemu plików w magazynie Azure lub usługi Data Lake Store. | `--jars /path/to/examples.jar` |
| --pakietów MAVEN_COORDS | Rozdzielana przecinkami lista maven współrzędne słoików do uwzględnienia w ścieżki klas sterownik i moduł wykonujący. Wyszukuje maven lokalnego repozytorium, a następnie centralnego maven, a następnie określić żadnych dodatkowych repozytoria zdalne za pomocą `--repositories`. Format współrzędnych jest *groupId*:*artifactId*:*wersji*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| LISTY py — pliki | Dla języka Python, rozdzielana przecinkami lista plików zip, .egg lub .py do umieszczenia na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [wprowadzenie do platformy Spark w usłudze Azure HDInsight](apache-spark-overview.md) omówienie.
- Zobacz [utworzyć klaster Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md) do pracy z klastrami Spark oraz SparkSQL.
- Zobacz [co to jest strukturalnych przesyłania strumieniowego Spark?](apache-spark-streaming-overview.md) do pisania aplikacji, które przetwarzają dane przesyłane strumieniowo z platformy Spark.

