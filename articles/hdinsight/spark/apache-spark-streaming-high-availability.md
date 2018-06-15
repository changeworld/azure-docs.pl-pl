---
title: Utwórz zadania przesyłania strumieniowego Spark wysokiej dostępności w YARN - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak skonfigurować przesyłania strumieniowego Spark dla scenariusza wysokiej dostępności.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: bbb4da02cbe4b0685c715c4cd6bd7b15c6b5cce0
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165319"
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Tworzenie zadań przesyłania strumieniowego Spark wysokiej dostępności z YARN

Przesyłanie strumieniowe Spark umożliwia wdrożenie skalowalne, wysokiej przepustowości, odpornej na uszkodzenia aplikacje do przetwarzania strumieni danych. Przesyłanie strumieniowe Spark aplikacji w klastrze Spark w usłudze HDInsight można nawiązać z różnych źródeł danych, takich jak Azure Event Hubs, Centrum IoT Azure, Kafka, Flume, Twitter, ZeroMQ, raw gniazda TCP lub monitorowania systemu plików HDFS zmiany. Przesyłanie strumieniowe Spark obsługuje odporność na uszkodzenia z gwarancją, że wszystkie z określonych zdarzeń jest przetwarzany tylko raz, nawet w przypadku awarii węzła.

Przesyłanie strumieniowe Spark tworzy długotrwałe zadania, w których będą mogli stosować przekształcenia danych, a następnie Wypchnij wyniki systemy plików baz danych, pulpity nawigacyjne i konsoli. Przesyłanie strumieniowe Spark przetwarza micro partii danych, przez pierwszy zbieranie partii zdarzeń w ustalonym czasie. Następnie tej partii jest wysyłany na przetwarzanie i danych wyjściowych. Przedziały czasu usługi partia zadań są zazwyczaj definiowane w części sekundy.

![Przesyłanie strumieniowe Spark](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Przesyłanie strumieniowe Spark stanowi ciągły strumień danych przy użyciu *zdyskretyzowany strumienia* (DStream). Ten DStream mogą być tworzone z źródeł danych wejściowych, takich jak usługi Event Hubs lub Kafka lub przy zastosowaniu przekształcenia w innym DStream. Po odebraniu zdarzenia aplikacji przesyłania strumieniowego Spark, zdarzenia są przechowywane w niezawodny sposób. Oznacza to dzięki czemu wiele węzłów ma kopię są replikowane dane zdarzeń. Dzięki temu, że błąd dowolnego pojedynczego węzła nie spowoduje utratę wydarzenia.

Używa Spark core *odporność rozproszonych zestawów danych* (RDDs). RDDs dystrybucji danych w wielu węzłach w klastrze, w którym każdy węzeł zazwyczaj przechowuje dane całkowicie w pamięci najlepszą wydajność. Każdy RDD reprezentuje zdarzenia zebrane przedziałach partii. Po upływie interwału partii, przesyłanie strumieniowe Spark tworzy nowy RDD, zawierającą wszystkie dane w tym zakresie. Ten zestaw ciągłego RDDs są zbierane w DStream. Aplikacja usługi przesyłania strumieniowego Spark przetwarza danych przechowywanych w każdej partii RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Strukturalne przesyłania strumieniowego Spark zadania

Strukturalne przesyłania strumieniowego Spark został wprowadzony w Spark 2.0 jako analityczne aparatu do użycia na przesyłanie strumieniowe danych strukturalnych. Strukturalne przesyłania strumieniowego Spark używa SparkSQL przetwarzanie wsadowe aparat interfejsów API. Podobnie jak w przypadku przesyłania strumieniowego Spark, strukturalnych przesyłania strumieniowego Spark uruchamia jego obliczenia za pośrednictwem stale odbieranych micro partie danych. Strukturalne przesyłania strumieniowego Spark reprezentuje strumienia danych w tabeli danych wejściowych z nieograniczoną liczbę wierszy. Oznacza to, że w tabeli danych wejściowych nadal będzie bardziej powszechne po odebraniu nowych danych. W tej tabeli danych wejściowych stale jest przetwarzany przez zapytanie długotrwałej, a wyniki są zapisywane w tabeli wyników.

![Struktura przesyłania strumieniowego Spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

W strukturze przesyłania strumieniowego dane dociera do systemu i jest natychmiast pozyskanych w tabeli danych wejściowych. Możesz pisać zapytania, które wykonują operacje względem tej tabeli danych wejściowych. Wynik kwerendy daje innej tabeli o nazwie tabeli wyników. W tabeli wyników zawiera wyniki zapytania, z którego rysowania do wysłania dane do magazynu zewnętrznego danych takie relacyjnej bazy danych. *Interwał wyzwalacza* ustawia chronometraż przetworzenia danych z tabeli danych wejściowych. Domyślnie strukturalnych przesyłania strumieniowego przetwarza dane zaraz po odebraniu. Jednak można również skonfigurować wyzwalacza, uruchom na dłuższy czas, aby dane przesyłane strumieniowo są przetwarzane w partiach oparte na czasie. Dane w tabeli wyników mogą być całkowicie odświeżyć zawsze istnieje nowe dane, co zawierają one wszystkich danych wyjściowych od chwili rozpoczęcia przesyłania strumieniowego kwerendy (*tryb pełny*), lub tylko może zawierać tylko dane, które jest nowy od czasu ostatniego czas przetwarzania zapytania (*tryb dołączania*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Tworzenie zadań przesyłania strumieniowego Spark odpornej na uszkodzenia

Aby utworzyć środowisko o wysokiej dostępności dla zadań przesyłania strumieniowego Spark, Rozpocznij od kodowanie poszczególnych zadań odzyskiwania w przypadku awarii. Takie zadania własnym przywracanej są odpornej na uszkodzenia.

RDDs ma kilka właściwości, które pomagają wysokiej dostępności i odporności na uszkodzenia zadania przesyłania strumieniowego Spark:

* Partie dane wejściowe, przechowywane w RDDs jako DStream są automatycznie replikowane w pamięci odporność na uszkodzenia.
* Dane utracone z powodu awarii procesu roboczego można przeliczane z zreplikowanych danych wejściowych z różnych pracowników, tak długo, jak te węzłów procesu roboczego są dostępne.
* Szybkie odzyskiwanie błędów mogą występować w ciągu sekundy, odzyskiwanie z błędów/wątpliwych przebiega za pośrednictwem obliczeń w pamięci.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Dokładnie — raz semantyki z przesyłania strumieniowego Spark

Aby utworzyć aplikację, która przetwarza każdego zdarzenia raz (i tylko jeden raz), należy wziąć pod uwagę jak wszystkich punktów awarii systemu, uruchom ponownie po problemu i jak można uniknąć utraty danych. Dokładnie — po semantyki wymagać, aby nie zostały utracone w dowolnym momencie żadne dane, przetwarzanie wiadomości jest ponownego uruchamiania, niezależnie od tego, gdzie występuje błąd. Zobacz [utworzyć przesyłania strumieniowego Spark zadania z dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Przesyłanie strumieniowe Spark i YARN

W usłudze HDInsight, klaster pracy jest koordynowane przez *jeszcze inny moduł zasobu negocjowania* (YARN). Projektowanie wysokiej dostępności do przesyłania strumieniowego Spark obejmuje techniki do przesyłania strumieniowego Spark i YARN składników.  Poniżej przedstawiono przykładową konfigurację przy użyciu YARN. 

![Architektura YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

W poniższych sekcjach opisano zagadnienia dotyczące projektowania dla tej konfiguracji.

### <a name="plan-for-failures"></a>Planowanie na wypadek awarii

Aby utworzyć konfigurację YARN wysokiej dostępności, należy zaplanować awarii możliwe Moduł wykonujący lub sterownika. Niektóre zadania przesyłania strumieniowego Spark również obejmować wymagania dotyczące gwarancji danych, które wymagają dodatkowej konfiguracji i instalacji. Na przykład przesyłania strumieniowego aplikacji może być wymaganiem biznesowym zero —-utratę danych zagwarantować pomimo wszelkie błędy występujące w klastrze HDInsight lub systemu, w którym przesyłania strumieniowego.

Jeśli **Moduł wykonujący** kończy się niepowodzeniem, jego zadań i odbiorcy są automatycznie uruchamiany ponownie przez Spark, dlatego nie jest wymagana żadna zmiana konfiguracji.

Jednak jeśli **sterownika** zakończy się niepowodzeniem, a następnie z jego skojarzony z modułów wykonujących nie powiedzie się i wszystkich odebranych bloków i wyników obliczeń zostaną utracone. Do odzyskiwania po awarii sterowników, należy użyć *DStream tworzenie punktów kontrolnych* zgodnie z opisem w [utworzyć przesyłania strumieniowego Spark zadania z dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Tworzenie punktów kontrolnych DStream zapisuje okresowo *skierowane wykresu acyklicznego* (DAG) z DStreams do magazynu odpornej na uszkodzenia, takie jak magazyn Azure.  Tworzenie punktów kontrolnych umożliwia Spark strukturalnych przesyłania strumieniowego o ponowne uruchomienie sterownik nie powiodło się z informacji punktu kontrolnego.  Ponowne uruchomienie tego sterownika uruchamia nowych modułów i również ponowne uruchomienie odbiorników.

Aby odzyskać sterowniki z DStream tworzenie punktów kontrolnych:

* Konfigurowanie sterownika automatycznego ponownego uruchomienia w ramach platformy YARN z ustawieniem konfiguracji `yarn.resourcemanager.am.max-attempts`.
* Ustawianie katalogu punktu kontrolnego w systemie plików HDFS zgodnego z `streamingContext.checkpoint(hdfsDirectory)`.
* Restrukturyzacja kodu źródłowego w celu użycia punktów kontrolnych podczas odzyskiwania, na przykład:

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

* Konfiguracja odzyskiwania utraconych danych przez włączenie dziennika zapisu z wyprzedzeniem (odnowy) z `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`i Wyłącz replikację w pamięci dla wejściowych DStreams z `StorageLevel.MEMORY_AND_DISK_SER`.

Podsumowując, przy użyciu tworzenie punktów kontrolnych + odnowy niezawodnej odbiorcy, będzie mogła dostarczać "co najmniej raz" Odzyskiwanie danych:

* Dokładnie po, tak długo, jak odebrane dane nie zostaną utracone i dane wyjściowe są albo idempotentności lub transakcyjnych.
* Dokładnie raz dzięki temu bezpośredniego Kafka, który używa Kafka jako zreplikowane dziennika, a nie przy użyciu odbiorcy lub WALs.

### <a name="typical-concerns-for-high-availability"></a>Typowe problemy wysokiej dostępności

* Jest trudne do monitorowania zadań przesyłania strumieniowego niż zadania wsadowe. Zadania przesyłania strumieniowego Spark są zwykle długotrwałe i YARN nie agregacji dzienniki, zakończenie zadania.  Platforma Spark punkty kontrolne zostaną utracone podczas uaktualnienia Spark i aplikacji, a należy wyczyścić katalog punktu kontrolnego podczas uaktualniania.

* Skonfiguruj tryb klastra YARN uruchomienie sterowników, nawet jeśli klient nie powiedzie się. Aby skonfigurować automatyczne ponowne uruchomienie sterowników:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Platforma Spark i Interfejsie przesyłania strumieniowego Spark jest system można skonfigurować metryki. Można również użyć dodatkowych bibliotek, takich jak grafitu/Grafana, aby pobrać metryk do pulpitu nawigacyjnego, takie jak "przetworzonych rekordów num", "Użycie pamięci/GC na sterowników i modułów", "opóźnienie całkowita", "wykorzystanie klastra" itd. W strukturze przesyłania strumieniowego w wersji 2.1 lub nowszej, można użyć `StreamingQueryListener` zebrać dodatkowe metryki.

* Należy podzielić długotrwałe zadania.  Po przesłaniu do przesyłania strumieniowego Spark aplikacji do klastra musi być zdefiniowany kolejki YARN, gdy zadanie działa. Można użyć [YARN pojemności harmonogramu](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) do przesyłania zadań długotrwałe, aby oddzielić kolejek.

* Bezpiecznie zamknąć przesyłania strumieniowego aplikacji. Jeśli wiadomo, że Twoje przesunięcia, a wszystkie stanu aplikacji są przechowywane zewnętrznie, można programowo zatrzymać przesyłania strumieniowego aplikacji w odpowiednim miejscu. Jeden technika jest użycie "wątku punkty zaczepienia" w łączniku Spark, sprawdzając zewnętrznych flagi co *n* sekund. Można również użyć *pliku znacznika* który jest utworzony w systemie plików HDFS podczas uruchamiania aplikacji, a następnie usuwane, gdy chcesz zatrzymać. Podejście znacznika pliku można użyć w oddzielnym wątku w aplikacji Spark, która wywołuje podobny do poniższego kodu:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Platforma Spark przesyłania strumieniowego — omówienie](apache-spark-streaming-overview.md)
* [Utwórz zadania przesyłania strumieniowego Spark dokładnie — raz zdarzenia przetwarzania](apache-spark-streaming-exactly-once.md)
* [Platforma Spark długotrwałych zadań w ramach platformy YARN przesyłania strumieniowego](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturalne przesyłania strumieniowego: Semantyka odporny na błędy błędów](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Zdyskretyzowany strumieni: Model odpornej na uszkodzenia strumienia skalowalne przetwarzania](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
