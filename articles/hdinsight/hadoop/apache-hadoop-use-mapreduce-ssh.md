---
title: Połączenie MapReduce i SSH z usługą Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać protokołu SSH do uruchamiania zadań MapReduce przy użyciu Apache Hadoop w usłudze HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044504"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Korzystanie z MapReduce z usługą Apache Hadoop w usłudze HDInsight przy użyciu protokołu SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesyłać zadania MapReduce z połączenia Secure Shell (SSH) do usługi HDInsight.

> [!NOTE]
> Jeśli znasz już serwery Apache Hadoop oparte na systemie Linux, ale jesteś nowym elementem usługi HDInsight, zobacz [wskazówki dotyczące usługi HDInsight opartej](../hdinsight-hadoop-linux-information.md)na systemie Linux.

## <a id="prereq"></a>Wymagania wstępne

* Klaster usługi HDInsight oparty na systemie Linux (Hadoop w usłudze HDInsight)

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Nawiązywanie połączenia przy użyciu protokołu SSH

Połącz się z klastrem przy użyciu protokołu SSH. Na przykład następujące polecenie nawiązuje połączenie z klastrem o nazwie Moja **HDInsight** jako kontem **sshuser** :

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Jeśli używasz klucza certyfikatu do uwierzytelniania SSH**, może być konieczne określenie lokalizacji klucza prywatnego w systemie klienta, na przykład:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Jeśli używasz hasła do uwierzytelniania SSH**, musisz podać hasło po wyświetleniu monitu.

Aby uzyskać więcej informacji na temat używania protokołu SSH z usługą HDInsight, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Korzystanie z poleceń Hadoop

1. Po nawiązaniu połączenia z klastrem usługi HDInsight Użyj następującego polecenia, aby uruchomić zadanie MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    To polecenie uruchamia klasę `wordcount`, która jest zawarta w pliku `hadoop-mapreduce-examples.jar`. Używa `/example/data/gutenberg/davinci.txt` dokumentu jako danych wejściowych, a dane wyjściowe są przechowywane w `/example/data/WordCountOutput`.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat tego zadania MapReduce i przykładowych danych, zobacz [Korzystanie z MapReduce w Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).

2. Zadanie emituje szczegóły w miarę procesów i zwraca informacje podobne do następującego tekstu po zakończeniu zadania:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić listę plików wyjściowych:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    To polecenie wyświetla dwa pliki, `_SUCCESS` i `part-r-00000`. Plik `part-r-00000` zawiera dane wyjściowe dla tego zadania.

    > [!NOTE]  
    > Niektóre zadania MapReduce mogą dzielić wyniki w wielu plikach **części-r-#** # # #. Jeśli tak, użyj sufiksu # # # # #, aby wskazać kolejność plików.

4. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    To polecenie wyświetla listę wyrazów, które znajdują się w pliku **wasb://example/Data/Gutenberg/DaVinci.txt** oraz liczbę przypadków wystąpienia każdego wyrazu. Następujący tekst to przykład danych zawartych w pliku:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Podsumowanie

Jak widać, polecenia usługi Hadoop umożliwiają łatwe uruchamianie zadań MapReduce w klastrze usługi HDInsight, a następnie wyświetlanie danych wyjściowych zadania.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w usłudze HDInsight:

* [Korzystanie z MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
