---
title: Przesyłanie strumieniowe na dużą skalę w usłudze Azure HDInsight
description: Jak korzystać z klastrami HDInsight, skalowalna do strumieniowego przesyłania danych.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e2b6cbabc9a0c727c9eb0232bd55048493b29128
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763716"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Przesyłanie strumieniowe na dużą skalę w usłudze HDInsight

W czasie rzeczywistym danych big Data działają na danych w ruchu. Zazwyczaj te dane są najbardziej przydatne w momencie jego nadejścia. Przychodzący strumień danych staje się większą niż mogą być obsługiwane w tej chwili, może być konieczne ograniczanie zasobów w dół. Alternatywnie klaster usługi HDInsight skalować w górę do spełnienia rozwiązania do przesyłania strumieniowego poprzez dodawanie węzłów na żądanie.


W przypadku przesyłania strumieniowego aplikacji co najmniej jedno źródło danych jest generowany zdarzeń (czasem w milionach na sekundę), które muszą być pozyskiwane szybko bez usuwania żadnych użytecznych informacji. Zdarzenia przychodzące są obsługiwane za pomocą *buforowanie strumienia*, nazywane również *Kolejkowanie zdarzeń*, przez usługę, takich jak [platformy Apache Kafka](kafka/apache-kafka-introduction.md) lub [usługi Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po zebraniu zdarzenia można następnie analizować dane przy użyciu systemu analizy w czasie rzeczywistym w ramach *przetwarzanie strumienia* warstwy, takich jak [Apache Storm](storm/apache-storm-overview.md) lub [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Przetworzone dane mogą być przechowywane w systemów magazynowania długoterminowe, takie jak [usługi Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)i wyświetlane w czasie rzeczywistym na pulpicie nawigacyjnym analizy biznesowej, takich jak [usługi Power BI](https://powerbi.microsoft.com), Tableau lub niestandardowe sieci web Strona.


![HDInsight Streaming wzorców](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Platforma Apache Kafka oferuje wysokiej przepływności, Usługa kolejkowania wiadomości o małych opóźnieniach i jest teraz częścią pakietu Apache Software źródła otwartych (OSS). Platforma Kafka korzysta publikowania i subskrybowania komunikatów strumieni modeli i magazynów danych podzielonych na partycje bezpiecznie w klastrze rozproszonym, replikowany. Platforma Kafka liniowe skalowanie w miarę zwiększania przepływności.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do platformy Apache Kafka w HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm to system obliczeń rozproszonych, odpornej na uszkodzenia, typu open source, zoptymalizowana pod kątem przetwarzania strumieni danych w czasie rzeczywistym z użyciem usługi Hadoop. Jednostki podstawowe dane dla zdarzenia przychodzącego jest spójnej kolekcji niezmienialnych zestaw par klucz/wartość. Niepowiązanego sekwencję formach krotek Stream, który jest pochodzi z Spout. Spout otacza przesyłania strumieniowego źródła danych (takiej jak Kafka) i emituje krotek. Topologii platformy storm to sekwencja przekształceń dla tych strumieni.

Aby uzyskać więcej informacji, zobacz [co to jest Apache Storm w usłudze Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Przesyłania strumieniowego platformy Spark

Przesyłanie strumieniowe Spark jest rozszerzeniem platformy Spark, która pozwala na ponowne użycie tego samego kodu, których używasz do przetwarzania wsadowego. Możesz połączyć usługi batch i interakcyjnych zapytań w tej samej aplikacji. W przeciwieństwie do systemu Storm, Spark Streaming oferuje stanowa dokładnie — jednokrotne przetwarzanie semantyki. W połączeniu z [bezpośrednie interfejsu API platformy Kafka](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), który zapewnia, że wszystkie dane platformy Kafka jest odbierany przez przesyłanie strumieniowe Spark dokładnie jeden raz, jest możliwe uzyskanie dokładnie end-to-end-gwarantuje raz. Jedną z mocnych strumieniowe Spark jest jego możliwości odpornej na uszkodzenia, odzyskiwanie szybko zwracające błędy węzłów, gdy są używane wiele węzłów w klastrze.

Aby uzyskać więcej informacji, zobacz [co to jest Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skalowanie klastra

Chociaż można określić liczbę węzłów w klastrze podczas tworzenia, może być możliwość zwiększania i zmniejszania klastra w celu dopasowania obciążenia. Wszystkie klastry HDInsight umożliwiają [zmianę liczby węzłów w klastrze](hdinsight-administer-use-portal-linux.md#scale-clusters). Klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub magazynu usługi Data Lake.

Istnieją zalety łączenia odsprzęgania technologii. Na przykład platforma Kafka jest zdarzenie buforowania technologii, dzięki czemu jest bardzo operacji We/Wy o znacznym wykorzystaniu i nie wymaga znacznie mocy obliczeniowej. W odróżnieniu od procesorów strumieni, takich jak Spark Streaming są intensywnie wymagające bardziej wydajne maszyny wirtualne. Dzięki tych technologii odłączone w różnych klastrach, możesz skalować je niezależnie najlepszego wykorzystania maszyn wirtualnych.

### <a name="scale-the-stream-buffering-layer"></a>Skalowanie usługi stream, buforowanie, warstwy

Strumień buforowania technologii usługi Event Hubs i Kafka używają partycji, a konsumenci zapoznaj się z tych partycji. Skalowanie przepływność danych wejściowych wymaga skalowanie w górę liczby partycji i dodawanie partycji umożliwia zwiększenie równoległości. W usłudze Event Hubs liczby partycji nie można zmienić po wdrożeniu, więc należy rozpoczynać skalowania docelowych należy pamiętać. Za pomocą platformy Kafka, istnieje możliwość [Dodawanie partycji](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), nawet wtedy, gdy platforma Kafka jest przetwarzanie danych. Platforma Kafka udostępnia narzędzie do ponownego przypisania partycji, `kafka-reassign-partitions.sh`. HDInsight zapewnia [repliki partycji ponowne równoważenie narzędzie](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Wywołuje narzędzie to ponowne `kafka-reassign-partitions.sh` narzędzi w taki sposób, że każda replika znajduje się w awarii oddzielne domeny i domeny aktualizacji, co Kafka stojak pamiętać i zwiększa odporność na uszkodzenia.

### <a name="scale-the-stream-processing-layer"></a>Skalowanie warstwy przetwarzania strumienia

Apache Storm i Spark Streaming obsługuje dodawanie węzłów procesu roboczego ich klastry, nawet wtedy, gdy dane są przetwarzane.

Aby móc skorzystać z nowych węzłów dodanych do skalowania systemu Storm, konieczne jest ponowne zrównoważenie wszelkich topologii systemu Storm uruchomionych przed zwiększeniem rozmiaru klastra. To ponowne równoważenie może odbywać się przy użyciu technologii Storm w sieci web interfejsu użytkownika lub jego interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [dokumentacji platformy Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark korzysta z trzech parametrów klucza konfigurowania swoje środowisko, w zależności od wymagań aplikacji: `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. *Wykonawca* to proces, który jest uruchamiany w aplikacji platformy Spark. Wykonawca działa w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba executors i rozmiary przetwarzania dla każdego klastra są obliczane na podstawie liczby węzłów procesu roboczego i rozmiar węzła procesu roboczego. Numery te są przechowywane w `spark-defaults.conf`pliku na każdy węzeł główny klastra.

Te trzy parametry można skonfigurować na poziomie klastra dla wszystkich aplikacji, które są uruchamiane w klastrze i można również określić dla poszczególnych aplikacji. Aby uzyskać więcej informacji, zobacz [zarządzania zasobami klastrów Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy z usługą Apache Storm w HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](storm/apache-storm-example-topology.md)
* [Wprowadzenie do platformy Apache Spark w HDInsight](spark/apache-spark-overview.md)
* [Uruchom przy użyciu platformy Apache Kafka w HDInsight](kafka/apache-kafka-get-started.md)
