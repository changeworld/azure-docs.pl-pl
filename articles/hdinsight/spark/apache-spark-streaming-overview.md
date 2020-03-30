---
title: Przesyłanie strumieniowe iskrą w usłudze Azure HDInsight
description: Jak korzystać z aplikacji Apache Spark Streaming w klastrach HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406263"
---
# <a name="overview-of-apache-spark-streaming"></a>Omówienie przesyłania strumieniowego w ramach apache Spark

[Apokaz Iskra](https://spark.apache.org/) Przesyłanie strumieniowe zapewnia przetwarzanie strumienia danych w klastrach platformy SPARK usługi HDInsight z gwarancją, że każde zdarzenie wejściowe jest przetwarzane dokładnie raz, nawet jeśli wystąpi błąd węzła. Spark Stream to długotrwałe zadanie, które odbiera dane wejściowe z wielu różnych źródeł, w tym usługi Azure Event Hubs, usługi Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), surowe gniazda TCP lub z monitorowania [apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) systemów plików. W przeciwieństwie do procesu opartego wyłącznie na zdarzeniach, strumień platformy Spark partii danych wejściowych w oknach czasowych, takich jak 2-sekundowy plasterek, a następnie przekształca każdą partię danych przy użyciu map, zmniejszyć, sprzężenia i wyodrębnić operacji. Następnie program Spark Stream zapisuje przekształcone dane w systemach plików, bazach danych, pulpitach nawigacyjnych i konsoli.

![Przetwarzanie strumienia z dostępem HDInsight i Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Aplikacje spark Streaming musi czekać ułamek sekundy, aby zebrać każdą *mikro-partii* zdarzeń przed wysłaniem tej partii do przetwarzania. Z drugiej strony aplikacja oparta na zdarzeniach przetwarza każde zdarzenie natychmiast. Opóźnienie przesyłania strumieniowego platformy Spark jest zazwyczaj w ciągu kilku sekund. Korzyści z podejścia mikro-partii są bardziej efektywne przetwarzanie danych i prostsze obliczenia zbiorcze.

## <a name="introducing-the-dstream"></a>Przedstawiamy DStream

Spark Streaming reprezentuje ciągły strumień przychodzących danych przy użyciu *discretized strumienia* o nazwie DStream. DStream można utworzyć ze źródeł wejściowych, takich jak Centra zdarzeń lub Kafka lub stosując przekształcenia na innym DStream.

A DStream zapewnia warstwę abstrakcji na podstawie nieprzetworzonych danych zdarzeń.

Zacznij od pojedynczego zdarzenia, powiedz odczyt temperatury z podłączonego termostatu. Gdy to zdarzenie dociera do aplikacji Spark Streaming, zdarzenie jest przechowywane w niezawodny sposób, gdzie jest replikowany w wielu węzłach. Ta odporność na uszkodzenia zapewnia, że awaria dowolnego pojedynczego węzła nie spowoduje utraty zdarzenia. Rdzeń platformy Spark używa struktury danych, która dystrybuuje dane w wielu węzłach w klastrze, gdzie każdy węzeł zazwyczaj przechowuje własne dane w pamięci dla najlepszej wydajności. Ta struktura danych jest nazywana *odpornym rozproszonym zestawem danych* (RDD).

Każda rdd reprezentuje zdarzenia zebrane w ramach czasowych zdefiniowanych przez użytkownika o nazwie *interwał partii*. W miarę upływu każdego interwału partii jest produkowany nowy RDD, który zawiera wszystkie dane z tego interwału. Ciągły zestaw RDD są zbierane w DStream. Na przykład jeśli interwał partii jest jedna sekunda długości, DStream emituje partii co sekundę zawierające jeden RDD, który zawiera wszystkie dane pojął w ciągu tej sekundy. Podczas przetwarzania DStream zdarzenie temperatury pojawia się w jednej z tych partii. Aplikacja Spark Streaming przetwarza partie, które zawierają zdarzenia i ostatecznie działa na dane przechowywane w każdym RDD.

![Przykład DStream ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikacji Do przesyłania strumieniowego iskrów

Aplikacja Do przesyłania strumieniowego platformy Spark jest aplikacją o długim czasie, która odbiera dane ze źródeł pozyskiwania, stosuje transformacje do przetwarzania danych, a następnie wypycha dane do jednego lub więcej miejsc docelowych. Struktura aplikacji Spark Streaming ma część statyczną i dynamiczną. Część statyczna określa, skąd pochodzą dane, co należy wykonać na danych i gdzie powinny być wyniki. Część dynamiczna działa aplikację przez czas nieokreślony, czekając na sygnał zatrzymania.

Na przykład następująca prosta aplikacja odbiera wiersz tekstu za pomocą gniazda TCP i zlicza liczbę wyświetleń każdego wyrazu.

### <a name="define-the-application"></a>Zdefiniuj aplikację

Definicja logiki aplikacji ma cztery kroki:

1. Utwórz streamingContext.
2. Utwórz DStream z StreamingContext.
3. Zastosuj przekształcenia do DStream.
4. Wysuń wyniki.

Ta definicja jest statyczna i żadne dane nie są przetwarzane do momentu uruchomienia aplikacji.

#### <a name="create-a-streamingcontext"></a>Tworzenie kontekstu przesyłania strumieniowego

Utwórz StreamingContext z SparkContext, który wskazuje na klastra. Podczas tworzenia streamingcontext, należy określić rozmiar partii w sekundach, na przykład:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Tworzenie strumienia DStream

Za pomocą wystąpienia StreamingContext utwórz wejściowy DStream dla źródła wejściowego. W takim przypadku aplikacja obserwuje pojawienie się nowych plików w domyślnej pamięci masowej dołączonej do klastra HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Stosowanie przekształceń

Zaimplementuj przetwarzanie, stosując przekształcenia na DStream. Ta aplikacja otrzymuje jeden wiersz tekstu na raz z pliku, dzieli każdy wiersz na słowa, a następnie używa wzorca redukcji mapy, aby policzyć liczbę wyświetleń każdego wyrazu.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Wyniki wyjściowe

Wypchnij wyniki transformacji do systemów docelowych, stosując operacje wyjściowe. W takim przypadku wynik każdego uruchomienia za pomocą obliczeń jest drukowany w danych wyjściowych konsoli.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację do przesyłania strumieniowego i uruchom do momentu odebraniem sygnału zakończenia.

```
ssc.start()
ssc.awaitTermination()
```

Aby uzyskać szczegółowe informacje na temat interfejsu API strumienia platformy Spark Stream, wraz ze źródłami zdarzeń, przekształceniami i operacjami wyjściowymi, które obsługuje, zobacz [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Poniższa przykładowa aplikacja jest niezależna, więc można ją uruchomić wewnątrz [notesu Jupyter.](apache-spark-jupyter-notebook-kernels.md) W tym przykładzie tworzy makiety źródła danych w klasie DummySource, który generuje wartość licznika i bieżący czas w milisekundach co pięć sekund. Nowy obiekt StreamingContext ma interwał wsadowy 30 sekund. Za każdym razem, gdy partia jest tworzona, aplikacja do przesyłania strumieniowego sprawdza RDD produkowane, konwertuje RDD do Platformy Spark DataFrame i tworzy tabelę tymczasową zaokrążenia DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Odczekaj około 30 sekund po uruchomieniu aplikacji powyżej.  Następnie można okresowo wysyłać kwerendy do elementu DataFrame, aby wyświetlić bieżący zestaw wartości obecnych w partii, na przykład przy użyciu tej kwerendy SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

Wynikowe dane wyjściowe wyglądają następująco:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Istnieje sześć wartości, ponieważ DummySource tworzy wartość co 5 sekund, a aplikacja emituje partię co 30 sekund.

## <a name="sliding-windows"></a>Okna przesuwne

Aby wykonać obliczenia zagregowane na DStream w pewnym okresie czasu, na przykład, aby uzyskać średnią temperaturę w ciągu ostatnich dwóch sekund, można użyć operacji *okna przesuwnego* dołączone do usługi Spark Streaming. Okno przesuwne ma czas trwania (długość okna) i interwał, w którym zawartość okna są oceniane (interwał slajdów).

Okna przesuwne mogą nakładać się na siebie, na przykład można zdefiniować okno o długości dwóch sekund, które przesuwa się co sekundę. Oznacza to, że za każdym razem, gdy wykonujesz obliczenia agregacji, okno będzie zawierać dane z ostatniej jednej sekundy poprzedniego okna, a także wszelkie nowe dane w następnej sekundzie.

![Przykładowe okno początkowe ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Przykładowe okno ze zdarzeniami temperatury po przesunięciu](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Poniższy przykład aktualizuje kod, który używa DummySource, do zbierania partii w oknie z jednominutowym czasem trwania i jednominutowym slajdem.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Po pierwszej minucie jest 12 wpisów - sześć wpisów z każdej z dwóch partii zebranych w oknie.

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Funkcje okna przesuwnego dostępne w interfejsie API przesyłania strumieniowego platformy Spark obejmują okno, countByWindow, reduceByWindow i countByValueAndWindow. Aby uzyskać szczegółowe informacje na temat tych funkcji, zobacz [Przekształcenia na DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

Aby zapewnić odporność i odporność na uszkodzenia, usługa Spark Streaming opiera się na punktach kontrolnych, aby zapewnić, że przetwarzanie strumienia może być kontynuowane nieprzerwanie, nawet w przypadku awarii węzłów. W usłudze HDInsight platforma Spark tworzy punkty kontrolne do trwałego magazynu (usługa Azure Storage lub magazyn usługi Data Lake). Te punkty kontrolne przechowują metadane dotyczące aplikacji przesyłania strumieniowego, takie jak konfiguracja, operacje zdefiniowane przez aplikację i wszystkie partie, które zostały umieszczone w kolejce, ale nie zostały jeszcze przetworzone. W niektórych przypadkach punkty kontrolne będą również obejmować zapisywanie danych w RDDs, aby szybciej odbudować stan danych z tego, co jest obecne w RDDs zarządzanych przez spark.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego w trybie iskrowym

Zazwyczaj tworzysz aplikację Spark Streaming lokalnie w pliku JAR, a następnie wdrażasz ją w programie Spark on HDInsight, kopiując plik JAR do domyślnego magazynu dołączonego do klastra HDInsight. Aplikację można uruchomić za pomocą interfejsów API REST LIVY dostępnych w klastrze przy użyciu operacji POST. Treść POST zawiera dokument JSON, który zawiera ścieżkę do JAR, nazwę klasy, której główna metoda definiuje i uruchamia aplikację przesyłania strumieniowego, a opcjonalnie wymagania dotyczące zasobów zadania (takie jak liczba wykonawców, pamięci i rdzeni) i wszelkie ustawienia konfiguracji, które wymaga kodu aplikacji.

![Wdrażanie aplikacji do przesyłania strumieniowego platformy Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stan wszystkich aplikacji można również sprawdzić za pomocą żądania GET względem punktu końcowego LIVY. Na koniec można zakończyć uruchomionej aplikacji, wystawiając żądanie DELETE względem punktu końcowego LIVY. Aby uzyskać szczegółowe informacje na temat interfejsu API LIVY, zobacz [Zadania zdalne z apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Streaming Programming Przewodnik](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Uruchom apache spark zadania zdalnie z Apache LIVY](apache-spark-livy-rest-interface.md)
