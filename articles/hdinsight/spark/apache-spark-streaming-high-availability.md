---
title: Zadania przesyłania strumieniowego platformy Spark o wysokiej dostępności w YARN — Azure HDInsight
description: Jak skonfigurować apache Spark Streaming dla scenariusza wysokiej dostępności w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807157"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Tworzenie ofert pracy Apache Spark Streaming o wysokiej dostępności dzięki funkcji YARN

[Apokaz Iskra](https://spark.apache.org/) Przesyłanie strumieniowe umożliwia implementowanie skalowalnych, wysokowydajnych, odpornych na uszkodzenia aplikacji do przetwarzania strumieni danych. Aplikacje spark streaming można łączyć w klastrze platformy SPARK usługi HDInsight z różnymi źródłami danych, takimi jak usługi Azure Event Hubs, Usługa Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), surowe gniazda TCP lub monitorowanie systemu plików [APACHE Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) pod kątem zmian. Usługa Spark Streaming obsługuje odporność na uszkodzenia, gwarantując, że dane zdarzenie jest przetwarzane dokładnie raz, nawet w przypadku awarii węzła.

Usługa Spark Streaming tworzy długotrwałe zadania, podczas których można zastosować przekształcenia do danych, a następnie wypchnąć wyniki do systemów plików, baz danych, pulpitów nawigacyjnych i konsoli. Spark Streaming przetwarza mikropartes danych, najpierw zbierając partię zdarzeń w określonym przedziale czasu. Następnie ta partia jest wysyłana do przetwarzania i danych wyjściowych. Interwały czasu partii są zazwyczaj definiowane w ułamkach sekundy.

![Strumieniowanie iskrą](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>Strumienie D

Spark Streaming reprezentuje ciągły strumień danych przy użyciu *strumienia discretized* (DStream). Ten DStream można utworzyć ze źródeł wejściowych, takich jak Centra zdarzeń lub Kafka lub stosując przekształcenia na innym DStream. Gdy zdarzenie dociera do aplikacji Spark Streaming, zdarzenie jest przechowywane w niezawodny sposób. Oznacza to, że dane zdarzenia są replikowane tak, że wiele węzłów mają kopię. Gwarantuje to, że awaria dowolnego pojedynczego węzła nie spowoduje utraty zdarzenia.

Rdzeń platformy Spark używa *odpornych rozproszonych zestawów danych* (RDD). RdD rozprowadzają dane w wielu węzłach w klastrze, gdzie każdy węzeł zazwyczaj przechowuje swoje dane całkowicie w pamięci, aby uzyskać najlepszą wydajność. Każdy RDD reprezentuje zdarzenia zebrane w interwale partii. Po upływie interwału wsadowego, Spark Streaming tworzy nowy RDD zawierający wszystkie dane w tym przedziale. Ten ciągły zestaw RDD są zbierane do DStream. Aplikacja Spark Streaming przetwarza dane przechowywane w rdd każdej partii.

![Strumień iskry DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Zadania przesyłania strumieniowego strukturyzowane platformy Spark

Spark Structured Streaming został wprowadzony w spark 2.0 jako aparat analityczny do użytku na przesyłaniu strumieniowego danych strukturalnych. Spark Structured Streaming używa interfejsów API aparatu wsadowego SparkSQL. Podobnie jak w przypadku usługi Spark Streaming, usługa Spark Structured Streaming uruchamia swoje obliczenia w przypadku stale przybywających mikropartii danych. Spark Structured Streaming reprezentuje strumień danych jako tabela wprowadzania z nieograniczoną liczbę wierszy. Oznacza to, że tabela wprowadzania nadal rośnie w miarę pojawiania się nowych danych. Ta tabela danych wejściowych jest stale przetwarzana przez długo działającą kwerendę, a wyniki są zapisywane w tabeli wyjściowej.

![Strumieniowanie strukturalne Spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

W ustrukturyzowanym strumieniowaniu dane docierają do systemu i są natychmiast pozyskiwania do tabeli wprowadzania. Piszesz kwerendy, które wykonują operacje względem tej tabeli wprowadzania. Dane wyjściowe kwerendy daje inną tabelę, o nazwie Tabela wyników. Tabela wyników zawiera wyniki kwerendy, z której można rysować dane do wysłania do zewnętrznego magazynu danych, takiej relacyjnej bazy danych. *Interwał wyzwalacza* ustawia czas, kiedy dane są przetwarzane z tabeli wprowadzania. Domyślnie usługa Structured Streaming przetwarza dane natychmiast po ich odebraniu. Można jednak również skonfigurować wyzwalacz do uruchamiania w dłuższym odstępie czasu, więc dane przesyłania strumieniowego są przetwarzane w partiach opartych na czasie. Dane w tabeli wyników mogą być odświeżane za każdym razem, gdy pojawią się nowe dane, tak aby zawierały wszystkie dane wyjściowe od rozpoczęcia kwerendy strumieniowej *(tryb kompletny)* lub mogą zawierać tylko te dane, które są nowe od czasu ostatniego przetworzenia kwerendy *(tryb dołączania).*

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Tworzenie zadań przesyłania strumieniowego iskrą odpornych na uszkodzenia

Aby utworzyć środowisko o wysokiej dostępności dla zadań spark streaming, zacznij od kodowania poszczególnych zadań do odzyskiwania w przypadku awarii. Takie zadania samonapędzajają się są odporne na uszkodzenia.

RdD mają kilka właściwości, które pomagają wysokiej dostępności i odporne na uszkodzenia Spark Streaming zadania:

* Partie danych wejściowych przechowywanych w rdds jako DStream są automatycznie replikowane w pamięci dla odporności na uszkodzenia.
* Dane utracone z powodu awarii procesu roboczego można ponownie skompentować z replikowanych danych wejściowych dla różnych pracowników, o ile te węzły procesu roboczego są dostępne.
* Szybkie odzyskiwanie błędów może wystąpić w ciągu jednej sekundy, ponieważ odzyskiwanie z usterek/stragglers odbywa się za pomocą obliczeń w pamięci.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Dokładnie raz semantyka z Spark Streaming

Aby utworzyć aplikację, która przetwarza każde zdarzenie raz (i tylko raz), należy wziąć pod uwagę, jak wszystkie punkty systemowe awarii ponownie po problemie i jak można uniknąć utraty danych. Dokładnie raz semantyka wymaga, aby żadne dane nie zostały utracone w dowolnym momencie, a przetwarzanie wiadomości można ponownie uruchomić, niezależnie od tego, gdzie występuje błąd. Zobacz [Tworzenie zadań przesyłania strumieniowego platformy Spark z dokładnie raz przetwarzania zdarzeń](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming i Apache Hadoop YARN

W HDInsight praca klastra jest koordynowana przez *negocjatora yet another resource* (YARN). Projektowanie wysokiej dostępności dla spark streaming obejmuje techniki spark streaming, a także dla składników YARN.  Przykładowa konfiguracja przy użyciu YARN jest pokazana poniżej.

![Architektura przędzy](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

W poniższych sekcjach opisano zagadnienia dotyczące projektu dla tej konfiguracji.

### <a name="plan-for-failures"></a>Planowanie awarii

Aby utworzyć konfigurację YARN dla wysokiej dostępności, należy zaplanować ewentualne niepowodzenie wykonawcy lub sterownika. Niektóre zadania przesyłania strumieniowego platformy Spark zawierają również wymagania dotyczące gwarancji danych, które wymagają dodatkowej konfiguracji i konfiguracji. Na przykład aplikacja przesyłania strumieniowego może mieć wymagania biznesowe dotyczące gwarancji utraty danych bez danych pomimo błędu występującego w systemie przesyłania strumieniowego hostingu lub klastrze HDInsight.

Jeśli **wykonawca** ulegnie awarii, jego zadania i odbiorniki są automatycznie uruchamiane przez program Spark, więc nie jest wymagana żadna zmiana konfiguracji.

Jednak jeśli **sterownik** ulegnie awarii, wszystkie skojarzone z nim executory nie powiodą się, a wszystkie odebrane bloki i wyniki obliczeń zostaną utracone. Aby odzyskać dane po awarii sterownika, należy użyć *punktu kontrolnego DStream* zgodnie z opisem w [twórz zadania przesyłania strumieniowego platformy Spark z dokładnie raz przetwarzającym zdarzenie.](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers) DStream punktów kontrolnych okresowo zapisuje *skierowane wykres acykliczny* (DAG) DStreams do magazynu odpornego na uszkodzenia, takich jak Usługa Azure Storage.  Punkt kontrolny umożliwia spark Structured Streaming, aby ponownie uruchomić sterownik nie powiodło się z informacji o punkcie kontrolnym.  Ten sterownik uruchom ponownie nowe executory, a także uruchamia ponownie odbiorniki.

Aby odzyskać sterowniki za pomocą punktu kontrolnego DStream:

* Skonfiguruj automatyczne ponowne uruchomienie sterownika na YARN z ustawieniem `yarn.resourcemanager.am.max-attempts`konfiguracji .
* Ustaw katalog punktów kontrolnych w systemie plików `streamingContext.checkpoint(hdfsDirectory)`zgodnym z systemem plików zgodnym z systemem HDFS za pomocą pliku .
* Zrestrukturyzuj kod źródłowy, aby używać punktów kontrolnych do odzyskiwania, na przykład:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Skonfiguruj odzyskiwanie utraconych danych, włączając `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`dziennik zapisu z wyprzedzeniem (WAL) `StorageLevel.MEMORY_AND_DISK_SER`za pomocą programu , i wyłącz replikację w pamięci wejściowej dstreams z .

Podsumowując, za pomocą punktów kontrolnych + WAL + niezawodnych odbiorników, będziesz w stanie dostarczyć "co najmniej raz" odzyskiwanie danych:

* Dokładnie raz, tak długo, jak odebrane dane nie zostaną utracone, a dane wyjściowe są idempotentne lub transakcyjne.
* Dokładnie raz, z nowym podejściem Kafka Direct, który używa platformy Kafka jako dziennika replikowanego, a nie przy użyciu odbiorników lub list WALs.

### <a name="typical-concerns-for-high-availability"></a>Typowe obawy dotyczące wysokiej dostępności

* Jest trudniejsze do monitorowania zadań przesyłania strumieniowego niż zadania wsadowe. Zadania przesyłania strumieniowego platformy Spark są zazwyczaj długotrwałe, a YARN nie agreguje dzienników, dopóki zadanie nie zakończy się.  Punkty kontrolne platformy Spark zostaną utracone podczas uaktualniania aplikacji lub platformy Spark i należy wyczyścić katalog punktów kontrolnych podczas uaktualniania.

* Skonfiguruj tryb klastra YARN, aby uruchamiał sterowniki, nawet jeśli klient ulegnie awarii. Aby skonfigurować automatyczne ponowne uruchamianie sterowników:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark i Spark Streaming UI mają konfigurowalny system metryk. Można również użyć dodatkowych bibliotek, takich jak Graphite/Grafana, aby pobrać metryki pulpitu nawigacyjnego, takie jak "num records processed", "memory/GC usage on driver & executors", "total delay", "utilization of the cluster" itd. W ustrukturyzowanej wersji przesyłania strumieniowego w `StreamingQueryListener` wersji 2.1 lub większej można użyć do zbierania dodatkowych danych.

* Należy podzielić na segmenty długotrwałych zadań.  Gdy aplikacja spark streaming jest przesyłany do klastra, kolejka YARN, w której jest uruchamiane zadanie, musi zostać zdefiniowana. Harmonogram zdolności [produkcyjnych YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) służy do przesyłania długotrwałych zadań do oddzielnych kolejek.

* Bezpiecznie zamknij aplikację do przesyłania strumieniowego. Jeśli przesunięcia są znane, a cały stan aplikacji jest przechowywany zewnętrznie, można programowo zatrzymać aplikację przesyłania strumieniowego w odpowiednim miejscu. Jedną z technik jest użycie "haków nici" w Spark, sprawdzając, czy flaga zewnętrzna co *n* sekund. Można również użyć *pliku znacznika,* który jest tworzony w systemie plików HDFS podczas uruchamiania aplikacji, a następnie usuwany, gdy chcesz zatrzymać. W przypadku podejścia do pliku znacznika należy użyć oddzielnego wątku w aplikacji Platformy Spark, który wywołuje kod podobny do tego:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Następne kroki

* [Apache Spark Streaming — omówienie](apache-spark-streaming-overview.md)
* [Tworzenie zadań przesyłania strumieniowego platformy Apache Spark z dokładnie raz przetwarzając zdarzenia](apache-spark-streaming-exactly-once.md)
* [Długotrwałe Apache Spark Streaming Praca na YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Ustrukturyzowane przesyłanie strumieniowe: Semantyka odporna na uszkodzenia](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Discretized Strumienie: Model odporny na uszkodzenia do skalowalnych przetwarzania strumienia](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
