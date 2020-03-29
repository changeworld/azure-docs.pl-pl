---
title: Migrowanie usługi Azure HDInsight 3.6 Apache Storm do usługi HDInsight 4.0 Apache Spark
description: Różnice i przepływ migracji do migracji obciążeń Apache Storm do spark streaming lub Spark Structured Streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157794"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrowanie usługi Azure HDInsight 3.6 Apache Storm do usługi HDInsight 4.0 Apache Spark

W tym dokumencie opisano sposób migracji obciążeń usługi Apache Storm w programie HDInsight 3.6 do HDInsight 4.0. Usługa HDInsight 4.0 nie obsługuje typu klastra Apache Storm i należy przeprowadzić migrację na inną platformę przesyłania strumieniowego danych. Dwie odpowiednie opcje to Apache Spark Streaming i Spark Structured Streaming. W tym dokumencie opisano różnice między tymi platformami, a także zaleca się przepływ pracy do migracji obciążeń Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Ścieżki migracji burzowej w umiań HDInsight

Jeśli chcesz przeprowadzić migrację z apache Storm na HDInsight 3.6, masz wiele opcji:

* Spark Streaming na HDInsight 4.0
* Strumieniowanie strukturalne Spark na HDInsight 4.0
* Azure Stream Analytics

Ten dokument zawiera przewodnik do migracji z Usługi Apache Storm do spark streaming i Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![Ścieżka migracji hdinsight storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Porównanie apache Storm i Spark Streaming, Spark Structured Streaming

System Apache Storm zapewnia różne poziomy gwarantowanego przetwarzania komunikatów. Na przykład podstawowa aplikacja Storm może zagwarantować przetwarzanie co najmniej raz, a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) może zagwarantować dokładnie po przetworzeniu. Spark Streaming i Spark Structured Streaming gwarantuje, że każde zdarzenie wejściowe jest przetwarzane dokładnie raz, nawet jeśli wystąpi błąd węzła. Storm ma model, który przetwarza każde pojedyncze zdarzenie, a także można użyć modelu micro batch z Trident. Spark Streaming i Spark Structured Streaming zapewniają mikro-batch przetwarzania modelu.

|  |Storm |Przesyłanie strumieniowe w usłudze Spark | Strumieniowanie strukturalne Spark|
|---|---|---|---|
|**Gwarancja przetwarzania zdarzeń**|Co najmniej raz <br> Dokładnie raz (Trident) |[Dokładnie raz](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Dokładnie raz](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Model przetwarzania**|Przesyłanie w czasie rzeczywistym <br> Mikro partia (Trójząb) |Mikro wsadowe |Mikro wsadowe |
|**Obsługa czasu zdarzenia**|[Tak](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nie|[Tak](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Języki**|Java, itp.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Strumieniowanie spark vs strumieniowanie strukturalne Spark

Spark Structured Streaming zastępuje Spark Streaming (DStreams). Usługa Strumieniowanie strukturalne będzie nadal otrzymywać ulepszenia i konserwacji, podczas gdy DStreams będzie tylko w trybie konserwacji. **Uwaga: potrzebujesz linków, aby podkreślić ten punkt**. Usługa strumieniowanie strukturalne nie ma tak wielu funkcji, jak DStreams dla źródeł i pochłaniacze, które obsługuje po wyjęciu z pudełka, więc ocenić wymagania, aby wybrać odpowiednią opcję przetwarzania strumienia platformy Spark.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Przetwarzanie przesyłania strumieniowego (pojedyncze zdarzenie) vs przetwarzanie mikro-wsadowe

Storm udostępnia model, który przetwarza każde pojedyncze zdarzenie. Oznacza to, że wszystkie przychodzące rekordy będą przetwarzane natychmiast po ich przybyciu. Aplikacje spark Streaming musi czekać ułamek sekundy, aby zebrać każdą mikro-partii zdarzeń przed wysłaniem tej partii do przetwarzania. Z drugiej strony aplikacja oparta na zdarzeniach przetwarza każde zdarzenie natychmiast. Opóźnienie przesyłania strumieniowego platformy Spark jest zazwyczaj w ciągu kilku sekund. Korzyści z podejścia mikro-partii są bardziej efektywne przetwarzanie danych i prostsze obliczenia zbiorcze.

> [!div class="mx-imgBorder"]
> ![przesyłanie strumieniowe i przetwarzanie mikro-wsadowe](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Architektura i komponenty burzowe

Topologie systemu Storm obejmują wiele składników rozmieszczonych w skierowanym grafie acyklicznym (DAG). Dane przepływają między składnikami tego grafu. Każdy składnik używa przynajmniej jednego strumienia danych i może opcjonalnie emitować przynajmniej jeden strumień.

|Składnik |Opis |
|---|---|
|Wylewka|Wprowadza dane do topologii. Wysyłają one co najmniej jeden strumień danych do topologii.|
|Bolt|Zużywa strumienie emitowane z wylewek lub innych śrub. Składniki typu bolt mogą opcjonalnie emitować strumienie do topologii. Odpowiadają również za zapisywanie danych w usługach zewnętrznych lub magazynie — takim jak system plików HDFS, platforma Kafka lub usługa HBase.|

> [!div class="mx-imgBorder"]
> ![interakcja składników burzowych](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm składa się z następujących trzech demonów, które utrzymują działanie klastra Burz.

|Daemon |Opis |
|---|---|
|Nimbus|Podobnie jak Hadoop JobTracker, jest odpowiedzialny za dystrybucję kodu wokół klastra i przypisywanie zadań do maszyn i monitorowanie błędów.|
|Zookeeper|Służy do koordynacji klastra.|
|Nadzorca|Nasłuchuje pracy przypisanej do maszyny i uruchamia i zatrzymuje procesy robocze oparte na dyrektywach nimbusa. Każdy proces roboczy wykonuje podzbiór topologii. Logika aplikacji użytkownika (Spouts i Bolt) uruchomić tutaj.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper i demony nadzorcy](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Architektura i komponenty Spark Streaming

W poniższych krokach podsumowano, jak składniki współpracują ze sobą w zakresie przesyłania strumieniowego iskrowego (DStreams) i strumieniowania strukturalnego platformy Spark:

* Po uruchomieniu usługi Spark Streaming sterownik uruchamia zadanie w wykonawcy.
* Wykonawca odbiera strumień ze źródła danych przesyłania strumieniowego.
* Gdy wykonawca odbiera strumienie danych, dzieli strumień na bloki i przechowuje je w pamięci.
* Bloki danych są replikowane do innych executors.
* Przetwarzane dane są następnie przechowywane w docelowym magazynie danych.

> [!div class="mx-imgBorder"]
> ![ścieżka strumieniowania iskrą do wyjścia](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Przepływ pracy przesyłania strumieniowego iskrowego (DStream)

W miarę upływu każdego interwału partii jest produkowany nowy RDD, który zawiera wszystkie dane z tego interwału. Ciągłe zestawy RDD są zbierane w DStream. Na przykład jeśli interwał partii jest jedna sekunda długości, DStream emituje partii co sekundę zawierające jeden RDD, który zawiera wszystkie dane pojął w ciągu tej sekundy. Podczas przetwarzania DStream zdarzenie temperatury pojawia się w jednej z tych partii. Aplikacja Spark Streaming przetwarza partie, które zawierają zdarzenia i ostatecznie działa na dane przechowywane w każdym RDD.

> [!div class="mx-imgBorder"]
> ![partie przetwarzania strumieniowania iskrowego](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Aby uzyskać szczegółowe informacje na temat różnych przekształceń dostępnych za pomocą usługi Spark Streaming, zobacz [Przekształcenia na strumieniach DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Strumieniowanie strukturalne Spark

Spark Structured Streaming reprezentuje strumień danych jako tabela, która jest nieograniczona w głębi. Tabela nadal rośnie wraz z nadejściem nowych danych. Ta tabela danych wejściowych jest stale przetwarzana przez długotrwałe zapytanie, a wyniki są wysyłane do tabeli danych wyjściowych.

W ustrukturyzowanym strumieniowaniu dane docierają do systemu i są natychmiast pojmowane w tabeli wprowadzania. Piszesz kwerendy (przy użyciu interfejsów API dataframe i zestawu danych), które wykonują operacje względem tej tabeli wejściowej.

Dane wyjściowe kwerendy daje *tabelę wyników*, która zawiera wyniki kwerendy. Można rysować dane z tabeli wyników dla zewnętrznego magazynu danych, takiej relacyjnej bazy danych.

Czas przetwarzania danych z tabeli wejściowej jest kontrolowany przez interwał wyzwalacza. Domyślnie interwał wyzwalacza wynosi zero, więc usługa Structured Streaming próbuje przetworzyć dane natychmiast po ich odebraniu. W praktyce oznacza to, że jak tylko structured streaming odbywa się przetwarzania uruchomienia poprzedniej kwerendy, rozpoczyna się inne przetwarzanie uruchomić względem wszystkich nowo odebranych danych. Wyzwalacz można skonfigurować do uruchamiania w danym przedziale czasu, tak aby dane przesyłania strumieniowego były przetwarzane w partiach opartych na czasie.

> [!div class="mx-imgBorder"]
> ![przetwarzanie danych w ustrukturyzowanym strumieniowaniu](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![model programowania do strumieniowania strukturalnego](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Ogólny przepływ migracji

Zalecany przepływ migracji z storm do spark zakłada następującą architekturę początkową:

* Kafka jest używana jako źródło danych przesyłania strumieniowego
* Kafka i Storm są wdrażane w tej samej sieci wirtualnej
* Dane przetwarzane przez usługę Storm są zapisywane w usłudze sink danych, takiej jak Usługa Azure Storage lub Usługa Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![schemat domniemanego środowiska prądu](./media/migrate-storm-to-spark/presumed-current-environment.png)

Aby przeprowadzić migrację aplikacji z usługi Storm do jednego z interfejsów API przesyłania strumieniowego platformy Spark, wykonaj następujące czynności:

1. **Wdrażanie nowego klastra.** Wdrożyć nowy klaster platformy SPARK usługi HDInsight 4.0 w tej samej sieci wirtualnej i wdrożyć aplikację Spark Streaming lub Spark Structured Streaming na nim i przetestować go dokładnie.

    > [!div class="mx-imgBorder"]
    > ![nowe wdrożenie iskry w umiaśnie HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Przestań spożywać na starym klastrze Storm.** W istniejącej burzy zatrzymaj zużywanie danych ze źródła danych przesyłania strumieniowego i poczekaj, aż dane zakończą zapisywanie do obiektu docelowego.

    > [!div class="mx-imgBorder"]
    > ![przestać zużywać na bieżącym klastrze](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Zacznij ysuwać na nowym klastrze platformy Spark.** Rozpocznij przesyłanie strumieniowe danych z nowo wdrożonego klastra platformy Spark usługi HDInsight 4.0. W tej chwili proces jest przejęty przez zużywanie z najnowszego offsetu Platformy Kafka.

    > [!div class="mx-imgBorder"]
    > ![rozpocząć korzystanie z nowego klastra](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **W razie potrzeby usuń stary klaster.** Po zakończeniu i prawidłowym uruchomieniu przełącznika należy usunąć stary klaster burzy HDInsight 3.6 w razie potrzeby.

    > [!div class="mx-imgBorder"]
    > ![w razie potrzeby usuwa stare klastry HDInsight](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat storm, Spark Streaming i Spark Structured Streaming, zobacz następujące dokumenty:

* [Omówienie przesyłania strumieniowego w ramach apache Spark](../spark/apache-spark-streaming-overview.md)
* [Omówienie strumieniowania strukturalnego Apache Spark](../spark/apache-spark-structured-streaming-overview.md)