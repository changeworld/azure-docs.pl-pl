---
title: Generowanie rekomendacji przy użyciu programu Apache Mahout w usłudze Azure HDInsight
description: Dowiedz się, jak używać biblioteki uczenia maszynowego Apache Mahout do generowania rekomendacji filmowych za pomocą funkcji HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767640"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Generowanie rekomendacji filmowych przy użyciu Apache Mahout z Apache Hadoop w HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak używać biblioteki uczenia maszynowego [Apache Mahout](https://mahout.apache.org) za pomocą usługi Azure HDInsight do generowania rekomendacji filmowych.

Mahout to biblioteka [uczenia maszynowego](https://en.wikipedia.org/wiki/Machine_learning) dla Apache Hadoop. Mahout zawiera algorytmy przetwarzania danych, takie jak filtrowanie, klasyfikacja i klastrowanie. W tym artykule użyjesz aparatu rekomendacji do generowania rekomendacji filmów, które są oparte na filmach, które widzieli twoi znajomi.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout przechowywanie wersji

Aby uzyskać więcej informacji na temat wersji programu Mahout w formacie HDInsight, zobacz [wersje HDInsight i komponenty Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="understanding-recommendations"></a>Zrozumienie zaleceń

Jedną z funkcji, która jest dostarczana przez Mahout jest aparat rekomendacji. Ten aparat akceptuje dane w `userID` `itemId`formacie `prefValue` , i (preferencja dla elementu). Mahout może następnie przeprowadzić analizę współodpadową w celu określenia: *użytkownicy, którzy preferują element, mają również preferencje dla tych innych elementów.* Mahout następnie określa użytkowników z preferencjami podobnych elementów, które mogą służyć do tworzenia zaleceń.

Następujący przepływ pracy jest uproszczonym przykładem, w który użyto danych filmowych:

* **Co-occurrence**: Joe, Alice i Bob wszyscy lubili *Star Wars*, *Imperium kontratakuje*i *Powrót Jedi*. Mahout określa, że użytkownicy, którzy lubią jeden z tych filmów również jak pozostałe dwa.

* **Współwystępowanie:** Bob i Alice również lubił *Phantom Menace*, *Attack of the Clones*i Revenge of the *Sith*. Mahout określa, że użytkownicy, którzy lubili poprzednie trzy filmy również lubią te trzy filmy.

* **Zalecenie podobieństwa:** Ponieważ Joe lubił pierwsze trzy filmy, Mahout patrzy na filmy, które inni o podobnych preferencjach lubili, ale Joe nie oglądał (lubił / oceniał). W tym przypadku Mahout poleca *The Phantom Menace*, *Attack of the Clones*i *Revenge of the Sith.*

### <a name="understanding-the-data"></a>Opis danych

[GroupLens Research](https://grouplens.org/datasets/movielens/) zapewnia dane ratingowe dla filmów w formacie zgodnym z mahoutem. Te dane są dostępne w domyślnym `/HdiSamples/HdiSamples/MahoutMovieData`magazynie klastra pod adresem .

Istnieją dwa pliki `moviedb.txt` `user-ratings.txt`i . Plik `user-ratings.txt` jest używany podczas analizy. Jest `moviedb.txt` używany do dostarczania przyjaznych dla użytkownika informacji tekstowych podczas przeglądania wyników.

Dane zawarte w `user-ratings.txt` ma strukturę `movieID` `userRating` `userID`, `timestamp`, i , który wskazuje, jak wysoko każdy użytkownik ocenił film. Oto przykład danych:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Uruchamianie analizy

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby uruchomić zadanie rekomendacji, użyj następującego polecenia:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Zadanie może potrwać kilka minut, aby zakończyć i może uruchomić wiele mapreduce zadań.

## <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić wygenerowane dane wyjściowe:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Dane wyjściowe są wyświetlane w następujący sposób:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    Pierwsza kolumna `userID`to . Wartości zawarte w '[' i ']' to: `movieId``recommendationScore`.

2. Można użyć danych wyjściowych, wraz z moviedb.txt, aby zapewnić więcej informacji na temat zaleceń. Najpierw skopiuj pliki lokalnie za pomocą następujących poleceń:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    To polecenie kopiuje dane wyjściowe do pliku o nazwie **recommendations.txt** w bieżącym katalogu wraz z plikami danych filmu.

3. Użyj następującego polecenia, aby utworzyć skrypt języka Python, który wyszukuje nazwy filmów dla danych w danych wyjściowych zaleceń:

    ```bash
    nano show_recommendations.py
    ```

    Po otwarciu edytora użyj następującego tekstu jako zawartości pliku:

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

    Naciśnij **klawisze Ctrl-X**, **Y**, a na koniec **klawisz Enter,** aby zapisać dane.

4. Uruchom skrypt Języka Python. Następujące polecenie zakłada, że znajdujesz się w katalogu, w którym pobrano wszystkie pliki:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    To polecenie analizuje zalecenia wygenerowane dla identyfikatora użytkownika 4.

   * Plik **user-ratings.txt** służy do pobierania filmów, które zostały ocenione.

   * Plik **moviedb.txt** służy do pobierania nazw filmów.

   * **Recommendations.txt** służy do pobierania rekomendacji filmu dla tego użytkownika.

     Dane wyjściowe z tego polecenia są podobne do następującego tekstu:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Usuwanie danych tymczasowych

Zadania Mahout nie usuwają tymczasowych danych, które są tworzone podczas przetwarzania zadania. Parametr `--tempDir` jest określony w przykładowym zadaniu, aby wyizolować pliki tymczasowe do określonej ścieżki w celu łatwego usunięcia. Aby usunąć pliki tymczasowe, użyj następującego polecenia:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Jeśli chcesz ponownie uruchomić polecenie, należy również usunąć katalog wyjściowy. Aby usunąć ten katalog, użyj następujących elementów:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z programu Mahout, odkryj inne sposoby pracy z danymi w programie HDInsight:

* [Ul Apache z hdinsight](hdinsight-use-hive.md)
* [MapReduce z HDInsight](hdinsight-use-mapreduce.md)
