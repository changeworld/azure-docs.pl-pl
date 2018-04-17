---
title: Uruchom przykłady MapReduce z Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy przy użyciu MapReduce próbek w pliki jar uwzględnione w usłudze HDInsight. Używanie protokołu SSH, aby połączyć się z klastrem, a następnie uruchom przykładowe zadania za pomocą polecenia Hadoop.
keywords: jar przykład hadoop, jar przykłady hadoop, przykłady mapreduce hadoop, przykłady mapreduce
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 71283f74242db8ea8811300e38ef0a6c22e9e541
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Uruchom przykłady MapReduce uwzględnione w usłudze HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Dowiedz się, jak uruchomić przykłady MapReduce dołączone do platformy Hadoop w usłudze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi HDInsight**: zobacz [rozpocząć korzystanie z usługi Hadoop przy użyciu Hive w usłudze HDInsight w systemie Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* **Klient SSH**: Aby uzyskać więcej informacji, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Przykłady MapReduce

**Lokalizacja**: przykłady znajdują się w klastrze usługi HDInsight w `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Zawartość**: następujące przykłady znajdują się w tym archiwum:

* `aggregatewordcount`: Agregacji na podstawie mapreduce program, który oblicza wyrażenie w plików wejściowych.
* `aggregatewordhist`: Agregacji na podstawie mapreduce program, który oblicza histogram słowa plików wejściowych.
* `bbp`: Mapreduce program, który używa Bailey-Borwein-Plouffe do wyliczenia dokładnej cyfr pi.
* `dbcount`: Zadanie przykład liczby dzienników widok strony przechowywanych w bazie danych.
* `distbbp`: Mapreduce program, który używa formuły BBP typu do obliczenia dokładne bitów pi.
* `grep`: Mapreduce program zlicza dopasowań wyrażeń regularnych w danych wejściowych.
* `join`: Zadanie, które wykonuje sprzężenie za pośrednictwem sortowane, jednakowo na partycje zestawów danych.
* `multifilewc`: Zadanie, które zlicza wyrazy z wielu plików.
* `pentomino`: Mapreduce kafelka zniesienia programu w celu rozwiązania problemów pentomino.
* `pi`: Mapreduce program, który szacuje Pi przy użyciu quasi Monte Carlo metody.
* `randomtextwriter`: Mapreduce program, który zapisuje 10 GB losowe danych tekstowych w każdym węźle.
* `randomwriter`: Mapreduce program, który zapisuje 10 GB losowe dane w każdym węźle.
* `secondarysort`: Przykładowy Definiowanie dodatkowej sortowania do fazy Zmniejsz.
* `sort`: Mapreduce program napisane przez autora losowe dane są sortowane.
* `sudoku`Solver sudoku.
* `teragen`: Generować dane o terasort.
* `terasort`: Uruchom terasort.
* `teravalidate`: Sprawdzanie wyników terasort.
* `wordcount`: Mapreduce program zlicza słowa plików wejściowych.
* `wordmean`: Mapreduce program, który oblicza średnią długość słowa plików wejściowych.
* `wordmedian`: Mapreduce program zlicza średniej długości słowa plików wejściowych.
* `wordstandarddeviation`: Mapreduce program, który oblicza odchylenie standardowe długości wyrazów w plików wejściowych.

**Kod źródłowy**: kod źródłowy dla tych przykładów znajduje się w klastrze usługi HDInsight w `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Uruchomienie przykładu wordcount

1. Połączenie do usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z `username@#######:~$` monit, użyj następującego polecenia, aby wyświetlić listę próbek:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    To polecenie generuje listę przykładowych opisanych w poprzedniej części tego dokumentu.

3. Użyj następującego polecenia, aby uzyskać pomoc dotyczącą określonego próbki. W takim przypadku **wordcount** próbki:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Pojawi się następujący komunikat:

        Usage: wordcount <in> [<in>...] <out>

    Ten komunikat oznacza, które można udostępniać wielu ścieżek dla dokumentów źródłowych. Ścieżka końcowego jest przechowywania danych wyjściowych (liczbę słów w dokumencie źródłowym).

4. Liczba wszystkich wyrazów w notesów programu Leonardo Da Vinci, które są dostarczane jako przykładowych danych z klastrem należy wykonać następujące kroki:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Dane wejściowe dla tego zadania zostanie odczytany z `/example/data/gutenberg/davinci.txt`. Dane wyjściowe w tym przykładzie jest przechowywane w `/example/data/davinciwordcount`. Obu ścieżek znajdują się na domyślny magazyn dla klastra, a nie w lokalnym systemie plików.

   > [!NOTE]
   > Jak wspomniano w pomocy dla przykładu wordcount, można również określić wielu plików wejściowych. Na przykład `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` zlicza słów w davinci.txt i ulysses.txt.

5. Po zakończeniu zadania, użyj następującego polecenia, aby wyświetlić dane wyjściowe:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    To polecenie łączy wszystkie pliki wyjściowe utworzonej przez zadanie. Wyświetla dane wyjściowe do konsoli. Dane wyjściowe będą podobne do następującego tekstu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Każdy wiersz zawiera słowo i jak często wystąpi w danych wejściowych.

## <a name="the-sudoku-example"></a>Przykład Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) jest układanki logiki, składa się z dziewięciu 3 x 3 siatki. Niektóre komórki w siatce mają numery, podczas gdy inne są puste, a celem jest do rozwiązania dla puste komórki. Więcej informacji na temat układanki ma poprzedniej konsolidacji, ale w tym przykładzie ma na celu rozwiązywanie dla puste komórki. Dlatego nasze dane wejściowe powinny być pliku, który znajduje się w następującym formacie:

* Dziewięć wierszy dziewięć kolumn
* Każda kolumna może zawierać wartość liczbowa lub `?` (co oznacza puste komórki)
* Komórki są rozdzielone spacjami

Brak niektórych sposób utworzenia łamigłówki Sudoku; Nie można powtórzyć liczbą kolumn lub wierszy. Jest to przykład w klastrze usługi HDInsight, która została prawidłowo skonstruowana. Znajduje się on w `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` i zawiera następujący tekst:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Aby uruchomić ten przykład problem za pomocą przykład Sudoku, użyj następującego polecenia:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Zostaną wyświetlone wyniki podobne do następującego tekstu:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Przykład pi (π)

Przykładowe pi używa statystycznego (quasi Monte Carlo) metodę, aby oszacować wartość liczby pi. Punkty są umieszczane w losowo wybranym w kwadratowe jednostki. Pole zawiera także okręgu. Prawdopodobieństwo, że punkty objęte okręgu są równe obszaru okręgu, pi/4. Wartość liczby pi można oszacować od wartości 4R. R jest stosunkiem liczby punktów, które znajdują się wewnątrz okręgu całkowitą liczbę punktów, które znajdują się w kwadrat. Im większa próbka punkty używane jest lepsze szacowania.

Użyj następującego polecenia, aby uruchomić ten przykład. To polecenie używa 16 mapy z 10 000 000 próbek do oszacowania wartość liczby pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Wartość zwrócona przez to polecenie jest podobny do **3.14159155000000000000**. Odwołania są 3.1415926535 10 pierwszych miejsc dziesiętnych pi.

## <a name="10-gb-greysort-example"></a>Przykład Greysort 10 GB

GraySort jest sortowania testu wydajności. Metryka jest wskaźnikiem sortowania (TB/minutę), które odbywa się podczas sortowania dużych ilości danych, zwykle 100 TB minimalnej.

W przykładzie użyto niewielkie 10 GB danych, aby można było uruchomić stosunkowo szybko. Używa aplikacji MapReduce opracowane przez Owen O'Malley oraz organizacji i Arun Murthy. Te aplikacje kupione roczne testu porównawczego sortowania terabajt ogólnego przeznaczenia ("daytona") w 2009 ze stawką 0.578 TB na minutę (100 TB 173 minut). Aby uzyskać więcej informacji na ten temat oraz innych sortowania testów porównawczych, zobacz [Sortbenchmark](http://sortbenchmark.org/) lokacji.

W tym przykładzie używa trzech zestawów MapReduce programów:

* **TeraGen**: A MapReduce program, który generuje wiersze danych do sortowania

* **TeraSort**: próbek danych wejściowych i używa MapReduce, aby posortować dane w kolejności całkowita

    TeraSort jest standardowe sortowania MapReduce, z wyjątkiem partycjonera niestandardowych. Obiekt partitioner używa posortowaną listę kluczy N-1 próbkowany, definiujące klucza zakresu dla każdego Zmniejsz. W szczególności, wszystkie klucze takie przykładowe [i-1] < klucz = < próbki [i] są wysyłane do i zmniejszyć. Ten moduł partycjonowania gwarantuje, że dane wyjściowe zmniejszyć i wyświetlane są wszystkie mniejszej niż dane wyjściowe zmniejszyć i + 1.

* **TeraValidate**: A MapReduce program, który sprawdza, czy dane wyjściowe są sortowane globalny

    Tworzy jeden mapy dla każdego pliku w katalogu wyjściowego, a każda mapa zapewnia, że każdy klucz jest mniejsza niż lub równa poprzedniej. Funkcja mapy generuje rekordy kluczy imię i nazwisko każdego pliku. Funkcja Zmniejsz zapewnia, że pierwszy klucz pliku i jest większy niż ostatni klucz i-1 pliku. Wszelkie problemy będą raportowane jako dane wyjściowe fazy Zmniejsz z kluczami, które są poza kolejnością.

Użyj następujące kroki, aby wygenerować danych, sortowania, a następnie zweryfikować dane wyjściowe:

1. Generowanie 10 GB danych, który jest przechowywany w magazynie domyślnego klastra usługi HDInsight przy `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` Informuje Hadoop ile zadań mapy do użycia dla tego zadania. Końcowe dwa parametry poinstruować zadania, aby utworzyć 10 GB danych i zapisz go w `/example/data/10GB-sort-input`.

2. Aby posortować dane, użyj następującego polecenia:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` Informuje Hadoop, Zmniejsz liczbę zadań dla zadania. Końcowe dwa parametry są tylko lokalizacje danych wejściowych i wyjściowych.

3. Aby sprawdzić poprawność danych wygenerowanych przez sortowanie, skorzystaj z następujących:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób uruchamiania przykładów dołączone do klastrów usługi HDInsight opartej na systemie Linux. Samouczki dotyczące przy użyciu Pig, Hive i MapReduce z usługą HDInsight zobacz następujące tematy:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

