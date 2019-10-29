---
title: Generowanie rekomendacji przy użyciu oprogramowania Apache Mahout w usłudze Azure HDInsight
description: Dowiedz się, jak wygenerować zalecenia dotyczące filmów za pomocą usługi HDInsight (Hadoop) za pomocą biblioteki Apache Mahout Machine Learning.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 3923abd10fc3a64773d561b1f375f9e2f00a7e56
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044562"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Generowanie zaleceń dotyczących filmów przy użyciu platformy Apache Mahout z Apache Hadoop w usłudze HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak generować zalecenia dotyczące filmu przy użyciu biblioteki [Apache Mahout](https://mahout.apache.org) Machine Learning z usługą Azure HDInsight.

Mahout to biblioteka [uczenia maszynowego](https://en.wikipedia.org/wiki/Machine_learning) dla Apache Hadoop. Mahout zawiera algorytmy przetwarzania danych, takie jak filtrowanie, klasyfikacja i klastrowanie. W tym artykule opisano użycie aparatu rekomendacji do generowania zaleceń dotyczących filmów, które są oparte na oglądanych przez znajomych filmach.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Obsługa wersji oprogramowania Apache Mahout

Aby uzyskać więcej informacji na temat wersji programu Mahout w usłudze HDInsight, zobacz [wersje usługi HDInsight i składniki Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Omówienie zaleceń

Jedną z funkcji dostarczanych przez Mahout jest aparat rekomendacji. Ten aparat akceptuje dane w formacie `userID`, `itemId`i `prefValue` (Preferencja dla elementu). Mahout może następnie wykonać analizę wspólnego wystąpienia, aby określić: *Użytkownicy, którzy mają preferencję dla elementu, również mają preferencję dla tych innych elementów*. Mahout następnie określa użytkowników z preferencjami podobne elementy, które mogą służyć do uzyskania rekomendacji.

Poniższy przepływ pracy to uproszczony przykład, który używa danych filmowych:

* **Wspólne wystąpienie**: Jan, Alicja i Robert wszystkie Niemniej *,* *Empire uderzają z powrotem*i *zwracają Jedi*. Mahout określa, że użytkownicy, którzy lubią jeden z tych filmów, lubią również pozostałe dwa.

* **Wspólne wystąpienie**: Robert i Alicja również polubili *Menace Fantom*, *atakujący klony*i *Revenge Sith*. Mahout określa, że użytkownicy, którzy lubią poprzednie trzy filmy, również lubią te trzy filmy.

* **Zalecenie dotyczące podobieństwa**: ze względu na to, że Jan polubili pierwsze trzy filmy, Mahout przegląda filmy, które przypominają inne podobne preferencje, ale Jan nie czuje się (podobieństwo/sklasyfikowany). W takim przypadku Mahout zaleca *Fantom Menace*, *ataki klonów*i *Revenge Sith*.

### <a name="understanding-the-data"></a>Zrozumienie danych

Wygodnie [GroupLens Research](https://grouplens.org/datasets/movielens/) udostępnia dane klasyfikacji dla filmów w formacie zgodnym z Mahout. Te dane są dostępne w domyślnym magazynie klastra w `/HdiSamples/HdiSamples/MahoutMovieData`.

Istnieją dwa pliki, `moviedb.txt` i `user-ratings.txt`. Plik `user-ratings.txt` jest używany podczas analizy. `moviedb.txt` służy do dostarczania przyjaznych dla użytkownika informacji tekstowych podczas wyświetlania wyników.

Dane zawarte w User-ratings. txt mają strukturę `userID`, `movieID`, `userRating`i `timestamp`, która wskazuje, jak wysoce każdy użytkownik ocenia film. Oto przykład danych:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Uruchamianie analizy

Z poziomu połączenia SSH z klastrem Użyj następującego polecenia, aby uruchomić zadanie rekomendacji:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Ukończenie zadania może potrwać kilka minut i może działać wiele zadań MapReduce.

## <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić wygenerowane dane wyjściowe:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Dane wyjściowe są wyświetlane w następujący sposób:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Pierwsza kolumna jest `userID`. Wartości zawarte w "[" i "]" są `movieId`:`recommendationScore`.

2. Możesz użyć danych wyjściowych wraz z MovieDB. txt, aby uzyskać więcej informacji na temat zaleceń. Najpierw skopiuj pliki przy użyciu następujących poleceń:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    To polecenie kopiuje dane wyjściowe do pliku o nazwie **rekomendacje. txt** w bieżącym katalogu wraz z plikami danych filmu.

3. Użyj następującego polecenia, aby utworzyć skrypt języka Python, który wyszukuje nazwy filmów w danych wyjściowych zaleceń:

    ```bash
    nano show_recommendations.py
    ```

    Gdy Edytor zostanie otwarty, użyj następującego tekstu jako zawartości pliku:

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

    Naciśnij **klawisze Ctrl-X**, **Y**i **Enter** , aby zapisać dane.

4. Uruchom skrypt języka Python. Następujące polecenie założono, że jesteś w katalogu, w którym wszystkie pliki zostały pobrane:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    To polecenie sprawdza zalecenia wygenerowane dla użytkownika o IDENTYFIKATORze 4.

   * Plik **User-ratings. txt** służy do pobierania przeklasyfikowanych filmów.

   * Plik **MovieDB. txt** służy do pobierania nazw filmów.

   * **Zalecenia. txt** są używane do pobierania zaleceń dotyczących filmu dla tego użytkownika.

     Dane wyjściowe tego polecenia są podobne do następującego tekstu:

       Siedem lat w Tibet (1997), Score = 5.0 Indiana Kowalski i ostatni Crusade (1989), Score = 5.0 szczęk (1975), Score = 5.0 sens i Sensibility (1995), Score = 5.0 niezależność (ID4), Score = 5.0 moje najlepsze przyjaciela (1996), Score = 5.0 Jerry Maguire (1997), Score = 5.0 Scream 2 (1997), Score = 5.0 czas zakończenia, A (1996), Score = 5.0

## <a name="delete-temporary-data"></a>Usuń dane tymczasowe

Zadania Mahout nie usuwają danych tymczasowych, które są tworzone podczas przetwarzania zadania. Parametr `--tempDir` jest określony w przykładowym zadaniu do izolowania plików tymczasowych w określonej ścieżce, aby można było łatwo usunąć. Aby usunąć pliki tymczasowe, użyj następującego polecenia:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Jeśli chcesz ponownie uruchomić polecenie, musisz również usunąć katalog wyjściowy. Aby usunąć ten katalog, wykonaj następujące czynności:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z usługi Mahout, odkryj inne sposoby pracy z danymi w usłudze HDInsight:

* [Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Apache świni z usługą HDInsight](hdinsight-use-pig.md)
* [MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)