---
title: Generowanie zaleceń przy użyciu Mahout i HDInsight (SSH) - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki uczenia maszynowego Apache Mahout do generowania zaleceń filmu z usługą HDInsight (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: 357df033c4d3fd0da84ea595d68802546aee3e9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Generowanie zaleceń filmu przy użyciu Apache Mahout z opartą na systemie Linux platformą Hadoop w HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak używać [Apache Mahout](http://mahout.apache.org) maszyny biblioteki learning z usługą Azure HDInsight w celu generowania zaleceń filmu.

Mahout jest [uczenia maszynowego] [ ml] biblioteki dla platformy Apache Hadoop. Mahout zawiera algorytmy przetwarzania danych, takich jak filtrowanie, klasyfikacji i klastrowania. W tym artykule aparat zalecenie służy do generowania zaleceń filmu, oparte na filmów, które miały Twoich znajomych.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight opartej na systemie Linux. Informacje o tworzeniu jedną, zobacz [Rozpoczynanie pracy z opartą na systemie Linux platformą Hadoop w usłudze HDInsight][getstarted].

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient SSH. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Przechowywanie wersji mahout

Aby uzyskać więcej informacji na temat wersji Mahout w usłudze HDInsight, zobacz [HDInsight wersje i składniki platformy Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Opis zalecenia

Jedną z funkcji, które są udostępniane przez Mahout jest aparatem zalecenia. Ten aparat akceptuje dane w formacie `userID`, `itemId`, i `prefValue` (preferencji elementu). Mahout można wykonywać analizy w celu oznaczenia wystąpienia wspólnej: *użytkowników, którzy mają preferencji elementu również mieć preferencji tych innych elementów*. Mahout określa użytkownikom preferencje podobnych elementów, które mogą być używane zaleceń.

Poniższy przepływ pracy jest uproszczony przykład korzystający z danych film:

* **wystąpienie wspólnej**: Jan, Alicja i Robert wszystkie zbędne *słów*, *ponownie ataki Empire*, i *powrotu Jedi*. Mahout Określa, że użytkownicy, którzy także, takich jak jeden z tych filmów, takich jak pozostałe dwa.

* **wystąpienie wspólnej**: Robert i Alicja również zbędne *zagrożenie fantom*, *ataku klonów*, i *zemsty Sith*. Mahout Określa, że użytkownicy, którzy także zbędne poprzednie trzy filmów, takich jak te trzy filmów.

* **Zalecenie podobieństwa**: ponieważ Jan zbędne pierwsze trzy filmów, Mahout analizuje filmy tej osoby z podobne preferencje zbędne, ale Jan nie ma obserwowane (zbędne/klasyfikowane). W takim przypadku zaleca Mahout *zagrożenie fantom*, *ataku klonów*, i *zemsty Sith*.

### <a name="understanding-the-data"></a>Opis danych

Wygodnie [GroupLens Research] [ movielens] zapewnia filmów w formacie, który jest zgodny z Mahout klasyfikacji danych. Te dane są dostępne w magazynie domyślne klastra na `/HdiSamples/HdiSamples/MahoutMovieData`.

Istnieją dwa pliki `moviedb.txt` i `user-ratings.txt`. `user-ratings.txt` Plik jest używany podczas analizy. `moviedb.txt` Służy do zapewnienia informacji tekstowych przyjazną dla użytkownika podczas przeglądania wyników.

Dane zawarte w ratings.txt użytkownika ma struktury `userID`, `movieID`, `userRating`, i `timestamp`, która wskazuje, jak bardzo każdego użytkownika oceną filmu. Oto przykładowe dane:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Analiza jest przeprowadzana

Z połączenia SSH do klastra użyj następującego polecenia, aby uruchomić zadanie zalecenia:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> Zadanie może potrwać kilka minut, a może uruchomić wielu zadań MapReduce.

## <a name="view-the-output"></a>Wyświetl dane wyjściowe

1. Po zakończeniu zadania, użyj następującego polecenia, aby wyświetlić wygenerowanych danych wyjściowych:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Dane wyjściowe wygląda następująco:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Pierwsza kolumna `userID`. Wartości zawartych w "[" i "]" są `movieId`:`recommendationScore`.

2. Dane wyjściowe, wraz z moviedb.txt, można użyć o podanie dodatkowych informacji zalecenia. Po pierwsze skopiuj pliki lokalnie za pomocą następujących poleceń:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    To polecenie powoduje skopiowanie danych wyjściowych w pliku o nazwie **recommendations.txt** w bieżącym katalogu, wraz z filmem plików danych.

3. Użyj następującego polecenia, aby utworzyć skrypt w języku Python, które wyszukuje nazwy filmu danych wyjściowych zalecenia:

    ```bash
    nano show_recommendations.py
    ```

    Po otwarciu edytora, użyj następującego tekstu jako zawartość pliku:

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

    Naciśnij klawisz **Ctrl-X**, **Y**, a na końcu **Enter** do zapisania danych.

4. Uruchom skrypt w języku Python. Polecenie przyjęto założenie, że znajdują się w katalogu, w którym wszystkie pliki zostały pobrane:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    To polecenie analizuje zalecenia wygenerowany dla użytkownika 4 identyfikator.

    * **Ratings.txt użytkownika** plik jest używany do pobierania filmów, które zostały sklasyfikowane.

    * **Moviedb.txt** pliku służy do pobierania nazwy filmów.

    * **Recommendations.txt** służy do pobierania zaleceń filmu dla tego użytkownika.

     Dane wyjściowe tego polecenia jest podobny do następującego tekstu:

        7 lat w Tibet (1997), wynik = 5.0 Kowalski Indiana i ostatniego Crusade (1989), wynik = 5.0 Jaws (1975), wynik = 5.0 znaczeniu i świadomości (1995), wynik = 5.0 niezależność dzień (ID4) (1996), wynik = 5.0 Moje najlepszy przyjaciel ślubu (1997), wynik = 5.0 Jerry Maguire (1996), wynik = 5.0 Scream 2 (1997), wynik = 5.0 czas Kill, (1996), wynik = 5.0

## <a name="delete-temporary-data"></a>Usuń dane tymczasowe

Mahout zadania nie należy usuwać dane tymczasowe, który jest tworzony podczas przetwarzania zadania. `--tempDir` Parametr jest określony w zadaniu przykład do izolowania plików tymczasowych do określonej ścieżki do łatwego usunięcia. Aby usunąć pliki tymczasowe, użyj następującego polecenia:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Ponownie uruchom polecenie, należy również usunąć katalog wyjściowy. Aby usunąć ten katalog, skorzystaj z następujących:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu używania Mahout, odnajdywanie inne sposoby pracy z danymi w usłudze HDInsight:

* [Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Pig z usługą HDInsight](hdinsight-use-pig.md)
* [MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
