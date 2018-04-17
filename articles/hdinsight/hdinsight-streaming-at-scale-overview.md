---
title: Przesyłanie strumieniowe na dużą skalę w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak używać danych przesyłania strumieniowego z skalowalne klastrów usługi HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 4f1a0873ccdffde7e3567d7e3c50336b20749116
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Przesyłanie strumieniowe na dużą skalę w usłudze HDInsight

Rozwiązania typu big data w czasie rzeczywistym działają na danych znajdujących się w ruchu. Zazwyczaj tych danych jest najbardziej przydatna w momencie jego odbioru. Jeśli przychodzącego strumienia danych jest większa niż mogą być obsługiwane w tej chwili, konieczne może być ograniczania zasobów w dół. Alternatywnie klastra usługi HDInsight można skalować do spełnienia rozwiązania do przesyłania strumieniowego, dodając węzły na żądanie.

W przypadku przesyłania strumieniowego aplikacji co najmniej jedno źródło danych generowania zdarzenia (czasami w milionach na sekundę), które muszą być pozyskanych szybko bez porzucenie wszelkie użyteczne informacje. Zdarzenia przychodzące są obsługiwane z *buforowanie strumienia*, nazywany również *zdarzeń usługi kolejkowania wiadomości*, przez usługę, takich jak [Kafka](kafka/apache-kafka-introduction.md) lub [usługi Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po zebraniu zdarzenia można następnie analizować dane przy użyciu systemu analiz w czasie rzeczywistym w *przetwarzania strumienia* warstw, takich jak [Storm](storm/apache-storm-overview.md) lub [przesyłania strumieniowego Spark](spark/apache-spark-streaming-overview.md). Przetworzone dane mogą być przechowywane w systemach magazynu długoterminowego, tak samo, jak [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)i wyświetlane w czasie rzeczywistym na pulpicie nawigacyjnym analizy biznesowej, takich jak [usługi Power BI](https://powerbi.microsoft.com), Tableau lub niestandardowej strony sieci web .

![HDInsight wzorców przesyłania strumieniowego](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka oferuje wysokiej przepustowości, Usługa kolejkowania wiadomości małe opóźnienia i jest obecnie częścią zestawu Apache oprogramowania źródłowym Otwórz (OSS). Kafka używa publikowania i subskrybowania komunikatów modelu i magazyny strumieni podzielonym na partycje danych bezpiecznie w klastrze rozproszonych, replikowany. Kafka zapewnia Skalowanie liniowe, jak zwiększa przepustowość.

Aby uzyskać więcej informacji, zobacz [wprowadzenie Kafka Apache na HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm to system obliczeniowy rozproszonych, odpornej na uszkodzenia, open source jest zoptymalizowany pod kątem przetwarzania strumieni danych w czasie rzeczywistym z platformą Hadoop. Jednostki podstawowe dane dla zdarzenia przychodzącego jest spójnej kolekcji, która jest niezmienialny zestaw par klucz/wartość. Niepowiązane sekwencji formach krotek strumienia, który jest pochodzi z Spout. Spout opakowuje przesyłania strumieniowego źródła danych (takich jak Kafka) i emituje spójnych kolekcji. Topologii platformy storm jest sekwencją przekształcenia na tych strumieni.

Aby uzyskać więcej informacji, zobacz [co to jest Apache Storm w usłudze Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Przesyłanie strumieniowe Spark

Przesyłanie strumieniowe Spark jest rozszerzeniem Spark, dzięki czemu można ponownie użyć tego samego kodu, którego używasz do przetwarzania wsadowego. Możesz łączyć zarówno partii i interakcyjnych zapytań w tej samej aplikacji. W odróżnieniu od Storm, Spark przesyłania strumieniowego zapewnia stateful dokładnie-po przetwarzania semantyki. W połączeniu z [Kafka bezpośredniego interfejsu API](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), który zapewnia, że wszystkie dane Kafka jest odbierane dokładnie raz przez przesyłania strumieniowego Spark, jest możliwość osiągnięcia dokładnie end-to-end-raz gwarancji. Jednym z strumieniowego Spark sile jest jego możliwości odpornej na uszkodzenia, odzyskiwanie szybko błędny węzłów, gdy jest używanych wiele węzłów w klastrze.

Aby uzyskać więcej informacji, zobacz [co to jest przesyłanie strumieniowe Spark?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skalowanie klastra

Chociaż podczas tworzenia, można określić liczbę węzłów w klastrze, można zwiększać i zmniejszać klastra w celu dopasowania obciążenie. Wszystkie klastry usługi HDInsight umożliwiają [zmianę liczby węzłów w klastrze](hdinsight-administer-use-management-portal.md#scale-clusters). Klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w magazynie Azure lub usługi Data Lake Store.

Istnieją pewne zalety odsprzęgania technologii. Na przykład Kafka jest zdarzeniem buforowanie technologii, dzięki czemu jest bardzo we/wy znacznym i nie wymaga znacznie mocy obliczeniowej. W porównaniu procesorów strumienia, np. przesyłania strumieniowego Spark są obliczeniowych, wymagających bardziej zaawansowanych maszyn wirtualnych. Dzięki użyciu tych technologii całkowicie niezależna w różnych klastrach, można skalować je niezależnie podczas najlepiej przy użyciu maszyn wirtualnych.

### <a name="scale-the-stream-buffering-layer"></a>Skalowania warstwy buforowanie strumienia

Strumień buforowanie technologii centra zdarzeń i Kafka używają partycji i konsumentów odczytywać te partycje. Skalowanie przepływności wejściowego wymaga skalowanie liczby partycji i dodawanie partycji zawiera równoległości rośnie. W centra zdarzeń liczba partycji nie można zmienić po wdrożeniu tak ważne jest, aby rozpoczynać się od skali docelowego na uwadze. Z Kafka, istnieje możliwość [Dodawanie partycji](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), nawet wtedy, gdy Kafka przetwarzania danych. Kafka udostępnia narzędzia do ponownego przypisania partycji, `kafka-reassign-partitions.sh`. Usługa HDInsight zapewnia [replik partycji ponowne równoważenie narzędzie](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Wywołuje narzędzie rebalancing `kafka-reassign-partitions.sh` narzędzia w taki sposób, że każdej repliki w domenie błędów oddzielne i domeny aktualizacji wprowadzania Kafka stojak pamiętać i zwiększa odporność na uszkodzenia.

### <a name="scale-the-stream-processing-layer"></a>Skalowania warstwy przetwarzania strumienia

Apache Storm i przesyłania strumieniowego Spark obsługuje dodawanie węzłów procesu roboczego do ich klastrów, nawet wtedy, gdy dane są przetwarzane.

Aby korzystać z nowych węzłów dodanych do skalowania Storm, musisz ponowne zrównoważenie wszelkich topologii Storm zostało uruchomione przed zwiększeniem rozmiaru klastra. To ponowne równoważenie może odbywać się za pomocą Storm sieci web interfejsu użytkownika lub jego interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [dokumentację Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark używa trzech parametrów klucza do konfigurowania jej środowisko, w zależności od wymagań aplikacji: `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. *Moduł wykonujący* jest procesem, który jest uruchamiany dla aplikacji Spark. Moduł wykonujący działa w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba modułów i rozmiary Moduł wykonujący dla każdego klastra są obliczane na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Numery te są przechowywane w `spark-defaults.conf`pliku na każdego węzła głównego klastra.

Te trzy parametry można skonfigurować na poziomie klastra dla wszystkich aplikacji, które są uruchomione w klastrze, a także można określić dla poszczególnych aplikacji. Aby uzyskać więcej informacji, zobacz [zarządzanie zasobami klastrów platformy Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do Apache Storm w usłudze HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](storm/apache-storm-example-topology.md)
* [Wprowadzenie do platformy Spark w usłudze HDInsight](spark/apache-spark-overview.md)
* [Rozpoczynać Kafka Apache na HDInsight](kafka/apache-kafka-get-started.md)
