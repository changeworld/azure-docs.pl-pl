---
title: Połączenie MapReduce i SSH z Apache Hadoop — usługa Azure HDInsight
description: Dowiedz się, jak używać SSH do uruchamiania zadań MapReduce przy użyciu Apache Hadoop w programie HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934697"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Użyj MapReduce z Apache Hadoop na HDInsight z SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesłać zadania MapReduce z połączenia Secure Shell (SSH) do usługi HDInsight.

> [!NOTE]
> Jeśli jesteś już zaznajomiony z wykorzystaniem opartych na Linuksie serwerów Apache Hadoop, ale jesteś nowy w HDInsight, zobacz [wskazówki HDInsight oparte na linuksie](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Hadoop w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Używanie poleceń Hadoop

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia z klastrem HDInsight użyj następującego polecenia, aby rozpocząć zadanie MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    To polecenie uruchamia `wordcount` klasę, która znajduje `hadoop-mapreduce-examples.jar` się w pliku. Używa `/example/data/gutenberg/davinci.txt` dokumentu jako danych wejściowych, a `/example/data/WordCountOutput`dane wyjściowe są przechowywane w .

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat tego zadania MapReduce i przykładowych danych, zobacz [Użyj MapReduce w Apache Hadoop w programie HDInsight](hdinsight-use-mapreduce.md).

    Zadanie emituje szczegóły podczas przetwarzania i zwraca informacje podobne do następującego tekstu po zakończeniu zadania:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić listę plików wyjściowych:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    To polecenie wyświetla `_SUCCESS` dwa `part-r-00000`pliki i . Plik `part-r-00000` zawiera dane wyjściowe dla tego zadania.

    > [!NOTE]  
    > Niektóre zadania MapReduce mogą dzielić wyniki na wiele plików **części-r-####.** Jeśli tak, użyj sufiksu ######, aby wskazać kolejność plików.

1. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    To polecenie wyświetla listę wyrazów zawartych w pliku **wasbs://example/data/gutenberg/davinci.txt** oraz liczbę występów każdego wyrazu. Poniższy tekst jest przykładem danych zawartych w pliku:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Następne kroki

Jak widać, polecenia Hadoop zapewniają łatwy sposób uruchamiania zadań MapReduce w klastrze HDInsight, a następnie wyświetlania danych wyjściowych zadania. Aby uzyskać informacje o innych sposobach pracy z Hadoop w programie HDInsight:

* [Użyj MapReduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)
