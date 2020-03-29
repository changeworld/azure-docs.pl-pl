---
title: Przesyłanie strumieniowe na dużą skalę w usłudze Azure HDInsight
description: Jak używać przesyłania strumieniowego danych za pomocą skalowalnych klastrów Apache w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435393"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Przesyłanie strumieniowe na dużą skalę w usłudze HDInsight

Rozwiązania big data w czasie rzeczywistym działają na podstawie danych, które są w ruchu. Zazwyczaj dane te są najcenniejsze w momencie przybycia. Jeśli przychodzący strumień danych staje się większy niż można obsłużyć w tym momencie, może być konieczne ograniczanie zasobów. Alternatywnie klastra HDInsight można skalować w górę, aby spełnić rozwiązania przesyłania strumieniowego przez dodanie węzłów na żądanie.

W aplikacji przesyłania strumieniowego jedno lub więcej źródeł danych generuje zdarzenia (czasami w milionach na sekundę), które muszą być szybko pozyskiwane bez upuszczania żadnych przydatnych informacji. Zdarzenia przychodzące są obsługiwane za pomocą *buforowania strumienia*, nazywanego również *kolejkowania zdarzeń,* przez usługę, taką jak [Apache Kafka](kafka/apache-kafka-introduction.md) lub [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po zebraniu zdarzeń można analizować dane za pomocą systemu analizy w czasie rzeczywistym w warstwie *przetwarzania strumienia,* takiej jak [Apache Storm](storm/apache-storm-overview.md) lub [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Przetwarzane dane mogą być przechowywane w długoterminowych systemach magazynowania, takich jak [Usługa Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)i wyświetlane w czasie rzeczywistym na pulpicie nawigacyjnym analizy biznesowej, takim jak Power [BI,](https://powerbi.microsoft.com)Tableau lub niestandardowa strona sieci Web.

![Wzorce przesyłania strumieniowego usługi Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka zapewnia usługę kolejkowania komunikatów o wysokiej przepływności i małych opóźnieniach, a teraz jest częścią pakietu Apache oprogramowania open source (OSS). Platforma Kafka używa modelu publikowania i subskrybowania wiadomości i bezpiecznie przechowuje strumienie podzielonych na partycje danych w rozproszonym, replikowanym klastrze. Platforma Kafka skaluje się liniowo wraz ze wzrostem przepływności.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie apache Kafka na HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm to rozproszony, odporny na uszkodzenia system obliczeniowy typu open source, który jest zoptymalizowany do przetwarzania strumieni danych w czasie rzeczywistym za pomocą Hadoop. Podstawową jednostką danych dla zdarzenia przychodzącego jest krotka, która jest niezmiennym zestawem par klucz/wartość. Nieograniczona sekwencja tych krotek tworzy Stream, który pochodzi z wylewki. Wylewka zawija źródło danych przesyłania strumieniowego (takie jak Kafka) i emituje krotek. Topologia burzy to sekwencja przekształceń w tych strumieniach.

Aby uzyskać więcej informacji, zobacz [Co to jest Usługa Apache Storm w usłudze Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Strumieniowanie iskrą

Spark Streaming jest rozszerzeniem platformy Spark, który umożliwia ponowne użycie tego samego kodu, który jest używany do przetwarzania wsadowego. W tej samej aplikacji można łączyć zapytania wsadowe i interaktywne. W przeciwieństwie do Storm, Spark Streaming zapewnia stanowe dokładnie po przetworzeniu semantyki. W połączeniu z [interfejsem API Kafka Direct,](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)który zapewnia, że wszystkie dane platformy Kafka są odbierane przez spark streaming dokładnie raz, możliwe jest osiągnięcie end-to-end dokładnie raz gwarantuje. Jedną z mocnych stron usługi Spark Streaming jest jego możliwości odporne na uszkodzenia, odzyskiwanie węzłów uszkodzonych szybko, gdy wiele węzłów są używane w klastrze.

Aby uzyskać więcej informacji, zobacz [Co to jest Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skalowanie klastra

Chociaż można określić liczbę węzłów w klastrze podczas tworzenia, można zwiększyć lub zmniejszyć klaster, aby dopasować obciążenie. Wszystkie klastry HDInsight umożliwiają [zmianę liczby węzłów w klastrze](hdinsight-administer-use-portal-linux.md#scale-clusters). Klastry platformy Spark można upuścić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub usługi Data Lake Storage.

Istnieją zalety oddzielenia technologii. Na przykład kafka jest technologią buforowania zdarzeń, więc jest bardzo intensywna we/wy i nie potrzebuje dużej mocy obliczeniowej. Dla porównania procesory strumienia, takie jak Spark Streaming, wymagają bardziej zaawansowanych maszyn wirtualnych. Dzięki tym technologiom oddzielonym od różnych klastrów można je skalować niezależnie, przy najlepszym wykorzystaniu maszyn wirtualnych.

### <a name="scale-the-stream-buffering-layer"></a>Skalowanie warstwy buforowania strumienia

Technologie buforowania strumienia Event Hubs i Kafka zarówno używać partycji i konsumentów odczytu z tych partycji. Skalowanie przepływności danych wejściowych wymaga skalowania w górę liczby partycji, a dodawanie partycji zapewnia zwiększenie równoległości. W centrach zdarzeń nie można zmienić liczby partycji po wdrożeniu, dlatego ważne jest, aby rozpocząć z myślą o skali docelowej. Z kafka, jest możliwe, aby [dodać partycje,](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)nawet gdy kafka przetwarza dane. Platforma Kafka udostępnia narzędzie do ponownego `kafka-reassign-partitions.sh`przypisywania partycji, . Usługa HDInsight zapewnia [narzędzie do równoważenia replik partycji](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. To narzędzie równoważenia `kafka-reassign-partitions.sh` wywołuje narzędzie w taki sposób, że każda replika znajduje się w osobnej domenie błędów i domenie aktualizacji, dzięki czemu stojak platformy Kafka jest świadomy i zwiększa odporność na uszkodzenia.

### <a name="scale-the-stream-processing-layer"></a>Skalowanie warstwy przetwarzania strumienia

Zarówno Apache Storm, jak i Spark Streaming obsługują dodawanie węzłów procesu roboczego do swoich klastrów, nawet podczas przetwarzania danych.

Aby skorzystać z nowych węzłów dodanych za pomocą skalowania Storm, musisz ponownie zrównoważyć wszystkie topologie Storm rozpoczęte przed zwiększeniem rozmiaru klastra. To równoważenie można wykonać za pomocą interfejsu użytkownika sieci Storm lub interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [dokumentację Usługi Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark używa trzech kluczowych parametrów do konfigurowania swojego `spark.executor.instances` `spark.executor.cores`środowiska, `spark.executor.memory`w zależności od wymagań aplikacji: , , i . *Wykonawca* jest procesem, który jest uruchamiany dla aplikacji Spark. Wykonawca działa w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba wykonawców i rozmiary executor dla każdego klastra są obliczane na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te numery są przechowywane `spark-defaults.conf`w pliku w każdym węźle głównym klastra.

Te trzy parametry można skonfigurować na poziomie klastra dla wszystkich aplikacji, które są uruchamiane w klastrze, a także można określić dla każdej aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami klastrów Platformy Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i monitorowanie topologii usługi Apache Storm w usłudze Azure HDInsight](storm/apache-storm-quickstart.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](storm/apache-storm-example-topology.md)
* [Wprowadzenie do Apache Spark na HDInsight](spark/apache-spark-overview.md)
* [Wprowadzenie do platformy Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md)
