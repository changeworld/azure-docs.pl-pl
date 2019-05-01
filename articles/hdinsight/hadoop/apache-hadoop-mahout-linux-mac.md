---
title: Generowanie rekomendacji za pomocą Apache Mahout i HDInsight (SSH) - Azure
description: Dowiedz się, jak za pomocą uczenia biblioteki maszynowego Apache Mahout Generowanie rekomendacji filmów za pomocą HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709086"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Generowanie rekomendacji filmów za pomocą Apache Mahout opartych na systemie Linux usługi Apache Hadoop w HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak używać [Apache Mahout](https://mahout.apache.org) Biblioteka uczenia maszynowego przy użyciu usługi Azure HDInsight do Generowanie rekomendacji filmów.

Mahout to [uczenia maszynowego](https://en.wikipedia.org/wiki/Machine_learning) biblioteki dla usługi Apache Hadoop. Mahout zawiera algorytmy przetwarzania danych, takich jak filtrowanie, klasyfikacja i klastrowanie. W tym artykule umożliwia to aparat rekomendacji Generowanie rekomendacji filmów, oparte na filmów, które zauważono swoich znajomych.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster platformy Apache Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout przechowywanie wersji

Aby uzyskać więcej informacji o wersji biblioteki Mahout w HDInsight, zobacz [HDInsight wersje i składniki platformy Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Omówienie zaleceń

Funkcji, które są dostarczane przez Mahout on aparat rekomendacji. Ten aparat akceptuje dane w formacie `userID`, `itemId`, i `prefValue` (preferencji elementu). Mahout można wykonać analizy współpracujących wystąpień w celu oznaczenia: *użytkowników, którzy mają preferencji elementu również mieć preferencji dla tych innych elementów*. Mahout określa użytkowników z preferencjami podobnych elementów, które mogą być używane wydaje zalecenia.

Poniższy przepływ pracy jest uproszczony przykład, która korzysta z danych filmu:

* **wystąpienie wspólnej**: Jan, Alice i Bob wszystkie zbędne *gwiezdnych*, *ponownie ataki Empire*, i *Return Jedi*. Mahout Określa, że użytkownicy, którzy także, takich jak jeden z tych filmów, takich jak pozostałe dwa.

* **wystąpienie wspólnej**: Bob i Alicja również zbędne *zagrożenie fantom*, *ataku klony*, i *zemsty Sith*. Mahout Określa, że użytkownicy, którzy również zbędne poprzednie trzy filmy, takich jak te trzy filmów.

* **Zalecenie podobieństwa**: Ponieważ Jan zbędne pierwszych trzech filmy, Mahout analizuje filmy tej osoby z preferencjami podobnych zbędne, ale Jan nie ma obserwowane (zbędne/klasyfikowane). W tym przypadku zaleca się Mahout *zagrożenie fantom*, *ataku klony*, i *zemsty Sith*.

### <a name="understanding-the-data"></a>Opis danych

Wygodne [badań GroupLens](https://grouplens.org/datasets/movielens/) dostarcza ocenę danych dotyczących filmów w formacie, który jest zgodny z biblioteki Mahout. Dane te są dostępne do magazynu domyślnego usługi klastra w `/HdiSamples/HdiSamples/MahoutMovieData`.

Istnieją dwa pliki `moviedb.txt` i `user-ratings.txt`. `user-ratings.txt` Plik jest używany podczas analizy. `moviedb.txt` Służy do zapewnienia informacji tekstowych przyjazny dla użytkownika podczas przeglądania wyników.

Dane zawarte w ratings.txt użytkownika ma strukturę `userID`, `movieID`, `userRating`, i `timestamp`, która wskazuje, jak wysoko ocenianych filmu przez każdego użytkownika. Oto przykład danych:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Wykonanie analizy

Z połączenia SSH z klastrem Użyj następującego polecenia, aby uruchomić zadanie zalecenia:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Zadanie może potrwać kilka minut, a może uruchomić wiele zadań MapReduce.

## <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

1. Po ukończeniu zadania, użyj następującego polecenia, aby przejrzeć wygenerowane dane wyjściowe:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Dane wyjściowe wyglądają następująco:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Pierwsza kolumna jest `userID`. Wartości zawarte w "[" i "]" są `movieId`:`recommendationScore`.

2. Można użyć danych wyjściowych, wraz z moviedb.txt, zawiera więcej informacje na temat zalecenia. Najpierw należy skopiować pliki lokalnie przy użyciu następujących poleceń:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    To polecenie kopiuje dane wyjściowe do pliku o nazwie **recommendations.txt** w bieżącym katalogu, wraz z plikami danych filmu.

3. Użyj następującego polecenia, aby utworzyć skrypt w języku Python, który wyszukuje nazwy filmu dla danych w danych wyjściowych zalecenia:

    ```bash
    nano show_recommendations.py
    ```

    Po otwarciu edytora, skorzystaj z poniższego tekstu jako zawartość pliku:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Naciśnij klawisz **Ctrl-X**, **Y**, a na koniec **Enter** do zapisania danych.

4. Uruchom skrypt języka Python. Następujące polecenie przyjęto założenie, że znajdują się w katalogu, w którym wszystkie pliki zostały pobrane:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    To polecenie analizuje zalecenia wygenerowany dla użytkownika Identyfikatorem 4.

   * **Ratings.txt użytkownika** plik jest używany do pobierania filmów, które zostały sklasyfikowane.

   * **Moviedb.txt** plik jest używany do pobierania nazwy filmów.

   * **Recommendations.txt** służy do pobierania rekomendacji filmów dla tego użytkownika.

     Dane wyjściowe tego polecenia będą podobne do następującego tekstu:

       Siedem lat Tibet (1997), wynik = 5.0 Indiana Jones i ostatniego Crusade (1989), wynik = 5.0 Jaws (1975), wynik = 5.0 znaczeniu i świadomości (1995), wynik = 5.0 niezależność od dnia (ID4) (1996), wynik = 5.0 Moje najlepszy przyjaciel ślubu (1997), wynik = 5.0 Jerry Maguire (1996) wynik = 5.0 Scream 2 (1997), wynik = 5.0 czas ataku typu Kill, (1996), wynik = 5.0

## <a name="delete-temporary-data"></a>Usuń dane tymczasowe

Zadania biblioteki mahout nie usuwaj dane tymczasowe, który jest tworzony podczas przetwarzania zadania. `--tempDir` Parametr jest określony w zadaniu przykładu do określonej ścieżki do łatwego usunięcia plików tymczasowych. Aby usunąć plików tymczasowych, użyj następującego polecenia:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Ponownie uruchom polecenie, należy także usunąć katalogu wyjściowego. Usuń ten katalog, należy wykonać następujące kroki:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać biblioteki Mahout poznać inne sposoby pracy z danymi w HDInsight:

* [Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Apache Pig z HDInsight](hdinsight-use-pig.md)
* [MapReduce z HDInsight](hdinsight-use-mapreduce.md)