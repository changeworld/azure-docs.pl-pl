---
title: Przesyłania strumieniowego w usłudze Azure HDInsight Spark
description: Jak korzystać z aplikacji przesyłania strumieniowego platformy Spark w klastrach HDInsight Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 19d77d4aa49008232a01cd3ac2761a796505a35c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712000"
---
# <a name="overview-of-apache-spark-streaming"></a>Przegląd platformy Apache Spark Streaming

[Platforma Apache Spark](https://spark.apache.org/) przesyłania strumieniowego zapewnia przetwarzanie w klastrach HDInsight Spark, gwarancji dowolne dane wejściowe zdarzenia strumienia danych jest przetwarzany tylko raz, nawet w przypadku wystąpienia awarii węzła. Stream Spark jest zadaniem długotrwałych, który odbiera dane wejściowe z wielu różnych źródeł, takich jak usługi Azure Event Hubs, Azure IoT Hub [platformy Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ ](http://zeromq.org/), pierwotne gniazdami TCP, lub z monitorowania [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) systemy plików. W przeciwieństwie do procesu wyłącznie oparte na zdarzeniach Stream Spark partii danych wejściowych na okna czasowe, takie jak wycinek: 2 sekundy i następnie przekształceń dla poszczególnych partii danych przy użyciu map, zmniejszyć, join i wyodrębnianie działań. Stream Spark następnie zapisuje przekształcone dane systemy plików, baz danych, pulpitów nawigacyjnych i konsoli.

![Przetwarzanie za pomocą HDInsight i przesyłania strumieniowego platformy Spark Stream](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Przesyłanie strumieniowe Spark aplikacji musi czekać część 1 sekunda zbierać każdy *micro-batch* zdarzeń przed wysłaniem tej partii potrzeby przetwarzania. Z kolei aplikacji oparte na zdarzeniach przetwarza każde zdarzenie natychmiast. Przesyłanie strumieniowe Spark opóźnienia jest zwykle w ciągu kilku sekund. Korzyści wynikające z podejścia micro partii są bardziej wydajne przetwarzanie danych i łatwiejsze obliczenia agregacji.

## <a name="introducing-the-dstream"></a>Wprowadzenie do DStream

Przesyłanie strumieniowe Spark reprezentuje ciągłego strumienia przychodzących danych przy użyciu *zdyskretyzowany strumienia* o nazwie DStream. Mogą być tworzone DStream ze źródeł wejściowych, takich jak Event Hubs i Kafka lub zastosowania przekształcenia w innym DStream.

DStream zapewnia warstwę abstrakcji nałożoną na dane nieprzetworzone zdarzenia. 

Rozpoczynać pojedyncze zdarzenie, na przykład temperatury odczytu z połączonych termostat. Po odebraniu tego zdarzenia w aplikacji Spark Streaming zdarzenia są przechowywane w niezawodny sposób, w których są replikowane w wielu węzłach. Ten odporności na uszkodzenia gwarantuje, że błąd dowolnego pojedynczego węzła nie spowoduje utraty zdarzenia. Spark core używa struktury danych, które rozprowadza dane w wielu węzłach w klastrze, gdzie każdy węzeł zazwyczaj zachowuje swój własny danych w pamięci w celu uzyskania najlepszej wydajności. Ta struktura danych jest wywoływana *odpornych rozproszonych zestawu danych* (RDD).

Każdy RDD reprezentuje zdarzenia zebrane za pośrednictwem przedziału czasu zdefiniowanych przez użytkownika o nazwie *odstęp czasu dla partii*. Zgodnie z każdej partii on upłynie, nowe RDD jest generowany, która zawiera wszystkie dane z tego interwału. Ciągłego zestawu danych są zbierane w DStream. Na przykład jeśli czasu dla partii jest jedna sekunda długie, Twoje DStream emituje partii co drugi zawierający RDD jeden, zawierający wszystkie dane pozyskane w ciągu sekundy tego. Podczas przetwarzania DStream, temperatury zdarzenie pojawi się jeden z tych partii. Aplikacja usługi Spark Streaming przetwarza partii, które zawiera zdarzenia i ostatecznie działa na danych przechowywanych w każdej RDD.

![Przykład DStream ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikacji do przesyłania strumieniowego platformy Spark

Przesyłanie strumieniowe Spark aplikacja to długotrwałe odbiera dane ze źródeł pozyskiwania, stosuje przekształcenia do przetwarzania danych, która następnie wypychanie danych do jednego lub więcej miejsc docelowych. Struktura aplikacji Spark Streaming ma części statycznych i dynamicznych. Definiuje statycznej części, gdzie dane pochodzą z, jakie operacje na danych, a wyniki powinny przechodzić. Części dynamicznej uruchamia aplikację przez czas nieokreślony, oczekiwania na sygnał zatrzymania.

Na przykład poniższy prostą aplikację odbiera wiersza tekstu za pośrednictwem gniazda TCP i zlicza liczbę wystąpień każdego wyrazu.

### <a name="define-the-application"></a>Definiowanie aplikacji

Definicji aplikacji logiki ma cztery kroki:

1. Utwórz StreamingContext.
2. Utwórz DStream ze StreamingContext.
3. Stosowanie przekształcenia do DStream.
4. Dane wyjściowe.

Ta definicja jest statyczna, a nie dane są przetwarzane do czasu uruchomienia aplikacji.

#### <a name="create-a-streamingcontext"></a>Utwórz StreamingContext

Utwórz StreamingContext na podstawie odporne rozproszone zestawy, wskazujący na klaster. Podczas tworzenia StreamingContext, należy określić rozmiar partii w sekundach, na przykład:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Utwórz DStream

Wystąpienie StreamingContext tworzenie wejściowego DStream dla źródła danych wejściowych. W takim przypadku aplikacja obserwuje wyglądu z nowymi plikami z domyślnego magazynu dołączone do klastra HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Zastosuj przekształcenia

Implementuje się przetwarzanie, stosując przekształcenia na DStream. Ta aplikacja otrzyma jeden wiersz tekstu w czasie z pliku dzieli każdy wiersz na słowa i następnie korzysta ze wzorca map-reduce do zliczania wystąpień każdego wyrazu.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Dane wyjściowe wyniki

Wypchnij wyniki przekształcenia docelowe systemy, stosując operacji wyjścia. W tym przypadku wynik każdego uruchomienia za pomocą obliczeń, wydrukowaniu w danych wyjściowych konsoli.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację przesyłania strumieniowego i uruchom do momentu otrzymania sygnału zakończenia.

```
ssc.start()
ssc.awaitTermination()
```

Szczegółowe informacje na temat interfejsu API usługi Stream platformy Spark, wraz z źródła zdarzeń, transformacji i operacje wyjściowego obsługuje ona [Apache Spark Streaming przewodnik programowania w usłudze](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Następujące Przykładowa aplikacja jest niezależne, aby można było uruchomić go w programie [notesu programu Jupyter](apache-spark-jupyter-notebook-kernels.md). W tym przykładzie tworzy źródło danych testowych w klasie DummySource zwracające wartość licznika i bieżący czas w milisekundach, co pięć sekund. Nowy obiekt StreamingContext ma odstęp czasu dla partii równej 30 sekund. Za każdym razem, gdy tworzony partii przesyłania strumieniowego aplikacji sprawdza RDD utworzone, konwertuje RDD do elementów DataFrame aparatu Spark i tworzy tymczasową tabelę za pośrednictwem ramki danych.

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

Poczekaj około 30 sekund po uruchomieniu aplikacji powyżej.  Następnie możesz zbadać DataFrame okresowo, aby wyświetlić bieżący zestaw wartości znajdujących się w usłudze batch, np. przy użyciu tego zapytania SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

Dane wyjściowe wyglądają następująco:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Istnieje sześć wartości, ponieważ DummySource tworzy wartość co 5 sekund, a aplikacja emituje zadaniu wsadowym co 30 sekund.

## <a name="sliding-windows"></a>Przedłużanie systemu windows

Do przeprowadzenia obliczeń agregacji w swojej DStream przez pewien czas, aby na przykład uzyskać średnia temperatura w ciągu ostatnich dwóch sekund, można użyć *oknem kroczącym* operacje dołączone do przesyłania strumieniowego platformy Spark. Przesuwającego się okna ma czas trwania (długość okna) i interwał, w którym zawartość okna są oceniane (interwał slajdów).

Przedłużanie systemu windows może pokrywać się, na przykład można zdefiniować okno o długości dwóch sekund, która slajdy co sekundę. Oznacza to, za każdym razem, gdy wykonywać obliczenia agregacji, okno będzie zawierać dane w ciągu ostatniej sekundy poprzedniego okna, a także wszelkie nowe dane, w następnej sekundy.

![Przykład początkowego okna ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Przykładowe okno ze zdarzeniami temperatury po przedłużanie](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Poniższy przykład aktualizuje kod, który używa DummySource, można zbierać partii do okna z jednej minuty czas trwania i slajdów jednej minuty.

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

Po pierwszym minucie istnieją wpisy 12 - sześć wpisy z każdej partii dwóch zebranych w oknie.

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

Przewijania funkcje okna dostępny w interfejsie API przesyłania strumieniowego platformy Spark obejmują okna, countByWindow, reduceByWindow i countByValueAndWindow. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [przekształcenia na DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

Aby zapewnić odporność i odporności na uszkodzenia, przesyłanie strumieniowe Spark opiera się na punkty kontrolne, aby upewnić się, że przetwarzanie strumienia można bezproblemowo kontynuują, nawet w przypadku awarii węzła. W HDInsight Spark tworzy punkty kontrolne do trwałego magazynu (Azure Storage lub usługi Data Lake Storage). Te punkty kontrolne są przechowywane metadane dotyczące przesyłania strumieniowego aplikacji, takie jak konfiguracja, operacje zdefiniowane przez aplikację i wszystkie instancje, które były umieszczane w kolejce, ale nie zostały jeszcze przetworzone. W niektórych przypadkach punkty kontrolne zawierają również zapisywać dane w danych, aby szybciej odbudować stanu danych z co znajduje się w danych zarządzanych przez rozwiązanie Spark.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego platformy Spark

Zazwyczaj tworzenie aplikacji Spark Streaming lokalnie do pliku JAR, a następnie wdrożyć go na platformę Spark w HDInsight przez skopiowanie pliku JAR do domyślnego magazynu dołączone do klastra usługi HDInsight. Aplikację można uruchomić za pomocą interfejsów API REST usługi LIVY, które są dostępne z klastra przy użyciu operacji POST. Treść wpisu zawiera dokument JSON, który zawiera ścieżkę do pliku JAR, nazwa klasy, którego metoda główna definiuje i przesyłanie aplikacji, i opcjonalnie wymagań dotyczących zasobów zadania (np. liczba executors, pamięci i rdzeni) , a wszystkie ustawienia konfiguracji kodu aplikacji wymaga.

![Wdrażanie aplikacji usługi przesyłania strumieniowego platformy Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Można również sprawdzić stan wszystkich aplikacji, za pomocą żądanie GET względem punktu końcowego usługi LIVY. Na koniec możesz zakończyć uruchomionej aplikacji, wysyłając żądanie DELETE względem punktu końcowego usługi LIVY. Szczegółowe informacje na temat interfejsu API usługi LIVY, [zdalnej obsługi zadań przy użyciu usługi LIVY Apache](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Przewodnik programowania w przesyłania strumieniowego platformy Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Uruchamiać zadania Apache Spark zdalnie za pomocą usługi LIVY Apache](apache-spark-livy-rest-interface.md)
