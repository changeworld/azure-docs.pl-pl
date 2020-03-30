---
title: Spark Streaming & dokładnie raz przetwarzania zdarzeń — Azure HDInsight
description: Jak skonfigurować Apache Spark Streaming do przetwarzania zdarzenia raz i tylko raz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228969"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Tworzenie zadań przesyłania strumieniowego platformy Apache Spark z przetwarzaniem zdarzeń dokładnie raz

Aplikacje do przetwarzania strumienia przyjmują różne podejścia do sposobu obsługi komunikatów przetwarzania po pewnym niepowodzeniu w systemie:

* Co najmniej raz: każda wiadomość jest gwarantowana do przetworzenia, ale może zostać przetworzona więcej niż jeden raz.
* Co najwyżej raz: Każda wiadomość może lub nie może być przetwarzana. Jeśli wiadomość jest przetwarzana, jest przetwarzana tylko raz.
* Dokładnie raz: Każda wiadomość jest gwarantowana do przetworzenia raz i tylko raz.

W tym artykule pokazano, jak skonfigurować usługę Spark Streaming, aby osiągnąć przetwarzanie dokładnie raz.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Dokładnie raz semantyka z Apache Spark Streaming

Najpierw należy wziąć pod uwagę, jak wszystkie punkty systemowe awarii ponownie po problemie i jak można uniknąć utraty danych. Aplikacja Spark Streaming posiada:

* Źródło wejściowe.
* Jeden lub więcej procesów odbiornika, które ściągają dane ze źródła wejściowego.
* Zadania, które przetwarzają dane.
* Zlew wyjściowy.
* Proces sterownika, który zarządza długotrwałym zadaniem.

Semantyka dokładnie raz wymaga, aby żadne dane nie zostały utracone w dowolnym momencie, a przetwarzanie wiadomości można ponownie uruchomić, niezależnie od tego, gdzie występuje błąd.

### <a name="replayable-sources"></a>Źródła powtarzalne

Źródło, z aplikacja Spark Streaming odczytuje zdarzenia, musi być *odtwarzane.* Oznacza to, że w przypadkach, gdy wiadomość została pobrana, ale następnie system nie powiódł się, zanim wiadomość może być utrwalona lub przetworzona, źródło musi dostarczyć ten sam komunikat ponownie.

Na platformie Azure zarówno usługi Azure Event Hubs, jak i [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight zapewniają źródła powtarzalne. Innym przykładem źródła do grywalnego jest odporny na uszkodzenia system plików, taki jak [Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)obiekty blob usługi Azure Storage lub usługa Azure Data Lake Storage, w której wszystkie dane są przechowywane na zawsze i w dowolnym momencie można ponownie odczytać dane w całości.

### <a name="reliable-receivers"></a>Niezawodne odbiorniki

W spark streaming źródeł, takich jak Event Hubs i Kafka mają *niezawodne odbiorniki,* gdzie każdy odbiornik śledzi jego postęp odczytu źródła. Niezawodny odbiornik utrzymuje swój stan do magazynu odpornego na uszkodzenia, w [Apache ZooKeeper](https://zookeeper.apache.org/) lub w punktach kontrolnych Spark Streaming napisanych do hdfs. Jeśli taki odbiornik ulegnie awarii, a później zostanie ponownie uruchomiony, może odebrać go tam, gdzie został przerwany.

### <a name="use-the-write-ahead-log"></a>Korzystanie z dziennika zapisu z wyprzedzeniem

Usługa Spark Streaming obsługuje użycie dziennika zapisu z wyprzedzeniem, w którym każde odebrane zdarzenie jest najpierw zapisywane w katalogu punktu kontrolnego platformy Spark w magazynie odpornym na uszkodzenia, a następnie przechowywane w odpornym rozproszonym zestawie danych (RDD). Na platformie Azure magazyn odporny na uszkodzenia jest hdfs wspierane przez usługi Azure Storage lub usługi Azure Data Lake Storage. W aplikacji Spark Streaming dziennik zapisu z wyprzedzeniem jest włączony `spark.streaming.receiver.writeAheadLog.enable` dla `true`wszystkich odbiorników, ustawiając ustawienie konfiguracji na . Dziennik zapisu z wyprzedzeniem zapewnia odporność na uszkodzenia błędów zarówno sterownika, jak i wykonawców.

W przypadku pracowników uruchamianych zadania względem danych zdarzeń każdy RDD jest z definicji replikowany i dystrybuowany między wieloma procesami. Jeśli zadanie nie powiedzie się, ponieważ pracownik, który go uruchomił, zadanie zostanie ponownie uruchomione na innym pracowniku, który ma replikę danych zdarzenia, więc zdarzenie nie zostanie utracone.

### <a name="use-checkpoints-for-drivers"></a>Używanie punktów kontrolnych dla kierowców

Sterowniki zadań muszą być ponownie uruchomione. Jeśli sterownik z uruchomiona aplikacją Spark Streaming ulegnie awarii, zdejmuje ze sobą wszystkie uruchomione odbiorniki, zadania i wszystkie rddy przechowujące dane zdarzeń. W takim przypadku musisz mieć możliwość zapisania postępu zadania, aby można było je wznowić później. Jest to realizowane przez punkty kontrolne skierowane wykres acykliczny (DAG) dstream okresowo do magazynu odpornego na uszkodzenia. Metadane DAG zawiera konfigurację używaną do tworzenia aplikacji przesyłania strumieniowego, operacje, które definiują aplikację i wszystkie partie, które są w kolejce, ale nie zostały jeszcze ukończone. Te metadane umożliwia uruchomienie sterownika nie powiodło się z informacji o punkcie kontrolnym. Po ponownym uruchomieniu sterownika uruchomi nowe odbiorniki, które same odzyskać dane zdarzenia z powrotem do RDD z dziennika zapisu z wyprzedzeniem.

Punkty kontrolne są włączone w spark streaming w dwóch krokach.

1. W obiekcie StreamingContext skonfiguruj ścieżkę magazynu dla punktów kontrolnych:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    W usłudze HDInsight te punkty kontrolne powinny być zapisywane w domyślnym magazynie dołączonym do klastra, usługi Azure Storage lub Usługi Azure Data Lake Storage.

2. Następnie należy określić interwał punktu kontrolnego (w sekundach) na DStream. W każdym interwale dane o stanie pochodzące ze zdarzenia wejściowego są utrwalone do magazynu. Dane stanu utrwalone można zmniejszyć obliczenia potrzebne podczas odbudowywania stanu ze zdarzenia źródłowego.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Korzystanie z zlewów idempotentnych

Ujście docelowe, do którego zadanie zapisuje wyniki musi być w stanie obsłużyć sytuację, w której jest podane ten sam wynik więcej niż jeden raz. Zlew musi być w stanie wykryć takie zduplikowane wyniki i zignorować je. *Idempotentny* umywalka można wywołać wiele razy z tych samych danych bez zmiany stanu.

Można utworzyć idempotentne pochłaniacze, implementując logikę, która najpierw sprawdza istnienie wyniku przychodzącego w magazynie danych. Jeśli wynik już istnieje, zapis powinien pojawić się pomyślnie z punktu widzenia zadania platformy Spark, ale w rzeczywistości magazyn danych zignorował zduplikowane dane. Jeśli wynik nie istnieje, a następnie ujście należy wstawić ten nowy wynik do magazynu.

Na przykład można użyć procedury składowanej z usługi Azure SQL Database, która wstawia zdarzenia do tabeli. Ta procedura składowana najpierw wyszukuje zdarzenie według pól klucza i tylko wtedy, gdy nie znaleziono pasującego zdarzenia, rekord zostanie wstawiony do tabeli.

Innym przykładem jest użycie systemu plików podzielonych na partycje, takich jak obiekty blob usługi Azure Storage lub usługi Azure Data Lake Storage. W takim przypadku logiki ujścia nie trzeba sprawdzać istnienia pliku. Jeśli plik reprezentujący zdarzenie istnieje, jest po prostu zastępowany tymi samymi danymi. W przeciwnym razie nowy plik jest tworzony na obliczonej ścieżce.

## <a name="next-steps"></a>Następne kroki

* [Apache Spark Streaming — omówienie](apache-spark-streaming-overview.md)
* [Tworzenie wysoce dostępnych miejsc pracy Apache Spark Streaming w Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
