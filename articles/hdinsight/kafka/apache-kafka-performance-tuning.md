---
title: Optymalizacja wydajności klastrów Apache Kafka HDInsight
description: Zawiera omówienie technik optymalizacji obciążeń platformy Apache Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494913"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optymalizacja wydajności klastrów Apache Kafka HDInsight

W tym artykule przedstawiono kilka sugestii dotyczących optymalizacji wydajności obciążeń Apache Kafka w udziale HDInsight. Nacisk kładzie się na dostosowanie konfiguracji producenta i brokera. Istnieją różne sposoby pomiaru wydajności, a optymalizacje, które stosujesz, będą zależeć od potrzeb biznesowych.

## <a name="architecture-overview"></a>Omówienie architektury

Tematy platformy Kafka służą do organizowania rekordów. Rekordy są tworzone przez producentów i używane przez odbiorców. Producenci wysyłają rekordy do brokerów platformy Kafka, którzy następnie przechowują dane. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka.

Tematy dzielą rekordy między brokerami. Podczas korzystania z rekordów można użyć maksymalnie jednego odbiorcy na jedną partycję, aby osiągnąć równoległe przetwarzanie danych.

Replikacja służy do duplikowania partycji między węzłami. Chroni to przed awariami węzłów (brokera). Pojedyncza partycja między grupą replik jest wyznaczona jako linia odniesienia partycji. Ruch producenta jest kierowany do partycji wiodącej w każdym węźle przy użyciu stanu zarządzanego przez usługę ZooKeeper.

## <a name="identify-your-scenario"></a>Identyfikowanie własnego scenariusza

Wydajność platformy Apache Kafka ma dwa główne aspekty — przepływność i opóźnienie. Przepływność to maksymalna szybkość, z jaką dane mogą być przetwarzane. Wyższa przepustowość jest zwykle lepsza. Opóźnienie to czas potrzebny na przechowywanie lub pobieranie danych. Mniejsze opóźnienie jest zwykle lepsze. Znalezienie właściwej równowagi między przepływnością, opóźnieniem i kosztem infrastruktury aplikacji może być trudne. Wymagania dotyczące wydajności będą prawdopodobnie zgodne z jedną z następujących trzech typowych sytuacji, w zależności od tego, czy wymagana jest wysoka przepustowość, małe opóźnienia lub obie te te czynności:

* Wysoka przepustowość, małe opóźnienia. W tym scenariuszu wymaga zarówno wysokiej przepływności, jak i małych opóźnień (~100 milisekund). Przykładem tego typu aplikacji jest monitorowanie dostępności usługi.
* Wysoka przepustowość, duże opóźnienia. Ten scenariusz wymaga wysokiej przepływności (~1,5 GBps), ale może tolerować większe opóźnienie (< 250 ms). Przykładem tego typu aplikacji jest połknięcie danych telemetrycznych dla procesów w czasie zbliżonym do rzeczywistego, takich jak aplikacje do wykrywania zabezpieczeń i włamań.
* Niska przepustowość, małe opóźnienia. Ten scenariusz wymaga małych opóźnień (< 10 ms) do przetwarzania w czasie rzeczywistym, ale może tolerować niższą przepływność. Przykładem tego typu aplikacji jest pisownia online i sprawdzanie gramatyki.

## <a name="producer-configurations"></a>Konfiguracje producentów

W poniższych sekcjach zostaną wyróżnione niektóre z najważniejszych właściwości konfiguracji w celu optymalizacji wydajności producentów platformy Kafka. Szczegółowe informacje na temat wszystkich właściwości konfiguracji można znaleźć [w dokumentacji programu Apache Kafka dotyczącej konfiguracji producenta.](https://kafka.apache.org/documentation/#producerconfigs)

### <a name="batch-size"></a>Rozmiar partii

Producenci platformy Apache Kafka montują grupy wiadomości (nazywane partiami), które są wysyłane jako jednostka do przechowywania w jednej partycji magazynu. Rozmiar partii oznacza liczbę bajtów, które muszą być obecne przed przekazaniem tej grupy. Zwiększenie `batch.size` parametru może zwiększyć przepływność, ponieważ zmniejsza obciążenie przetwarzania z żądań sieciowych i we/wy. Przy lekkim obciążeniu zwiększony rozmiar partii może zwiększyć opóźnienie wysyłania platformy Kafka, ponieważ producent czeka na przygotowanie partii. Pod dużym obciążeniem zaleca się zwiększenie rozmiaru partii w celu zwiększenia przepływności i opóźnienia.

### <a name="producer-required-acknowledgments"></a>Producent wymagał potwierdzenia

Wymagana `acks` konfiguracja producenta określa liczbę potwierdzeń wymaganych przez lidera partycji, zanim żądanie zapisu zostanie uznane za zakończone. To ustawienie wpływa na niezawodność danych `0`i `1`przyjmuje `-1`wartości , lub . Wartość `-1` oznacza, że potwierdzenie musi zostać odebrane ze wszystkich replik przed zakończeniem zapisu. Ustawienie `acks = -1` zapewnia silniejsze gwarancje przed utratą danych, ale również powoduje większe opóźnienia i niższą przepustowość. Jeśli wymagania aplikacji wymagają wyższej przepływności, spróbuj ustawienie `acks = 0` lub `acks = 1`. Należy pamiętać, że nieuznawanie wszystkich replik może zmniejszyć niezawodność danych.

### <a name="compression"></a>Kompresja

Producent platformy Kafka można skonfigurować do kompresji wiadomości przed wysłaniem ich do brokerów. Ustawienie `compression.type` określa kodek kompresji, który ma być używany. Obsługiwane kodeki kompresji to "gzip", "snappy" i "lz4". Kompresja jest korzystna i należy rozważyć, jeśli istnieje ograniczenie pojemności dysku.

Wśród dwóch powszechnie używanych kodeków kompresji, `gzip` i `snappy`, `gzip` ma wyższy stopień kompresji, co powoduje mniejsze użycie dysku kosztem wyższego obciążenia procesora. Koder-dekodu `snappy` zapewnia mniejszą kompresję przy mniejszym obciążeniu procesora. Można zdecydować, którego kodeka używać na podstawie ograniczeń dysku brokera lub procesora producenta. `gzip`można kompresować dane z szybkością pięciokrotnie wyższą niż `snappy`.

Użycie kompresji danych zwiększy liczbę rekordów, które mogą być przechowywane na dysku. Może również zwiększyć obciążenie procesora CPU w przypadkach, gdy występuje niezgodność między formatami kompresji używanymi przez producenta i brokera. ponieważ dane muszą być skompresowane przed wysłaniem, a następnie zdekompresowane przed przetworzeniem.

## <a name="broker-settings"></a>Ustawienia brokera

W poniższych sekcjach zostaną wyróżnione niektóre z najważniejszych ustawień, aby zoptymalizować wydajność brokerów Platformy Kafka. Szczegółowe informacje na temat wszystkich ustawień brokera można znaleźć [w dokumentacji apache platformy Kafka dotyczącej konfiguracji producentów.](https://kafka.apache.org/documentation/#producerconfigs)

### <a name="number-of-disks"></a>Liczba dysków

Dyski magazynu mają ograniczone operacje We/Wy operacji wejścia/wyjścia na sekundę i bajtów odczytu/zapisu na sekundę. Podczas tworzenia nowych partycji kafka przechowuje każdą nową partycję na dysku z najmniejszą licznymi istniejącymi partycjami, aby zrównoważyć je na dostępnych dyskach. Pomimo strategii magazynowania podczas przetwarzania setek replik partycji na każdym dysku, platforma Kafka może łatwo nasycić dostępną przepływność dysku. Kompromis w tym miejscu jest między przepływności i kosztów. Jeśli aplikacja wymaga większej przepływności, utwórz klaster z większą liczą dysków zarządzanych na brokera. Usługa HDInsight nie obsługuje obecnie dodawania dysków zarządzanych do uruchomionego klastra. Aby uzyskać więcej informacji na temat konfigurowania liczby dysków zarządzanych, zobacz [Konfigurowanie magazynu i skalowalności dla platformy Apache Kafka w programie HDInsight](apache-kafka-scalability.md). Poznaj wpływ na koszty zwiększenia przestrzeni magazynowej dla węzłów w klastrze.

### <a name="number-of-topics-and-partitions"></a>Liczba tematów i partycji

Producenci Kafki piszą do tematów. Konsumenci platformy Kafka czytają z tematów. Temat jest skojarzony z dziennikiem, który jest strukturą danych na dysku. Platforma Kafka dołącza rekordy od producenta(-ów) na końcu dziennika tematów. Dziennik tematu składa się z wielu partycji, które są rozłożone na wiele plików. Pliki te są z kolei rozłożone na wiele węzłów klastra platformy Kafka. Konsumenci czytają z tematów platformy Kafka w ich rytmie i mogą wybrać ich pozycję (przesunięcie) w dzienniku tematów.

Każda partycja platformy Kafka jest plikiem dziennika w systemie, a wątki producenta mogą zapisywać do wielu dzienników jednocześnie. Podobnie, ponieważ każdy wątek konsumenta odczytuje komunikaty z jednej partycji, zużywanie z wielu partycji jest obsługiwane równolegle, jak również.

Zwiększenie gęstości partycji (liczba partycji na brokera) dodaje obciążenie związane z operacjami metadanych i na żądanie partycji/odpowiedź między liderem partycji i jej zwolenników. Nawet w przypadku braku danych przepływanych przez repliki partycji nadal pobierają dane z liderów, co powoduje dodatkowe przetwarzanie dla wysyłania i odbierania żądań za pośrednictwem sieci.

W przypadku klastrów Apache Kafka 1.1 lub powyżej w umiań HDInsight zalecamy, aby mieć maksymalnie 1000 partycji na brokera, w tym repliki. Zwiększenie liczby partycji na brokera zmniejsza przepływność i może również powodować niedostępność tematu. Aby uzyskać więcej informacji na temat obsługi partycji Platformy Kafka, zobacz [oficjalny wpis na blogu Apache Kafka na temat wzrostu liczby obsługiwanych partycji w wersji 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Aby uzyskać szczegółowe informacje na temat modyfikowania tematów, zobacz [Apache Kafka: modyfikowanie tematów](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Liczba replik

Wyższy współczynnik replikacji powoduje dodatkowe żądania między liderem partycji a obserwatorami. W związku z tym wyższy współczynnik replikacji zużywa więcej dysku i procesora CPU do obsługi dodatkowych żądań, zwiększając opóźnienie zapisu i zmniejszając przepływność.

Zaleca się użycie co najmniej 3-krotnej replikacji dla platformy Kafka w usłudze Azure HDInsight. Większość regionów platformy Azure ma trzy domeny błędów, ale w regionach z tylko dwiema domenami błędów użytkownicy powinni używać replikacji 4x.

Aby uzyskać więcej informacji na temat replikacji, zobacz [Apache Kafka: replication](https://kafka.apache.org/documentation/#replication) i [Apache Kafka: increasing replication factor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Następne kroki

* [Processing trillions of events per day with Apache Kafka on Azure (Przetwarzanie bilionów zdarzeń dziennie za pomocą platformy Apache Kafka na platformie Azure)](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co to jest platforma Apache Kafka w usłudze HDInsight?](apache-kafka-introduction.md)
