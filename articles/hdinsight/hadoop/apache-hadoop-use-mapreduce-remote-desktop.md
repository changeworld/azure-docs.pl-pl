---
title: MapReduce i zdalny pulpit z platformą Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą pulpitu zdalnego można nawiązać połączenia z platformą Hadoop w usłudze HDInsight i uruchamiania zadań MapReduce.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb59a510085a9f08e63f17cec1de2044905f914a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398729"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Korzystać z usługi MapReduce w Hadoop w usłudze HDInsight przy użyciu pulpitu zdalnego
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

W tym artykule dowiesz się, jak nawiązać połączenia z platformą Hadoop w klastrze usługi HDInsight przy użyciu pulpitu zdalnego, a następnie uruchomienie zadania MapReduce za pomocą polecenia Hadoop.

> [!IMPORTANT]
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight opartych na systemie Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> Dla usługi HDInsight w wersji 3.4 lub większą, zobacz [MapReduce Użyj przy użyciu protokołu SSH](apache-hadoop-use-mapreduce-ssh.md) informacji na temat nawiązywania połączenia z klastrem usługi HDInsight i uruchamiania zadań MapReduce.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, będą potrzebne następujące czynności:

* Klastra z systemem Windows HDInsight (Hadoop w usłudze HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Uzyskuj dostęp do usług pulpitu zdalnego
Włączenie pulpitu zdalnego dla klastra usługi HDInsight, a następnie nawiązać zgodnie z instrukcjami w [Connect do klastrów usługi HDInsight za pomocą protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Użyj polecenia Hadoop
Po nawiązaniu połączenia na pulpicie dla klastra usługi HDInsight, wykonaj następujące kroki, aby uruchomić zadanie MapReduce za pomocą polecenia Hadoop:

1. Z poziomu pulpitu HDInsight start **wiersza polecenia usługi Hadoop**. Spowoduje to otwarcie nowego wiersza polecenia w **c:\apps\dist\hadoop-&lt;numer wersji >** katalogu.

   > [!NOTE]
   > Numer wersji zmienia Hadoop jest aktualizowana. **HADOOP_HOME** zmiennej środowiskowej można znaleźć ścieżki. Na przykład `cd %HADOOP_HOME%` zmienia katalogi na katalog Hadoop, bez konieczności znajomości numer wersji.
   >
   >
2. Aby użyć **Hadoop** polecenie, aby uruchamiać zadanie MapReduce przykład, użyj następującego polecenia:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Spowoduje to uruchomienie **wordcount** klasy, która jest zawarta w **hadoop-mapreduce-examples.jar** plik w bieżącym katalogu. Jako dane wejściowe, używa **wasb://example/data/gutenberg/davinci.txt** dokumentu, a dane wyjściowe są przechowywane w: **wasb: / / / przykład/data/WordCountOutput**.

   > [!NOTE]
   > Aby uzyskać więcej informacji dotyczących tego zadania MapReduce i przykładowe dane, zobacz <a href="hdinsight-use-mapreduce.md">MapReduce użycia w usłudze HDInsight Hadoop</a>.
   >
   >
3. Zadanie emituje szczegółowe informacje, jak są przetwarzane i zwraca informacje podobne do następujących po zakończeniu zadania:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Po zakończeniu zadania, użyj następującego polecenia, aby wyświetlić listę plików wyjściowych przechowywane w **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    To powinien być wyświetlany dwa pliki **_SUCCESS** i **części r-00000**. **Części r-00000** plik zawiera dane wyjściowe dla tego zadania.

   > [!NOTE]
   > Niektóre zadania MapReduce mogą podzielone wyniki w wielu **części r-###** plików. Jeśli tak, użyj ### przyrostka, aby wskazać kolejność plików.
   >
   >
5. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Wyświetla listę słów, które są zawarte w **wasb://example/data/gutenberg/davinci.txt** pliku oraz liczbę razy podczas każdego wyrazu. Oto przykładowe dane, które zostaną uwzględnione w pliku:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Podsumowanie
Jak widać, polecenie Hadoop zapewnia prosty sposób uruchamiania zadań MapReduce na klaster usługi HDInsight, a następnie przejrzyj dane wyjściowe zadania.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat zadań MapReduce w usłudze HDInsight:

* [Korzystać z usługi MapReduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
