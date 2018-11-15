---
title: MapReduce i Pulpit zdalny przy użyciu technologii Apache Hadoop w HDInsight — Azure
description: Dowiedz się, jak nawiązać połączenie z platformą Apache Hadoop w HDInsight i uruchamianie zadań MapReduce za pomocą usług pulpitu zdalnego.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: b8cd389bee98058e5362a217268fb53d6aaf3424
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634485"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight-with-remote-desktop"></a>Korzystanie z technologii MapReduce na platformie Apache Hadoop w HDInsight przy użyciu pulpitu zdalnego
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

W tym artykule dowiesz się, jak nawiązać połączenie z technologii Apache Hadoop w klastrze HDInsight przy użyciu pulpitu zdalnego, a następnie uruchomienie zadania MapReduce za pomocą polecenia Hadoop.

> [!IMPORTANT]
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> HDInsight 3.4 lub nowszej, zobacz [używanie MapReduce z protokołu SSH](apache-hadoop-use-mapreduce-ssh.md) informacji na temat łączenia się z klastrem HDInsight i uruchamiania zadań MapReduce.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy:

* Klaster HDInsight dla komputerów z systemem Windows (platformy Hadoop w HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Połącz przy użyciu pulpitu zdalnego
Włączanie pulpitu zdalnego dla klastra HDInsight, a następnie nawiązać z nim, postępując zgodnie z instrukcjami na [nawiązywanie połączenia z klastrami HDInsight przy użyciu protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Użyj polecenia usługi Hadoop
Po połączeniu na pulpicie dla klastra HDInsight umożliwia uruchomienie zadania MapReduce, za pomocą polecenia Hadoop następujące czynności:

1. Na pulpicie HDInsight start **wiersza polecenia usługi Hadoop**. Spowoduje to otwarcie nowego wiersza polecenia w **c:\apps\dist\hadoop-&lt;numer wersji >** katalogu.

   > [!NOTE]
   > Numer wersji zmienia się po zaktualizowaniu usługi Hadoop. **HADOOP_HOME** zmiennej środowiskowej można znaleźć ścieżki. Na przykład `cd %HADOOP_HOME%` zmian katalogów do katalogu usługi Hadoop, nie wymagając od znać numer wersji.
   >
   >
2. Aby użyć **Hadoop** polecenie, aby uruchamiać zadanie MapReduce w przykładzie, użyj następującego polecenia:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Spowoduje to uruchomienie **wordcount** klasy, która jest zawarta w **hadoop-mapreduce-examples.jar** plik w bieżącym katalogu. Jako dane wejściowe, używa **wasb://example/data/gutenberg/davinci.txt** dokumentów i danych wyjściowych jest przechowywany w: **wasb: / / / przykład/data/WordCountOutput**.

   > [!NOTE]
   > Aby uzyskać więcej informacji dotyczących tego zadania MapReduce i przykładowe dane, zobacz <a href="hdinsight-use-mapreduce.md">używanie MapReduce w usłudze HDInsight Hadoop</a>.
   >
   >
3. Zadanie emituje szczegółowe informacje, jak jest przetwarzany, a jego zwraca informacje podobne do następującego po ukończeniu zadania:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Kiedy zadanie zostanie ukończone, użyj następującego polecenia, aby wyświetlić pliki wyjściowe przechowywane w **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    To powinien być wyświetlany dwa pliki **_SUCCESS** i **część r-00000**. **Część r-00000** plik zawiera dane wyjściowe dla tego zadania.

   > [!NOTE]
   > Niektóre zadania MapReduce może podzielić wyniki w wielu **część r-###** plików. Jeśli tak, użyj ### przyrostka, aby wskazać kolejność plików.
   >
   >
5. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Wyświetla listę słów, które są zawarte w **wasb://example/data/gutenberg/davinci.txt** plików wraz z liczbą wystąpień każdego wyrazu. Oto przykład danych, który będzie zawarty w pliku:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Podsumowanie
Jak widać, polecenia Hadoop udostępnia prosty sposób uruchamiania zadań MapReduce w klastrze usługi HDInsight, a następnie przejrzyj dane wyjściowe zadania.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat zadań MapReduce w HDInsight:

* [Korzystanie z technologii MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)
