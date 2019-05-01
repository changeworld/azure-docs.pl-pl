---
title: Uruchom przykłady Apache Hadoop MapReduce w HDInsight — Azure
description: Rozpocznij pracę w plikach jar zawarte w HDInsight przy użyciu przykładów technologii MapReduce. Łączenie z klastrem za pomocą protokołu SSH, a następnie uruchom przykładowe zadania za pomocą polecenia Hadoop.
keywords: jar przykład hadoop, hadoop przykłady jar, przykłady technologii mapreduce usługi hadoop, przykłady technologii mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: hrasheed
ms.openlocfilehash: f0251e3926c569b45ebebcd18b98df5af4564443
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706668"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Uruchamianie przykładów technologii MapReduce zawarte w HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Dowiedz się, jak i uruchamianie przykładów technologii MapReduce dołączonej usługi Apache Hadoop w HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster platformy Apache Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Przykłady technologii MapReduce

**Lokalizacja**: Przykłady znajdują się w klastrze HDInsight na `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Zawartość**: Poniższe przykłady są zawarte w tym archiwum:

* `aggregatewordcount`: Wartość zagregowana na podstawie programu mapreduce, który zlicza wyrazy w plików wejściowych.
* `aggregatewordhist`: Wartość zagregowana na podstawie programu mapreduce, który oblicza histogram słów w plików wejściowych.
* `bbp`: Program mapreduce, który używa Bailey — Borwein-Plouffe do obliczenia dokładną liczbę cyfr liczby Pi.
* `dbcount`: Zadanie przykład, który zlicza dzienników pageview przechowywanych w bazie danych.
* `distbbp`: Program mapreduce, który używa formuły BBP typu do obliczenia dokładna liczba bitów Pi.
* `grep`: Programu mapreduce zliczającego dopasowania wyrażenia regularnego, w danych wejściowych.
* `join`: Zadanie, które wykonuje sprzężenie sortowane, równomiernie podzielone na partycje zestawów danych.
* `multifilewc`: Zadanie, który zlicza wyrazów z kilku plików.
* `pentomino`: Mapreduce kafelka zniesienia program, aby znaleźć rozwiązania problemów pentomino.
* `pi`: Program mapreduce, który szacuje Pi przy użyciu quasi Monte Carlo metody.
* `randomtextwriter`: Program mapreduce, który zapisuje 10 GB danych tekstowych losowych na węzeł.
* `randomwriter`: Program mapreduce, który zapisuje 10 GB danych losowych na węzeł.
* `secondarysort`: Przykład definiowania dodatkowych sortowania do fazy Zmniejsz.
* `sort`: Program mapreduce sortuje zapisane przez moduł zapisujący losowe dane.
* `sudoku`: Sudoku solver.
* `teragen`: Generowanie danych dla terasort.
* `terasort`: Uruchom terasort.
* `teravalidate`: Sprawdzanie wyników terasort.
* `wordcount`: Programu mapreduce zliczającego słów w plików wejściowych.
* `wordmean`: Programu mapreduce zliczającego średnia długość słowa w plików wejściowych.
* `wordmedian`: Programu mapreduce zliczającego mediany długość słowa w plików wejściowych.
* `wordstandarddeviation`: Programu mapreduce zliczającego odchylenie standardowe długość słowa w plików wejściowych.

**Kod źródłowy**: Kod źródłowy dla tych przykładów znajduje się w klastrze HDInsight na `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Uruchomienie przykładu wordcount

1. Nawiązać HDInsight przy użyciu protokołu SSH. Zastąp `CLUSTER` nazwą klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Z `username@#######:~$` monit, użyj następującego polecenia, aby wyświetlić listę próbek:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    To polecenie generuje listę przykładowych opisanych w poprzedniej części tego dokumentu.

3. Użyj następującego polecenia, aby uzyskać pomoc dotyczącą określonego próbki. W tym przypadku **wordcount** próbki:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Pojawi się następujący komunikat:

        Usage: wordcount <in> [<in>...] <out>

    Ten komunikat oznacza, że może zapewnić kilka ścieżek wejściowych dla dokumentów źródłowych. Końcowego ścieżka jest, gdzie są przechowywane dane wyjściowe (liczba słów w dokumencie źródłowym).

4. Liczba wszystkie wyrazy w Notesy programu Leonardo da Vinci, które są dostarczane jako przykładowe dane z klastra, należy wykonać następujące kroki:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Dane wejściowe dla tego zadania są odczytywane z `/example/data/gutenberg/davinci.txt`. Dane wyjściowe przechowywane w tym przykładzie `/example/data/davinciwordcount`. Obie ścieżki znajdują się na domyślny magazyn dla klastra, a nie w lokalnym systemie plików.

   > [!NOTE]  
   > Jak wspomniano w pomocy, na przykład liczby słów, można również określić wiele plików wejściowych. Na przykład `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` będzie zliczania wyrazów w davinci.txt i ulysses.txt.

5. Po ukończeniu zadania, użyj następującego polecenia, aby wyświetlić dane wyjściowe:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    To polecenie łączy wszystkie pliki wyjściowe utworzone przez zadanie. Wyświetla dane wyjściowe do konsoli. Dane wyjściowe będą podobne do następującego tekstu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Każdy wiersz reprezentuje wyrazu i ile razy wystąpił w danych wejściowych.

## <a name="the-sudoku-example"></a>W przykładzie Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) jest układanki logiki, składają się z dziewięciu 3 x 3 siatki. Niektóre komórki na siatce mają numery, podczas gdy inne są puste, a celem jest zapewnienie rozwiązania dla pustych komórek. Poprzedni link zawiera więcej informacji na temat układanki, ale w tym przykładzie ma na celu rozwiązywanie dla pustych komórek. Dlatego nasze dane wejściowe powinny być pliku, który znajduje się w następującym formacie:

* Dziewięć wierszy dziewięć kolumn
* Każda kolumna może zawierać wartość liczbowa lub `?` (wskazuje, pusta komórka)
* Komórki są oddzielone spacją

Brak niektórych sposobem konstruowania zagadki Sudoku; Nie można powtórzyć liczbę w kolumnie lub wierszu. Znajduje się przykład w klastrze HDInsight, która została prawidłowo skonstruowana. Znajduje się on w `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` zawierający następujący tekst:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Aby uruchomić ten przykład problem za pośrednictwem przykład Sudoku, użyj następującego polecenia:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Wyniki wyglądać podobnie do następującego tekstu:

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

Próbki statystycznego pi (quasi Monte Carlo) metodę, aby oszacować wartość liczby pi. Punkty są umieszczane w losowo wybranym momencie w kwadrat jednostki. Kwadrat zawiera także okrąg. Prawdopodobieństwo, że punkty mieszczą się w okręgu są równe pole koła, pi/4. Wartość liczby pi można oszacować od wartości 4R. R to stosunek liczby punktów znajdujące się wewnątrz okręgu całkowitą liczbę punktów należących do kwadratu. Im większy próbka punkty używane jest lepiej oszacować.

Użyj następującego polecenia, aby uruchomić ten przykład. To polecenie używa 16 mapy z 10 000 000 przykłady można oszacować wartości liczby pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Wartość zwracana przez to polecenie jest podobne do **3.14159155000000000000**. W przypadku odwołania 10 pierwszych miejsc dziesiętnych pi są 3.1415926535.

## <a name="10-gb-graysort-example"></a>Przykład GraySort 10 GB

GraySort jest sortowanie testów porównawczych. Metryki jest wskaźnikiem sortowania (TB/minutę), które odbywa się podczas sortowania dużych ilości danych, zazwyczaj 100 TB minimalnej.

Ta próbka używa skromną 10 GB danych, dzięki czemu mogą być uruchamiane względnie szybko. Używa ona aplikacje MapReduce, opracowane przez Owen O'Malley oraz organizacji i Arun Murthy. Te aplikacje wygrał porównawczych roczne sortowania terabajt ogólnego przeznaczenia ("Daytona") w 2009 r. o częstotliwości 0.578 TB na minutę (100 TB w ciągu minut 173). Aby uzyskać więcej informacji na ten temat i inne wzorce sortowania, zobacz [testów porównawczych sortowania](https://sortbenchmark.org/) lokacji.

W tym przykładzie użyto trzy rodzaje programów MapReduce:

* **TeraGen**: Program MapReduce, który generuje wiersze danych, aby posortować

* **TeraSort**: Pobiera próbki danych wejściowych i używa MapReduce, aby posortować dane w kolejności całkowity

    TeraSort jest standardowa sortowania MapReduce, z wyjątkiem niestandardowego partycjonera. Partycjonera używa posortowanej listy kluczy podczas próbkowania n-1, które określają zakres kluczy dla każdego redukcji. W szczególności, wszystkie klucze takie, które [i-1] < = klucz < przykładowe [i] są wysyłane do i zmniejszyć. Ta partycjonera gwarantuje, że dane wyjściowe zmniejszyć i są dostępne w mniej niż dane wyjściowe zmniejszyć i + 1.

* **TeraValidate**: Program MapReduce, który sprawdza, czy dane wyjściowe są sortowane globalnie

    Tworzy jedną mapę dla pliku w katalogu wyjściowym i każdej mapy gwarantuje, że każdy klucz jest mniejsza niż poprzedniego. Funkcja mapy generuje rekordów klucze imię i nazwisko każdego pliku. Funkcja redukcji gwarantuje, czy pierwszy klucz w pliku jest większa niż ostatni klucz i-1 pliku. Wszelkie problemy są zgłaszane jako dane wyjściowe fazy Zmniejsz przy użyciu kluczy, które są poza kolejnością.

Należy użyć następującego kroki, aby wygenerować dane, sortowania, a następnie walidować dane wyjściowe:

1. Generowanie 10 GB danych, które są przechowywane do magazynu domyślnego klastra HDInsight na `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` Informuje o ile zadań mapy do użycia dla tego zadania usługi Hadoop. Końcowe dwa parametry poinstruować zadania do utworzenia 10 GB danych i zapisz go w `/example/data/10GB-sort-input`.

2. Aby posortować dane, użyj następującego polecenia:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` Informuje usługi Hadoop, ile zmniejszyć zadań dla zadania. Końcowe dwa parametry są po prostu lokalizacje danych wejściowych i wyjściowych.

3. Aby sprawdzić poprawność danych generowanych przez sortowania, skorzystaj z następujących:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób uruchamiania przykładów uwzględniony przy użyciu klastrów HDInsight opartych na systemie Linux. Samouczki dotyczące przy HDInsight Pig i Hive, MapReduce zobacz następujące tematy:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)