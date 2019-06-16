---
title: Tworzenie zadań przesyłania strumieniowego platformy Spark o wysokiej dostępności w YARN — Azure HDInsight
description: Jak skonfigurować przesyłania strumieniowego platformy Spark dla scenariusza wysokiej dostępności.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 79a36ad39284dc66467ba7c500a363668f78b893
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720660"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Tworzenie zadania Apache Spark Streaming wysokiej dostępności przy użyciu usługi YARN

[Platforma Apache Spark](https://spark.apache.org/) przesyłania strumieniowego umożliwia wdrożenie skalowalne o wysokiej przepływności, odpornej na uszkodzenia aplikacji dla strumieni danych, przetwarzanie. Można połączyć aplikacje przesyłania strumieniowego platformy Spark w klastrze HDInsight Spark do różnych źródeł danych, takich jak usługi Azure Event Hubs, Azure IoT Hub, [platformy Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ ZeroMQ](http://zeromq.org/), pierwotne gniazdami TCP, lub przez monitorowanie [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) systemu plików dla zmian. Przesyłanie strumieniowe Spark obsługuje odporności na uszkodzenia z gwarancją, że danego zdarzenia jest przetwarzany tylko raz, nawet w przypadku awarii węzła.

Przesyłanie strumieniowe Spark tworzy długotrwałych zadań, podczas których będą mogli zastosować przekształceń danych, a następnie Wypchnij wyniki, na systemy plików, baz danych, pulpitów nawigacyjnych i konsoli. Przesyłanie strumieniowe Spark przetwarza micro partie danych, zbierając pierwszej partii zdarzeń za pośrednictwem określonego przedziału czasowego. Następnie tej partii jest wysyłany na potrzeby przetwarzania i danych wyjściowych. Przedziały czasu usługi Batch są zazwyczaj definiowane w ułamków sekund.

![Przesyłania strumieniowego platformy Spark](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Przesyłanie strumieniowe Spark reprezentuje ciągłego strumienia danych przy użyciu *zdyskretyzowany strumienia* (DStream). Można utworzyć tego DStream ze źródeł wejściowych, takich jak Event Hubs i Kafka lub zastosowania przekształcenia w innym DStream. Po odebraniu zdarzenia aplikacji Spark Streaming, zdarzenia są przechowywane w niezawodny sposób. Oznacza to, że dane zdarzenia są replikowane tak, aby wiele węzłów ma jego kopię. Daje to gwarancję, że błąd dowolnego pojedynczego węzła nie spowoduje utraty zdarzenia.

Używa Spark core *odporne rozproszone zestawy danych* (danych). Danych dystrybuować dane w wielu węzłach w klastrze, w którym każdy węzeł zazwyczaj zajmuje się jego danych całkowicie wewnątrzpamięciowej w celu uzyskania najlepszej wydajności. Każdy RDD reprezentuje zdarzenia zbierane wraz z upływem czasu dla partii. Po upływie czasu dla partii, przesyłanie strumieniowe Spark tworzy nowy RDD, zawierającą wszystkie dane w tym zakresie. Ta ciągłego zestawu danych są zbierane w DStream. Aplikacja usługi Spark Streaming przetwarza danych przechowywanych w każdej partii RDD.

![DStream platformy Spark](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Przesyłanie strumieniowe ze strukturą platformy Spark zadania

Przesyłanie strumieniowe ze strukturą platformy Spark została wprowadzona w Spark 2.0 jako aparat analityczny do użytku na strumieniu danych ze strukturą. Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z SparkSQL dzielenia na partie aparatu interfejsów API. Podobnie jak w przypadku przesyłania strumieniowego platformy Spark, przesyłanie strumieniowe ze strukturą platformy Spark uruchamia jego obliczenia za pośrednictwem przychodzących stale micro partie danych. Przesyłanie strumieniowe ze strukturą platformy Spark przedstawia strumień danych jako tabeli danych wejściowych z wierszami, bez ograniczeń. Oznacza to w tabeli danych wejściowych w dalszym ciągu Rozwijaj nadejściu nowych danych. W tej tabeli danych wejściowych są stale przez nią przetwarzane wolno działające zapytanie, a wyniki są zapisywane do tabeli wyjściowej.

![Przesyłanie strumieniowe ze strukturą platformy Spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

W przesyłanie strumieniowe ze strukturą danych dociera do systemu i od razu są zbierane w tabeli danych wejściowych. Możesz pisać zapytania, które wykonują operacje względem tej tabeli danych wejściowych. Wynik kwerendy daje drugiej tabeli o nazwie tabeli wyników. Tabela wyników zawiera wyniki zapytania, z którego narysować dane do wysłania do zewnętrznego magazynu danych takie relacyjnej bazy danych. *Interwał wyzwalacza* ustawia chronometraż podczas przetwarzania danych z tabeli danych wejściowych. Domyślnie przesyłanie strumieniowe ze strukturą przetwarza dane zaraz po ich odebraniu. Jednak można również skonfigurować wyzwalacz, uruchom na dłuższy czas, aby dane przesyłane strumieniowo są przetwarzane w partiach oparte na czasie. Dane w tabeli wyników może być całkowicie odświeżona każdorazowo, ma nowych danych, aby obejmowała wszystkie dane wyjściowe od chwili rozpoczęcia przesyłania strumieniowego zapytania (*w trybie*), lub tylko może zawierać tylko dane, które jest nowy od czasu ostatniego czas przetwarzania zapytania (*trybie append*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Tworzenie zadań przesyłania strumieniowego platformy Spark odpornej na uszkodzenia

Aby utworzyć środowiska wysokiej dostępności dla zadań przesyłania strumieniowego platformy Spark, Rozpocznij od kodowania pojedynczych zadań odzyskiwania w przypadku awarii. Takie własnym przywracanej zadania są odporne na uszkodzenia.

Danych ma kilka właściwości, które pomagają wysoko dostępnej i odpornej na uszkodzenia zadania przesyłania strumieniowego platformy Spark:

* Partie danych wejściowych przechowywanych w danych jako DStream są automatycznie replikowane w pamięci dla odporności na uszkodzenia.
* Dane utracone z powodu awarii procesów roboczych można przeliczane z replikowanych danych wejściowych na różnych pracowników, tak długo, jak te węzły procesu roboczego są dostępne.
* Szybkie odzyskiwanie błędów może wystąpić w ciągu sekundy, ponieważ odzyskanie danych będzie błędy/wątpliwych się stanie, za pomocą obliczeń w pamięci.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Dokładnie-jednokrotnej semantyki z przesyłaniem strumieniowym Spark

Aby utworzyć aplikację, która przetwarza każde zdarzenie, gdy (i tylko jeden raz), należy wziąć pod uwagę jak wszystkie punkty awarii systemu, uruchom ponownie po wystąpił problem i jak można uniknąć utraty danych. Dokładnie — po semantyki wymagają, że żadne dane nie są tracone w dowolnym momencie i przetworzenia komunikatu jest ponownego uruchamiania, niezależnie od tego, gdzie występuje błąd. Zobacz [tworzenie Spark Streaming zadania przy użyciu dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Przesyłania strumieniowego platformy Spark i Apache Hadoop YARN

W HDInsight, klaster pracy są koordynowany przez *jeszcze inny moduł zasobów negocjowania* (YARN). Projektowanie wysokiej dostępności na potrzeby przesyłania strumieniowego platformy Spark obejmuje techniki, przesyłania strumieniowego platformy Spark i składników usługi YARN.  Poniżej przedstawiono przykładową konfigurację za pomocą usługi YARN. 

![Architektura usługi YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

W poniższych sekcjach opisano zagadnienia dotyczące projektowania dla tej konfiguracji.

### <a name="plan-for-failures"></a>Plan na wypadek awarii

Aby utworzyć konfigurację usługi YARN dla wysokiej dostępności, należy również zaplanować awaryjnego funkcji wykonawczej i sterownika. Niektóre zadania przesyłania strumieniowego platformy Spark jest również obejmować wymagania dotyczące gwarancji danych, które wymagają dodatkowej konfiguracji i instalacji. Na przykład przesyłania strumieniowego aplikacji może być wymaganiem biznesowym zero — — utratę danych gwarantuje pomimo wszelkie błędy występujące w hostingu przesyłania strumieniowego systemie lub w klastrze HDInsight.

Jeśli **wykonawca** kończy się niepowodzeniem, jego zadań i odbiorcy są automatycznie uruchamiany ponownie przez rozwiązanie Spark, dlatego nie jest wymagana żadna zmiana konfiguracji.

Jednak jeśli **sterownika** zakończy się niepowodzeniem, a wszystkie jego skojarzony executors się nie powieść, bloki wszystkich odebranych i dostęp do wyników obliczeń zostaną utracone. Aby wykonać odzyskiwanie po awarii sterowników, należy użyć *DStream tworzenie punktów kontrolnych* zgodnie z opisem w [tworzenie Spark Streaming zadania przy użyciu dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Tworzenie punktów kontrolnych DStream zapisuje okresowo *kierowane grafie acyklicznym* (DAG) programu DStreams w odpornej na uszkodzenia magazynie, takim jak Azure Storage.  Tworzenie punktów kontrolnych umożliwia Spark przesyłanie strumieniowe ze strukturą ponowne uruchomienie nie powiodło się sterownika z informacji dotyczących punktu kontrolnego.  Ponowne uruchomienie tego sterownika uruchamia nowy executors i powoduje także ponowne uruchomienie odbiorników.

Aby odzyskać sterowników DStream punkty kontrolne:

* Konfigurowanie sterownika automatyczne ponowne uruchomienie w ramach platformy YARN z ustawieniem konfiguracji `yarn.resourcemanager.am.max-attempts`.
* Ustaw katalog punktu kontrolnego w systemie plików zgodnego systemem plików HDFS z `streamingContext.checkpoint(hdfsDirectory)`.
* Restrukturyzacja kodu źródłowego, aby używać punktów kontrolnych do odzyskania, na przykład:

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

* Konfigurowanie odzyskiwania utraconych danych, należy włączyć dziennik zapisu z wyprzedzeniem (WAL) za pomocą `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`i Wyłącz replikację w pamięci dla danych wejściowych DStreams z `StorageLevel.MEMORY_AND_DISK_SER`.

Aby podsumować, korzystając z procesu tworzenia punktów kontrolnych, WAL + odbiorniki niezawodne, będzie mogła dostarczać "co najmniej raz" Odzyskiwanie danych:

* Dokładnie jednokrotne, tak długo, jak odebrane dane nie zostaną utracone i dane wyjściowe są idempotentne albo lub transakcyjnych.
* Dokładnie jeden raz za pomocą nowego podejścia platformy Kafka bezpośrednie który korzysta z platformy Kafka jako replikowanych dziennika, a nie przy użyciu odbiorców lub WALs.

### <a name="typical-concerns-for-high-availability"></a>Typowe problemy, wysokiej dostępności

* Jest trudniejsze do monitorowania zadań przesyłania strumieniowego niż zadania usługi batch. Zadania przesyłania strumieniowego platformy Spark są zazwyczaj długotrwałych i usługi YARN nie agregować dzienniki, aż do zakończenia zadania.  Spark punkty kontrolne zostaną utracone podczas uaktualnienia platformy Spark i aplikacji, a następnie należy wyczyścić katalog punktu kontrolnego podczas uaktualniania.

* Konfigurowanie usługi trybu klastra YARN do uruchomienia sterowniki, nawet jeśli klient nie powiedzie się. Aby skonfigurować automatyczne ponowne uruchomienie dla sterowników:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Platforma Spark i interfejs użytkownika przesyłania strumieniowego platformy Spark jest system można konfigurować metryki. Można również użyć dodatkowych bibliotek, takich jak grafitu/Grafana, można pobrać pulpitu nawigacyjnego metryki, takie jak "przetworzonych rekordów num", "Użycie pamięci/GC w sterowników i", "opóźnienie całkowitej", "wykorzystanie klastra" itd. W przesyłanie strumieniowe ze strukturą w wersji 2.1 lub większą, można użyć `StreamingQueryListener` zebrać dodatkowe metryki.

* Należy podzielić długotrwałych zadań.  Po przesłaniu aplikacją przesyłania strumieniowego platformy Spark w klastrze muszą być zdefiniowane kolejki YARN, w których zadanie zostanie uruchomione. Możesz użyć [Planisty wydajności usługi YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) do przesyłania zadań długotrwałych do oddzielnych kolejek.

* Zamknięcie aplikacji przesyłania strumieniowego. Jeśli Twoje przesunięcia, są znane i stan wszystkich aplikacji są przechowywane zewnętrznie, można programowo zatrzymać aplikację przesyłania strumieniowego w odpowiednim miejscu. Jedna z technik jest użycie, "wątek punkty zaczepienia" platformy Spark, sprawdzając zewnętrznych flagi co *n* sekund. Można również użyć *pliku znacznika* , jest tworzone w systemie HDFS, podczas uruchamiania aplikacji, a następnie usuwane, gdy chcesz zatrzymać. Podejścia pliku znaczników należy użyć oddzielnego wątku w aplikacji platformy Spark, który wywołuje kod podobny do następującego:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie przesyłania strumieniowego platformy Apache Spark](apache-spark-streaming-overview.md)
* [Utwórz Apache Spark Streaming zadań z dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md)
* [Długotrwałe Apache Spark zadania przesyłania strumieniowego na podstawie usługi YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Przesyłanie strumieniowe ze strukturą: Semantyka odpornego na błędy błędów](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Zdyskretyzowany strumieni: Odporne na uszkodzenia modelu przetwarzania skalowalne Stream](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
