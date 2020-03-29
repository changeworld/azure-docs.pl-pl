---
title: Uruchom apache Hadoop MapReduce przykłady na HDInsight - Azure
description: Rozpocznij korzystanie z mapReduce próbek w plikach jar zawarte w HDInsight. Użyj SSH, aby połączyć się z klastrem, a następnie użyj polecenia Hadoop do uruchamiania przykładowych zadań.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435750"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Uruchamianie przykładów mapReduce zawartych w hdinsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Dowiedz się, jak uruchomić przykłady MapReduce zawarte w Apache Hadoop w programie HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Przykłady MapReduce

Próbki znajdują się w klastrze HDInsight w punkcie `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`. Kod źródłowy dla tych przykładów znajduje się `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`w klastrze HDInsight w programie .

Następujące przykłady są zawarte w tym archiwum:

|Sample |Opis |
|---|---|
|suma słów|Zlicza wyrazy w plikach wejściowych.|
|aggregatewordhist|Oblicza histogram słów w plikach wejściowych.|
|Bbp|Używa Bailey-Borwein-Plouffe do obliczania dokładnych cyfr Pi.|
|dbcount (liczba dbcount)|Zlicza dzienniki odsłon przechowywane w bazie danych.|
|distbbp ( distbbp )|Używa formuły typu BBP do obliczania dokładnych bitów Pi.|
|grep|Zlicza dopasowania wyrażenia regularnego w danych wejściowych.|
|join|Wykonuje sprzężenie za posortowane, równo podzielone na partycje zestawy danych.|
|multifilewc|Liczy słowa z kilku plików.|
|pentomino|Program układania płytek w celu znalezienia rozwiązań problemów pentomino.|
|Pi|Szacuje Pi metodą quasi-Monte Carlo.|
|randomtextwriter|Zapisuje 10 GB losowych danych tekstowych na węzeł.|
|randomwriter|Zapisuje 10 GB losowych danych na węzeł.|
|wtórnyort|Definiuje sortowanie pomocnicze do fazy redukcji.|
|sort|Sortuje dane napisane przez moduł zapisujący losowo.|
|Sudoku|Solver sudoku.|
|teragen|Generowanie danych dla terasort.|
|terasort ( terasort )|Uruchom terasort.|
|terawalidate|Sprawdzanie wyników terasort.|
|Wordcount|Zlicza wyrazy w plikach wejściowych.|
|słowomean|Zlicza średnią długość wyrazów w plikach wejściowych.|
|wordmedian (wordmedian)|Zlicza średnią długość wyrazów w plikach wejściowych.|
|wordstandarddeviation|Zlicza odchylenie standardowe długości wyrazów w plikach wejściowych.|

## <a name="run-the-wordcount-example"></a>Uruchamianie przykładu wordcount

1. Połącz się z HDInsight za pomocą SSH. Zamień `CLUSTER` nazwę klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. W sesji SSH użyj następującego polecenia, aby wyświetlić listę próbek:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    To polecenie generuje listę przykładów z poprzedniej sekcji tego dokumentu.

3. Użyj następującego polecenia, aby uzyskać pomoc dotyczącą określonego przykładu. W takim przypadku **przykładowy licznik słów:**

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zostanie wyświetlony następujący komunikat:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Ten komunikat wskazuje, że można podać kilka ścieżek wejściowych dla dokumentów źródłowych. Końcowa ścieżka jest, gdzie dane wyjściowe (liczba słów w dokumentach źródłowych) jest przechowywany.

4. Poniższe elementy służą do zliczania wszystkich słów w notesach Leonarda da Vinci, które są dostarczane jako przykładowe dane w klastrze:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Dane wejściowe dla `/example/data/gutenberg/davinci.txt`tego zadania jest odczytywany z . Dane wyjściowe w `/example/data/davinciwordcount`tym przykładzie są przechowywane w pliku . Obie ścieżki znajdują się w domyślnym magazynie klastra, a nie w lokalnym systemie plików.

   > [!NOTE]  
   > Jak wspomniano w pomocy dla wordcount próbki, można również określić wiele plików wejściowych. Na przykład `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` można policzyć wyrazy zarówno w davinci.txt i ulysses.txt.

5. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić dane wyjściowe:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    To polecenie łączy wszystkie pliki wyjściowe produkowane przez zadanie. Wyświetla dane wyjściowe do konsoli. Dane wyjściowe będą podobne do następującego tekstu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Każdy wiersz reprezentuje wyraz i ile razy wystąpił w danych wejściowych.

## <a name="the-sudoku-example"></a>Przykład Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) to logiczna układanka składająca się z dziewięciu siatek 3x3. Niektóre komórki w siatce mają liczby, podczas gdy inne są puste, a celem jest rozwiązanie pustych komórek. Poprzedni link zawiera więcej informacji na temat układanki, ale celem tego przykładu jest rozwiązanie dla pustych komórek. Więc nasze dane wejściowe powinny być plikiem, który jest w następującym formacie:

* Dziewięć wierszy po dziewięć kolumn
* Każda kolumna może zawierać `?` liczbę lub (co wskazuje pustą komórkę)
* Komórki są oddzielone spacją

Istnieje pewien sposób konstruowania zagadek Sudoku; nie można powtórzyć liczby w kolumnie lub wierszu. Istnieje przykład w klastrze HDInsight, który jest prawidłowo skonstruowany. Znajduje się `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` w i zawiera następujący tekst:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Aby uruchomić ten przykładowy problem w przykładzie Sudoku, użyj następującego polecenia:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Wyniki są podobne do następującego tekstu:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Przykład Pi (π)

Próbka pi wykorzystuje metodę statystyczną (quasi-Monte Carlo) do oszacowania wartości pi. Punkty są umieszczane losowo w kwadracie jednostki. Kwadrat zawiera również okrąg. Prawdopodobieństwo, że punkty mieszczą się w okręgu, jest równe obszarowi okręgu, pi/4. Wartość pi można oszacować na podstawie wartości 4R. R jest stosunkiem liczby punktów znajdujących się wewnątrz okręgu do całkowitej liczby punktów znajdujących się w obrębie kwadratu. Im większa próbka użytych punktów, tym lepsze jest oszacowanie.

Aby uruchomić ten przykład, użyj następującego polecenia. To polecenie używa 16 map z 10,000,000 próbek każdy do oszacowania wartości pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Wartość zwracana przez to polecenie jest podobna do **wartości 3.1415915500000000000000000000000.** W przypadku odwołań pierwsze 10 miejsc po przecinku pi to 3.1415926535.

## <a name="10-gb-graysort-example"></a>Przykład GraySort 10 GB

GraySort jest sortem odniesienia. Metryka jest szybkość sortowania (TB/minute), który jest osiągany podczas sortowania dużych ilości danych, zwykle minimum 100 TB.

W tym przykładzie użyto skromnych 10 GB danych, dzięki czemu można je uruchomić stosunkowo szybko. Wykorzystuje aplikacje MapReduce opracowane przez Owena O'Malleya i Aruna Murthy'ego. W 2009 r. wnioski te uzyskały roczny wskaźnik sortowania terabajtów ogólnego przeznaczenia ("Daytona"), z szybkością 0,578 TB/min (100 TB w 173 minuty). Aby uzyskać więcej informacji na temat tego i innych testów porównawczych sortowania, zobacz witrynę [Sort benchmark.](https://sortbenchmark.org/)

W tym przykładzie użyto trzech zestawów programów MapReduce:

* **TeraGen**: Program MapReduce, który generuje wiersze danych do sortowania

* **TeraSort**: Próbki danych wejściowych i używa MapReduce do sortowania danych w całkowitej kolejności

    TeraSort jest standardowym sortem MapReduce, z wyjątkiem niestandardowego partycjonowania. Partycjonujący używa posortowane listy N-1 próbkowane klucze, które definiują zakres kluczy dla każdego zmniejszenia. W szczególności wszystkie klucze takie jak, że próbka[i-1] <= klucz < próbki[i] są wysyłane w celu zmniejszenia i. Ten partycjoner gwarantuje, że wyjścia reduce i są mniejsze niż wyjście reduce i + 1.

* **TeraValidate:** Program MapReduce, który sprawdza, czy dane wyjściowe są posortowane globalnie

    Tworzy jedną mapę na plik w katalogu wyjściowym, a każda mapa zapewnia, że każdy klucz jest mniejszy lub równy poprzedniemu. Funkcja mapy generuje rekordy pierwszego i ostatniego klucza każdego pliku. Funkcja reduce zapewnia, że pierwszy klucz pliku i jest większy niż ostatni klucz pliku i-1. Wszelkie problemy są zgłaszane jako dane wyjściowe fazy redukcji, z kluczami, które są nie w porządku.

Aby wygenerować dane, posortować, a następnie sprawdzić poprawność danych wyjściowych, należy wykonać następujące czynności:

1. Wygeneruj 10 GB danych, które są przechowywane w `/example/data/10GB-sort-input`domyślnym magazynie klastra HDInsight pod adresem:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    Informuje `-Dmapred.map.tasks` Hadoop, ile zadań mapy do wykorzystania dla tego zadania. Ostatnie dwa parametry poinstruują zadanie, aby utworzyć 10 GB danych i zapisać je w . `/example/data/10GB-sort-input`

2. Użyj następującego polecenia, aby posortować dane:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Informuje `-Dmapred.reduce.tasks` Hadoop, ile zmniejszyć zadania do wykorzystania w zadaniu. Ostatnie dwa parametry to tylko lokalizacje wejściowe i wyjściowe danych.

3. Aby sprawdzić poprawność danych generowanych przez sortowanie, należy użyć następujących właściwości:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Następne kroki

Z tego artykułu dowiesz się, jak uruchomić przykłady dołączone do klastrów HDInsight opartych na systemie Linux. Aby uzyskać samouczki dotyczące korzystania z pig, hive i MapReduce z HDInsight, zobacz następujące tematy:

* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)
* [Użyj MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)