---
title: MapReduce i ustawieniami SSH połączenia z usługą Apache Hadoop w HDInsight — Azure
description: Dowiedz się, jak uruchamiać zadania MapReduce, przy użyciu technologii Apache Hadoop w HDInsight za pomocą protokołu SSH.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlunb
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/10/2018
ms.date: 01/14/2019
ms.author: v-yiso
ms.openlocfilehash: 3448a5e89f6930a5bdcb7d0d77b92576e58fc90b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129386"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight przy użyciu protokołu SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesyłać zadania MapReduce z połączeń protokołu Secure Shell (SSH) do HDInsight.

> [!NOTE]
> Jeśli znasz już przy użyciu serwerów Apache Hadoop oparte na systemie Linux, ale dopiero zaczynasz korzystać z HDInsight, zobacz [porady HDInsight opartych na systemie Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Wymagania wstępne

* Klaster HDInsight opartych na systemie Linux (platformą Hadoop w HDInsight)

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Połącz przy użyciu protokołu SSH

Połącz się z klastrem przy użyciu protokołu SSH. Na przykład następujące polecenie umożliwia nawiązanie klastra o nazwie **myhdinsight** jako **sshuser** konta:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.cn
```

**Jeśli używasz klucza certyfikatu do uwierzytelniania SSH**, konieczne może być na przykład określ lokalizację klucza prywatnego w systemie klienta:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.cn
```

**Jeśli używasz hasła do uwierzytelniania SSH**, należy podać hasło po wyświetleniu monitu.

Aby uzyskać więcej informacji dotyczących korzystania z protokołu SSH z usługą HDInsight, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Używać poleceń platformy Hadoop

1. Po połączeniu się z klastrem HDInsight, użyj następującego polecenia, aby uruchomić zadanie MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    To polecenie uruchamia `wordcount` klasy, która jest zawarta w `hadoop-mapreduce-examples.jar` pliku. Używa ona `/example/data/gutenberg/davinci.txt` dokumentu jako dane wejściowe i wyjściowe są przechowywane w `/example/data/WordCountOutput`.

    > [!NOTE]
    > Aby uzyskać więcej informacji dotyczących tego zadania MapReduce i przykładowe dane, zobacz [używanie MapReduce na platformie Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md).

2. Zadanie emituje szczegółowe informacje, jak są przetwarzane i jego zwraca informacje podobne do następującego tekstu po zakończeniu zadania:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Po zakończeniu zadania, użyj następującego polecenia, aby wyświetlić listę plików danych wyjściowych:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    To polecenie wyświetla dwa pliki `_SUCCESS` i `part-r-00000`. `part-r-00000` Plik zawiera dane wyjściowe dla tego zadania.

    > [!NOTE]
    > Niektóre zadania MapReduce może podzielić wyniki w wielu **część r-###** plików. Jeśli tak, użyj ### przyrostka, aby wskazać kolejność plików.

4. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    To polecenie wyświetla listę słów, które są zawarte w **wasb://example/data/gutenberg/davinci.txt** pliku oraz liczbę wystąpień każdego wyrazu. Następujący tekst jest przykładem danych, który jest zawarty w pliku:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Podsumowanie

Jak widać, polecenia Hadoop zapewniają prosty sposób uruchamiania zadań MapReduce w klastrze usługi HDInsight, a następnie przejrzyj dane wyjściowe zadania.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w HDInsight:

* [Korzystanie z technologii MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
