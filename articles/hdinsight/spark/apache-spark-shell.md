---
title: Używanie powłoki interaktywne platformy Spark w usłudze Azure HDInsight
description: Interakcyjnej powłoki Spark udostępnia proces odczytu wykonania — Drukuj uruchamianie poleceń jeden platformy Spark w danym momencie i sprawdzamy rezultaty.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116077"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Uruchamianie platformy Apache Spark z poziomu powłoki Spark

Interakcyjna [platformy Apache Spark](https://spark.apache.org/) powłoki zapewnia środowisko REPL (odczytu wykonania — Drukuj pętli) uruchamianie poleceń jeden platformy Spark w danym momencie i sprawdzamy rezultaty. Ten proces jest przydatne w przypadku programowania i debugowania. Platforma Spark udostępnia jeden powłoki dla każdego z jego obsługiwane języki: Scala, Python i R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Uzyskać dostęp do powłoki usługi Apache Spark przy użyciu protokołu SSH

Dostęp do powłoki usługi Spark Apache na HDInsight, łącząc się z podstawowym węzłem głównym klastra za pomocą protokołu SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Pełne polecenie SSH dla klastra można uzyskać w witrynie Azure portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do okienka klastra HDInsight Spark.
3. Wybierz Secure Shell (SSH).

    ![W okienku HDInsight w witrynie Azure portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Skopiuj wyświetlone polecenie SSH i uruchom w terminalu.

    ![Okienko HDInsight SSH w witrynie Azure portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Aby uzyskać szczegółowe informacje o korzystaniu protokołu SSH, aby nawiązać połączenie HDInsight, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Uruchamianie powłoki usługi Apache Spark

Platforma Spark zapewnia powłoki (powłoki aparatu spark) w języku Scala, Python (pyspark) i R (sparkR). W sesji SSH pod węzłem głównym klastra usługi HDInsight wpisz jedno z następujących poleceń:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Teraz możesz wprowadzić poleceń platformy Spark w odpowiednim języku.

## <a name="sparksession-and-sparkcontext-instances"></a>Wystąpienia SparkSession i odporne rozproszone zestawy

Domyślnie podczas uruchamiania powłoki Spark wystąpień SparkSession i odporne rozproszone zestawy są automatycznie tworzone dla Ciebie.

Aby uzyskać dostęp do wystąpienia SparkSession, wprowadź `spark`. Aby uzyskać dostęp do wystąpienia odporne rozproszone zestawy, wprowadź `sc`.

## <a name="important-shell-parameters"></a>Parametry ważne powłoki

Polecenia powłoki usługi Spark (`spark-shell`, `pyspark`, lub `sparkR`) obsługuje wiele parametrów wiersza polecenia. Aby wyświetlić pełną listę parametrów, należy uruchomić powłoki Spark z przełącznikiem `--help`. Należy pamiętać, że niektóre z tych parametrów może dotyczą tylko `spark-submit`, który otacza powłoki Spark.

| — przełącznik | description | Przykład |
| --- | --- | --- |
| --opanować MASTER_URL | Określa główny adres URL. W HDInsight, ta wartość jest zawsze `yarn`. | `--master yarn`|
| --jars JAR_LIST | Rozdzielana przecinkami lista lokalnych plikach JAR do uwzględnienia na ścieżkami sterownik i przetwarzania. W HDInsight składa się lista ścieżek do domyślnego systemu plików w usłudze Azure Storage lub magazynu usługi Data Lake. | `--jars /path/to/examples.jar` |
| --pakietów MAVEN_COORDS | Rozdzielana przecinkami lista współrzędne maven plikach JAR do uwzględnienia na ścieżkami sterownik i przetwarzania. Wyszukuje maven w lokalnym repozytorium, a następnie maven central, a następnie wszelkie dodatkowe repozytoria zdalne określony za pomocą `--repositories`. Format współrzędnych *groupId*:*artifactId*:*wersji*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| LISTY py — pliki | Dla języka Python tylko pliki zip, .egg lub PY do umieszczenia na PYTHONPATH listę rozdzielonych przecinkami. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [wprowadzenie do platformy Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md) omówienie.
- Zobacz [Tworzenie klastra Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md) do pracy z klastrami Spark oraz SparkSQL.
- Zobacz [co to jest Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) do pisania aplikacji, które przetwarzają dane przesyłane strumieniowo z platformą Spark.
