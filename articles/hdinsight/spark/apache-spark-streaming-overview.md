---
title: Przesyłania strumieniowego w usłudze Azure HDInsight Spark | Dokumentacja firmy Microsoft
description: Jak używać aplikacji przesyłania strumieniowego Spark w klastrach HDInsight Spark.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 2f521df81e5153affa95248cda2aa001bc5d6484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="overview-of-spark-streaming"></a>Omówienie przesyłania strumieniowego Spark

Przesyłanie strumieniowe Spark umożliwia przetwarzanie strumienia danych Spark w usłudze HDInsight klastrów z gwarancją, że wszystkie zdarzenia wejściowe jest przetwarzany tylko raz, nawet jeśli wystąpi awaria węzła. Strumień Spark jest długotrwałe zadanie, które otrzymuje danych wejściowych z różnych źródeł, takich jak Azure Event Hubs, Centrum IoT Azure, Kafka, Flume, Twitter, ZeroMQ, gniazd TCP lub monitorowania systemu plików HDFS systemy plików. W przeciwieństwie do procesu wyłącznie zdarzeniami strumienia Spark partii danych wejściowych do uruchomienia systemu windows, takich jak wycinek 2 sekundy, a następnie przekształca poszczególnych partii danych przy użyciu mapy, Zmniejsz join i wyodrębnianie działań. Strumień Spark następnie zapisuje przekształcone dane systemy plików, baz danych, pulpity nawigacyjne i konsoli.

![Strumień przetwarzania za pomocą usługi HDInsight i przesyłania strumieniowego Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Przesyłanie strumieniowe Spark aplikacji należy poczekać ułamek sekund zbierać poszczególnych *partii micro* zdarzeń przed wysłaniem tej partii potrzeby przetwarzania. Z kolei aplikacji sterowane zdarzeniami przetwarza każdego zdarzenia natychmiast. Opóźnienia przesyłania strumieniowego Spark jest zazwyczaj w ciągu kilku sekund. Zalety podejście micro partii są bardziej wydajne przetwarzania danych i obliczeń agregujących łatwiejsze.

## <a name="introducing-the-dstream"></a>Wprowadzenie do DStream

Przesyłanie strumieniowe Spark reprezentuje ciągłego strumienia przychodzącego danych przy użyciu *zdyskretyzowany strumienia* o nazwie DStream. DStream mogą być tworzone z źródeł danych wejściowych, takich jak usługi Event Hubs lub Kafka lub przy zastosowaniu przekształcenia w innym DStream.

DStream zapewnia warstwę abstrakcji nałożoną dane pierwotne zdarzenia. 

Rozpoczynać pojedyncze zdarzenie, co oznacza temperatury odczytu z połączonych termostat. Jeśli to zdarzenie dociera do lokalizacji aplikacji przesyłania strumieniowego Spark, zdarzenie jest przechowywany w niezawodny sposób, w których są replikowane w wielu węzłach. To tolerancji gwarantuje, że awarii dowolnego pojedynczego węzła nie spowoduje utratę wydarzenia. Spark core wykorzystuje dystrybuująca dane w wielu węzłach w klastrze, struktura danych, w którym każdy węzeł zazwyczaj zajmuje własnych danych w pamięci najlepszą wydajność. Ta struktura danych jest wywoływana *odporność rozproszonej zestawu danych* (RDD).

Każdy RDD reprezentuje zdarzenia zebrane przez przedział czasu zdefiniowany przez użytkownika o nazwie *Interwał partii*. Zgodnie z każdej partii ciągu określonego czasu, jest generowany nowy RDD zawierający wszystkie dane z tego interwału. Ciągłego zestawu RDDs są zbierane w DStream. Na przykład jeśli interwał partii jest długa jednej sekundy, DStream Twojego emituje partii co drugi zawierający RDD jedną, z danymi pozyskanych w ciągu sekundy tego. Podczas przetwarzania DStream, zdarzenie temperatury występuje w jednym z tych partii. Do przesyłania strumieniowego Spark aplikacji przetwarza partie, które zawierają zdarzenia i ostatecznie działania w przypadku danych przechowywanych w każdej RDD.

![Przykład DStream ze zdarzeniami temperatury ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikacji Spark przesyłania strumieniowego

Przesyłanie strumieniowe Spark aplikacja to długotrwałe odbiera dane ze źródeł pozyskiwania, stosuje przekształcenia do przetwarzania danych, a następnie wypycha dane do jednego lub więcej miejsc docelowych. Struktura aplikacji Spark przesyłania strumieniowego ma części statycznych i dynamicznych. Definiuje statycznej części, gdzie dane pochodzą z, jakie operacje na danych, a powinien pojawiać się wyniki. Części dynamicznej jest uruchomiona aplikacja, oczekiwanie na sygnał zatrzymania.

Na przykład następujące prostą aplikację odbiera wiersza tekstu za gniazda TCP i oblicza liczbę razy, zostanie wyświetlony każdego wyrazu.

### <a name="define-the-application"></a>Definiowanie aplikacji

Definicja aplikacji logiki obejmuje cztery kroki:

1. Utwórz StreamingContext.
2. Utwórz DStream StreamingContext.
3. Przekształcane DStream.
4. Zapisuje wyniki.

Ta definicja jest statyczny, a nie dane są przetwarzane do czasu uruchomienia aplikacji.

#### <a name="create-a-streamingcontext"></a>Utwórz StreamingContext

Utwórz StreamingContext SparkContext, wskazujące do klastra. Podczas tworzenia StreamingContext, należy określić rozmiar partii w sekundach, na przykład:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Utwórz DStream

Wystąpienie StreamingContext tworzenie wejściowych DStream dla źródła danych wejściowych. W takim przypadku aplikacja obserwuje wyglądu nowe pliki w domyślny magazyn dołączony do klastra usługi HDInsight.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Zastosuj przekształcenia

Zaimplementowaniem przetwarzania przez stosowanie przekształceń na DStream. Ta aplikacja odbiera jednego wiersza tekstu w czasie z pliku, dzieli każdy wiersz na słowa i następnie korzysta ze wzorca map-reduce, aby określić liczbę razy, zostanie wyświetlony każdego wyrazu.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Wynik

Przekładanie wyniki transformacji do docelowe systemy, stosując operacji wyjścia. W takim przypadku wynik każdego uruchomienia obliczenia jest drukowany w danych wyjściowych konsoli.

    wordCounts.print()

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom przesyłania strumieniowego aplikacji i uruchomić do momentu otrzymania sygnału zakończenia.

    ssc.start()            
    ssc.awaitTermination()

Szczegółowe informacje na temat Spark strumienia interfejsu API, wraz ze źródła zdarzeń, transformacji i operacji wyjścia, obsługuje on [przewodnik programowania w języku przesyłania strumieniowego Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Następujące przykładowej aplikacji jest autonomiczna, więc można uruchomić go w programie [notesu Jupyter](apache-spark-jupyter-notebook-kernels.md). W tym przykładzie tworzy źródło danych testowych w klasie DummySource, który wyświetla wartość licznika oraz bieżący czas w milisekundach co pięć sekund. Nowy obiekt StreamingContext ma przedział partii 30 sekund. Za każdym razem, gdy partii jest tworzony, przesyłania strumieniowego aplikacji sprawdza RDD utworzone, konwertuje RDD Spark DataFrame i tworzy tabeli tymczasowej w DataFrame.

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

Można następnie zapytań DataFrame okresowo, aby wyświetlić bieżący zestaw wartości istnieje w partii, na przykład przy użyciu tej kwerendy SQL:

    %%sql
    SELECT * FROM demo_numbers

Dane wyjściowe wygląda następująco:

| wartość | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Brak sześciu wartości, ponieważ DummySource tworzy wartość 5 sekund, a aplikacja emituje partii co 30 sekund.

## <a name="sliding-windows"></a>Przedłużanie systemu windows

Do przeprowadzenia obliczeń agregacji w Twojej DStream w niektórych przedziale czasu, na przykład w celu pobrania średnia temperatura w ciągu ostatnich dwóch sekund, można użyć *przedłużanie okna* operacje dołączone do przesyłania strumieniowego Spark. Metodzie przesuwanego okna ma czas trwania (długość okna) i interwał, w którym zawartość okna jest oceniane (interwał slajdów).

Przedłużanie systemu windows mogą nakładać się na, na przykład można zdefiniować okno o długości dwusekundowe, slajdów każdej sekundzie. Oznacza to, za każdym razem, gdy obliczanie agregacji, w oknie zostaną wyświetlone dane z ciągu ostatniej sekundy poprzedniego okna, a także żadnych nowych danych w ciągu następnej sekundy jeden.

![Przykład okna ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Przykład okna ze zdarzeniami temperatury po przedłużanie](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Poniższy przykład aktualizuje kod, który używa DummySource, w celu zbierania partii do okna z duration co minutę i slajdów co minutę.

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

Po pierwszym minutę Brak wpisów 12 - sześć wpisów z każdej partii dwóch zbierane w oknie.

| wartość | time |
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

Metodzie przesuwanego okna funkcji dostępnych w interfejsie API przesyłania strumieniowego Spark obejmują okna, countByWindow reduceByWindow i countByValueAndWindow. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [przekształcenia DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

Aby zapewnić odporność i odporność na uszkodzenia, przesyłanie strumieniowe Spark polega na tworzenie punktów kontrolnych, aby upewnić się, że można kontynuować nieprzerwaną, nawet w wypadku awarii węzła przetwarzania strumienia. W usłudze HDInsight Spark tworzy punkty kontrolne do magazynu trwałego (magazynu Azure lub usługi Data Lake Store). Te punkty kontrolne przechowują metadane dotyczące przesyłania strumieniowego aplikacji, takie jak konfiguracji, zdefiniowany przez aplikację i wszystkie instancje, które zostały umieszczone w kolejce, ale jeszcze nie przetworzonych operacji. W niektórych przypadkach punkty kontrolne zostaną także zapisywania danych w RDDs szybciej odbudować stanu danych z co znajduje się w RDDs zarządza Spark.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji Spark przesyłania strumieniowego

Zazwyczaj tworzenie aplikacji Spark przesyłania strumieniowego lokalnie do pliku JAR, a następnie wdrożyć Spark w usłudze HDInsight, kopiując plik JAR do magazynu domyślnego powiązany z klastrem usługi HDInsight. Aplikację można uruchomić z interfejsów API REST LIVY, które są dostępne z klastrem przy użyciu operacji POST. Treść wpisu zawiera dokument JSON, który określa ścieżkę do Twojej JAR nazwę klasy, którego metoda główna definiuje i uruchamia przesyłania strumieniowego aplikacji oraz opcjonalnie wymagań dotyczących zasobów, zadania (na przykład liczba modułów, pamięci i rdzenie) , a wszystkie ustawienia konfiguracji kodu aplikacji wymaga.

![Wdrażanie aplikacji Spark przesyłania strumieniowego](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Można również sprawdzić stan wszystkich aplikacji z żądanie GET względem punktu końcowego programu LIVY. Na koniec może prowadzić działającej aplikacji, wysyłając żądanie usunięcia, względem punktu końcowego programu LIVY. Aby uzyskać więcej informacji na temat interfejsu API programu LIVY, zobacz [zdalnego zadań z programu LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie klastra Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Przewodnik programowania w języku przesyłania strumieniowego Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Uruchamianie zadań Spark zdalnie za pomocą programu LIVY](apache-spark-livy-rest-interface.md)
