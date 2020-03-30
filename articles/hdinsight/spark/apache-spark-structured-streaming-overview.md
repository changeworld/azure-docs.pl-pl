---
title: Przesyłanie strumieniowe w strukturze platformy Spark w usłudze Azure HDInsight
description: Jak korzystać z aplikacji do przesyłania strumieniowego w strukturze platformy Spark w klastrach platformy HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548840"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Omówienie strumieniowania strukturalnego Apache Spark

[Apokaz Iskra](https://spark.apache.org/) Usługa ustrukturyzowania przesyłania strumieniowego umożliwia implementowanie skalowalnych, odpornych na uszkodzenia aplikacji o wysokiej przepływie do przetwarzania strumieni danych. Usługa strumieniowania strukturalnego jest oparta na silniku SQL platformy Spark i poprawia konstrukcje z ramek danych i zestawów danych programu Spark SQL, dzięki czemu można zapisywać zapytania przesyłania strumieniowego w taki sam sposób, jakpytujesz zapytania wsadowe.  

Aplikacje do przesyłania strumieniowego strukturalnego działają w klastrach platformy SPARK usługi HDInsight i łączą się z danymi przesyłania strumieniowego z [usługi Apache Kafka](https://kafka.apache.org/), gniazda TCP (do celów debugowania), usługi Azure Storage lub usługi Azure Data Lake Storage. Te dwie ostatnie opcje, które opierają się na zewnętrznych usługach magazynu, umożliwiają obserwowanie nowych plików dodanych do magazynu i przetwarzanie ich zawartości tak, jakby były przesyłane strumieniowo.

Usługa strumieniowania strukturalnego tworzy długotrwałą kwerendę, podczas której można zastosować operacje do danych wejściowych, takie jak wybór, projekcja, agregacja, okno i łączenie przesyłania strumieniowego DataFrame z referencyjnymi dataframe. Następnie wyniki są wyprowadzane do magazynu plików (obiektów Blobs usługi Azure Storage lub Data Lake Storage) lub do dowolnego magazynu danych przy użyciu kodu niestandardowego (takiego jak baza danych SQL lub usługa Power BI). Usługa strumieniowania strukturalnego zapewnia również dane wyjściowe do konsoli do debugowania lokalnie i do tabeli w pamięci, dzięki czemu można zobaczyć dane wygenerowane do debugowania w hdinsight.

![Przetwarzanie strumienia za pomocą usługi HDInsight i strumieniowania w strukturze iskry](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Spark Structured Streaming zastępuje Spark Streaming (DStreams). Idąc dalej, ustrukturyzowane przesyłanie strumieniowe będzie otrzymywać ulepszenia i konserwację, podczas gdy strumienie DStream będą dostępne tylko w trybie konserwacji. Usługa przesyłania strumieniowego strukturalnego nie jest obecnie tak funkcjonami jak strumienie D dla źródeł i pochłaniaczy, które obsługuje po wyjęciu z pudełka, więc oceń wymagania, aby wybrać odpowiednią opcję przetwarzania strumienia platformy Spark.

## <a name="streams-as-tables"></a>Strumienie jako tabele

Spark Structured Streaming reprezentuje strumień danych jako tabela, która jest nieograniczona w głębi, oznacza to, że tabela nadal rośnie w miarę pojawiania się nowych danych. Ta *tabela danych wejściowych* jest stale przetwarzana przez długotrwałą kwerendę, a wyniki wysyłane do *tabeli danych wyjściowych:*

![Ustrukturyzowane koncepcje przesyłania strumieniowego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

W ustrukturyzowanym strumieniowaniu dane docierają do systemu i są natychmiast pojmowane w tabeli wprowadzania. Piszesz kwerendy (przy użyciu interfejsów API dataframe i zestawu danych), które wykonują operacje względem tej tabeli wejściowej. Dane wyjściowe kwerendy dają inną tabelę, *tabelę wyników*. Tabela wyników zawiera wyniki kwerendy, z której można rysować dane dla zewnętrznego magazynu danych, takiej relacyjnej bazy danych. Czas przetwarzania danych z tabeli wejściowej jest kontrolowany przez *interwał wyzwalania*. Domyślnie interwał wyzwalacza wynosi zero, więc usługa Structured Streaming próbuje przetworzyć dane natychmiast po ich odebraniu. W praktyce oznacza to, że jak tylko structured streaming odbywa się przetwarzania uruchomienia poprzedniej kwerendy, rozpoczyna się inne przetwarzanie uruchomić względem wszystkich nowo odebranych danych. Wyzwalacz można skonfigurować do uruchamiania w danym przedziale czasu, tak aby dane przesyłania strumieniowego były przetwarzane w partiach opartych na czasie.

Dane w tabelach wyników mogą zawierać tylko dane, które są nowe od czasu ostatniego przetworzenia kwerendy *(tryb dołączania)* lub tabela może być odświeżana za każdym razem, gdy pojawią się nowe dane, więc tabela zawiera wszystkie dane wyjściowe od momentu rozpoczęcia kwerendy strumieniowej *(tryb zakończenia).*

### <a name="append-mode"></a>Tryb dołączania

W trybie dołączania tylko wiersze dodane do tabeli wyników od ostatniego uruchomienia kwerendy są obecne w tabeli wyników i zapisywane w magazynie zewnętrznym. Na przykład najprostsza kwerenda po prostu kopiuje wszystkie dane z tabeli wprowadzania do tabeli wyników bez obrażeń. Za każdym razem, gdy upłynie interwał wyzwalacza, nowe dane są przetwarzane, a wiersze reprezentujące nowe dane są wyświetlane w tabeli wyników.

Rozważmy scenariusz, w którym przetwarzasz dane telemetryczne z czujników temperatury, takich jak termostat. Załóżmy, że pierwszy wyzwalacz przetworzył jedno zdarzenie w czasie 00:01 dla urządzenia 1 z odczytem temperatury 95 stopni. W pierwszym wyzwalaczu kwerendy w tabeli wyników pojawia się tylko wiersz z czasem 00:01. W czasie 00:02 po nadejściu innego zdarzenia, tylko nowy wiersz jest wiersz z czasem 00:02 i tak tabela wyników będzie zawierać tylko jeden wiersz.

![Tryb dołączania strumieniowego strukturalnego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

W przypadku korzystania z trybu dołączania kwerenda będzie stosować rzuty (wybieranie kolumn, na których jej zależy), filtrowanie (wybieranie tylko wierszy spełniających określone warunki) lub łączenie (powiększanie danych o dane ze statycznej tabeli odnośności). Tryb dołączania ułatwia wypychanie tylko odpowiednich nowych danych do zewnętrznej pamięci masowej.

### <a name="complete-mode"></a>Tryb kompletny

Należy wziąć pod uwagę ten sam scenariusz, tym razem przy użyciu trybu complete. W trybie kompletnym cała tabela danych wyjściowych jest odświeżana na każdym wyzwalaczu, dzięki czemu tabela zawiera dane nie tylko z ostatniego uruchomienia wyzwalacza, ale ze wszystkich uruchomień. Można użyć trybu pełnego, aby skopiować dane bez ekstremów z tabeli wprowadzania do tabeli wyników. Przy każdym wyzwalanym przebiegu nowe wiersze wyników są wyświetlane wraz ze wszystkimi poprzednimi wierszami. Tabela wyników wyjściowych będzie kończyć przechowywania wszystkich danych zebranych od początku kwerendy i ostatecznie zabraknie pamięci. Tryb Complete jest przeznaczony do użytku z zapytaniami agregującymi, które podsumowują przychodzące dane w jakiś sposób, więc na każdym wyzwalaczu tabela wyników jest aktualizowana o nowe podsumowanie.

Załóżmy, że do tej pory jest pięć sekund wartości danych już przetworzonych, i nadszedł czas, aby przetworzyć dane na szóstą sekundę. Tabela wprowadzania ma zdarzenia dla czasu 00:01 i czasu 00:03. Celem tego przykładowego zapytania jest podanie średniej temperatury urządzenia co pięć sekund. Implementacja tej kwerendy stosuje agregację, która przyjmuje wszystkie wartości, które mieszczą się w każdym 5-sekundowym oknie, uśrednia temperaturę i tworzy wiersz dla średniej temperatury w tym przedziale. Na końcu pierwszego okna 5-sekundowe znajdują się dwie krotki: (00:01, 1, 95) i (00:03, 1, 98). Tak więc dla okna 00:00-00:05 agregacja tworzy krotki ze średnią temperaturą 96,5 stopni. W następnym 5-sekundowym oknie jest tylko jeden punkt danych w czasie 00:06, więc średnia temperatura wynosi 98 stopni. W czasie 00:10, przy użyciu trybu complete, tabela wyników ma wiersze dla obu okien 00:00-00:05 i 00:05-00:10, ponieważ kwerenda wyprowadza wszystkie zagregowane wiersze, a nie tylko nowe. W związku z tym tabela wyników nadal rośnie w miarę dodawania nowych okien.

![Tryb kompletnego strumieniowania strukturalnego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nie wszystkie zapytania przy użyciu trybu complete spowoduje tabela rosnąć bez granic.  Należy wziąć pod uwagę w poprzednim przykładzie, że zamiast uśredniania temperatury według przedziału czasu, to uśredniona zamiast tego według identyfikatora urządzenia. Tabela wyników zawiera stałą liczbę wierszy (po jednym na urządzenie) ze średnią temperaturą dla urządzenia we wszystkich punktach danych odebranych z tego urządzenia. Po otrzymaniu nowych temperatur tabela wyników jest aktualizowana tak, aby średnie w tabeli były zawsze aktualne.

## <a name="components-of-a-spark-structured-streaming-application"></a>Składniki aplikacji do przesyłania strumieniowego w strukturze iskryskowej

Proste przykładowe zapytanie może podsumować odczyty temperatury według godzinnych okien. W takim przypadku dane są przechowywane w plikach JSON w usłudze Azure Storage (dołączonych jako domyślny magazyn dla klastra HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Te pliki JSON są `temps` przechowywane w podfolderze pod kontenerem klastra HDInsight.

### <a name="define-the-input-source"></a>Definiowanie źródła wejściowego

Najpierw skonfiguruj formę dataframe, która opisuje źródło danych i wszelkie ustawienia wymagane przez to źródło. W tym przykładzie rysuje się z plików JSON w usłudze Azure Storage i stosuje schemat do nich w czasie odczytu.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Stosowanie kwerendy

Następnie zastosuj kwerendę, która zawiera żądane operacje względem przesyłania strumieniowego dataframe. W takim przypadku agregacja grupuje wszystkie wiersze w okna 1-godzinne, a następnie oblicza temperatury minimalne, średnie i maksymalne w tym oknie 1-godzinnym.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Zdefiniuj zlew wyjściowy

Następnie zdefiniuj miejsce docelowe dla wierszy, które są dodawane do tabeli wyników w każdym interwale wyzwalacza. W tym przykładzie tylko wyprowadza wszystkie wiersze do tabeli `temps` w pamięci, które można później kwerendy z SparkSQL. Tryb pełnego wyjścia zapewnia, że wszystkie wiersze dla wszystkich okien są wyprowadzane za każdym razem.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Uruchamianie kwerendy

Uruchom kwerendę przesyłania strumieniowego i uruchom do momentu odebraniem sygnału zakończenia.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Wyświetlanie wyników

Gdy kwerenda jest uruchomiona, w tym samym SparkSession, można uruchomić `temps` kwerendę SparkSQL względem tabeli, w której są przechowywane wyniki kwerendy.

    select * from temps

Ta kwerenda daje wyniki podobne do następujących:

| okno |  min(temp) | avg(temp) | max(temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Aby uzyskać szczegółowe informacje na temat interfejsu API strumienia strukturalnego platformy Spark wraz z źródłem danych wejściowych, operacjami i pochłaniaczami wyjściowymi, które obsługuje, zobacz [Apache Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Dzienniki punktów kontrolnych i zapisu z wyprzedzeniem

Aby zapewnić odporność i odporność na uszkodzenia, ustrukturyzowane przesyłanie strumieniowe opiera się na *punktach kontrolnych,* aby zapewnić, że przetwarzanie strumienia może być kontynuowane nieprzerwanie, nawet w przypadku awarii węzłów. W usłudze HDInsight program Spark tworzy punkty kontrolne do trwałego magazynu, usługi Azure Storage lub Data Lake Storage. Te punkty kontrolne przechowują informacje o postępie kwerendy przesyłania strumieniowego. Ponadto usługa Structured Streaming używa *dziennika zapisu z wyprzedzeniem* (WAL). WW przechwytuje pozyskane dane, które zostały odebrane, ale nie zostały jeszcze przetworzone przez kwerendę. Jeśli wystąpi błąd i przetwarzanie zostanie ponownie uruchomione z WAL, wszystkie zdarzenia odebrane ze źródła nie zostaną utracone.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego w trybie iskrowym

Zazwyczaj tworzysz aplikację Spark Streaming lokalnie w pliku JAR, a następnie wdrażasz ją w programie Spark on HDInsight, kopiując plik JAR do domyślnego magazynu dołączonego do klastra HDInsight. Aplikację można uruchomić za pomocą interfejsów API [Apache Livy](https://livy.incubator.apache.org/) REST dostępnych w klastrze przy użyciu operacji POST. Treść POST zawiera dokument JSON, który zawiera ścieżkę do JAR, nazwę klasy, której główna metoda definiuje i uruchamia aplikację przesyłania strumieniowego, a opcjonalnie wymagania dotyczące zasobów zadania (takie jak liczba wykonawców, pamięci i rdzeni) i wszelkie ustawienia konfiguracji, które wymaga kodu aplikacji.

![Wdrażanie aplikacji do przesyłania strumieniowego platformy Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stan wszystkich aplikacji można również sprawdzić za pomocą żądania GET względem punktu końcowego LIVY. Na koniec można zakończyć uruchomionej aplikacji, wystawiając żądanie DELETE względem punktu końcowego LIVY. Aby uzyskać szczegółowe informacje na temat interfejsu API LIVY, zobacz [Zadania zdalne z apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Structured Streaming Programming Przewodnik](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Uruchom apache spark zadania zdalnie z Apache LIVY](apache-spark-livy-rest-interface.md)
