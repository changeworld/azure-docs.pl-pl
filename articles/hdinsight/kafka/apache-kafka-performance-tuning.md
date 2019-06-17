---
title: Optymalizacja wydajności w przypadku klastrów Apache Kafka HDInsight
description: Przegląd techniki optymalizacji obciążeń platformy Apache Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689355"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optymalizacja wydajności w przypadku klastrów Apache Kafka HDInsight

Ten artykuł zawiera sugestie dotyczące optymalizacji wydajności obciążeń platformy Apache Kafka w HDInsight. Koncentruje się na dostosowywanie producentów i konfiguracji brokera. Istnieją różne sposoby pomiaru wydajności i optymalizacje, które można zastosować zależy od potrzeb biznesowych.

## <a name="architecture-overview"></a>Omówienie architektury

Tematów platformy Kafka są używane do organizowania rekordów. Rekordy są produkowane przez producentów i używane przez klientów. Producenci wysyłać rekordy do brokerów Kafka, które następnie przechowywania danych. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka.

Tematy dzielą rekordy między brokerami. Podczas korzystania z rekordów można użyć maksymalnie jednego odbiorcy na jedną partycję, aby osiągnąć równoległe przetwarzanie danych.

Replikacja jest używana do duplikowania partycji na węzłach. Chroni to przed awariami węzła (brokera). Jedna partycja w grupie repliki jest wyznaczony jako lidera w partycji. Ruch producenta jest kierowany do partycji wiodącej w każdym węźle przy użyciu stanu zarządzanego przez usługę ZooKeeper.

## <a name="identify-your-scenario"></a>Identyfikowanie własnego scenariusza

Wydajności platformy Apache Kafka ma dwa główne aspekty — przepływności i opóźnień. Przepływność jest maksymalna szybkość danych mogą być przetwarzane. Zazwyczaj lepiej jest większej przepływności. Opóźnienie to czas, potrzebny dla danych do zapisania lub pobrać. Zazwyczaj lepiej jest mniejsze opóźnienie. Znajdowanie równowagę między przepływności, opóźnienia i koszty infrastruktury aplikacji może być wyzwaniem. Wymagań dotyczących wydajności prawdopodobnie będzie zgodna z jedną z następujących trzy typowe sytuacje, w oparciu wymagają wysokiej przepływności, małego opóźnienia i / lub:

* Wysokiej przepływności, małego opóźnienia. Ten scenariusz wymaga wysokiej przepływności i małego opóźnienia (w milisekundach ~ 100). Przykładem tego typu aplikacji jest usługi monitorowania dostępności.
* Wysoka przepływność, duże opóźnienie. Ten scenariusz wymaga wysokiej przepływności (~1.5 GB/s), ale może tolerować wyższy opóźnienie (< 250 ms). Przykładem tego typu aplikacji jest pozyskiwanie danych telemetrii dla niemal w czasie rzeczywistym procesy, takie jak aplikacje wykrywania zabezpieczeń i nieautoryzowanego dostępu.
* Mało przepływność, krótki czas oczekiwania. Ten scenariusz wymaga małe opóźnienia (< 10 ms) na potrzeby przetwarzania w czasie rzeczywistym, ale umożliwia tolerowanie niższe przepływności. Przykładem tego typu aplikacji jest sprawdzania pisowni i gramatyki w trybie online.

## <a name="producer-configurations"></a>Konfiguracje producenta

W poniższych sekcjach zostaną omówiono niektóre z najważniejszych właściwości konfiguracji, aby zoptymalizować wydajność usługi Kafka producentów. Aby uzyskać szczegółowy opis wszystkich właściwości konfiguracji, zobacz [dokumentacja platformy Apache Kafka w konfiguracjach producentów](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Rozmiar partii

Producenci platformy Apache Kafka złóż grup wiadomości (nazywane w partiach), które są wysyłane jako jednostki mają być przechowywane w partycji pojedynczy magazyn. Rozmiar partii oznacza liczbę bajtów, które musi znajdować się przed wysłaniem tej grupy. Zwiększenie `batch.size` parametru może zwiększyć przepływność, ponieważ zmniejsza przetwarzania narzut żądań We/Wy i sieć. W małym obciążeniem rozmiar partii zwiększone może zwiększyć opóźnienie wysyłania platformy Kafka jako producent czeka, aż gotowość zadaniu wsadowym. Pod dużym obciążeniem zaleca się zwiększyć rozmiar partii w celu zwiększenia przepływności i opóźnień.

### <a name="producer-required-acknowledgements"></a>Producent wymaga potwierdzenia

Producent wymaga `acks` konfiguracji określa liczbę potwierdzeń wymagane przez lidera partycji przed żądania zapisu jest uznawany za ukończone. To ustawienie ma wpływ na niezawodność danych i potrzebny wartości `0`, `1`, lub `-1`. Wartość `-1` oznacza, że potwierdzenie muszą dostarczony z wszystkie repliki przed zakończeniem zapisu. Ustawienie `acks = -1` zapewnia lepsze gwarancje przed utratą danych, ale również wyniki w większych opóźnień i przepływności niższe. Jeśli wymagania aplikacji wymagają wyższej przepustowości, spróbuj ustawienie `acks = 0` lub `acks = 1`. Należy pamiętać, który nie potwierdzają jedynie wszystkie repliki może zmniejszyć niezawodności dotyczącej danych.

### <a name="compression"></a>Kompresja

Aby skompresować komunikatów przed ich wysłaniem do brokerów można skonfigurować producenta platformy Kafka. `compression.type` Ustawienie umożliwia określenie kodera-dekodera kompresji ma być używany. Kodery-dekodery kompresji obsługiwanych są "gzip," "snappy," i "lz4." Kompresja jest korzystne i należy rozważyć w przypadku limitu pojemności dysku.

Między dwa kodery-dekodery kompresji powszechnie używane `gzip` i `snappy`, `gzip` ma wyższy stopień kompresji, co skutkuje niższe użycie dysku kosztem wyższe obciążenie procesora CPU. `snappy` Kodera-dekodera umożliwia mniej stopień kompresji i mniejsze obciążenie procesora CPU. Można zdecydować, które kodera-dekodera umożliwia oparte na ograniczenia brokera dysku lub producent procesora CPU. `gzip` można kompresować dane z szybkością pięć razy wyższa niż `snappy`.

Przy użyciu kompresji danych zwiększy liczbę rekordów, które mogą być przechowywane na dysku. To może również zwiększyć Procesora obciążenie w przypadkach, gdzie występuje niezgodność między formatów kompresji używany przez producenta i brokera. jak musi być kompresowane przed wysłaniem i następnie dekompresowane przed rozpoczęciem przetwarzania danych.

## <a name="broker-settings"></a>Ustawienia brokera

W poniższych sekcjach zostaną omówiono niektóre z najważniejszych ustawień w celu zoptymalizowania wydajności usługi brokerów platformy Kafka. Aby uzyskać szczegółowy opis wszystkich brokera ustawień, zobacz [dokumentacja platformy Apache Kafka w konfiguracjach producentów](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Liczba dysków

Dyski magazynu mają ograniczone operacje We/Wy (dane wejściowe i wyjściowe operacji na sekundę) i odczyt/zapis bajtów na sekundę. Podczas tworzenia nowych partycji, platforma Kafka przechowuje każdej nowej partycji na dysk z najmniejszą liczbą istniejące partycje w celu ich zrównoważenia różnych dostępnych dysków. Pomimo strategię magazynowania, podczas przetwarzania kilkuset replik partycji na poszczególnych dyskach Kafka można łatwo saturate przepustowości dostępnego miejsca na dysku. Kosztem, w tym miejscu znajduje się między przepływność i kosztów. Jeśli aplikacja wymaga większej przepływności, Utwórz klaster z bardziej zarządzanych dysków na brokera. HDInsight aktualnie nie obsługuje dodawania dysków zarządzanych do działającego klastra. Aby uzyskać więcej informacji na temat konfigurowania liczbę dysków zarządzanych, zobacz [Konfigurowanie magazynu i skalowalności klastra Apache kafka w HDInsight](apache-kafka-scalability.md). Poznanie skutków koszt zwiększa miejsca do magazynowania dla węzłów w klastrze.

### <a name="number-of-topics-and-partitions"></a>Liczba tematów i partycji

Producenci Kafka zapisu do tematów. Odbiorcy platformy Kafka są odczytywane z tematów. Temat jest skojarzony z dziennika, która jest strukturą danych na dysku. Platforma Kafka dołącza rekordów z producentów do końca dziennika tematu. Dziennik tematu składa się z wielu partycji, które jest rozłożona na wiele plików. Te pliki z kolei rozkładają się na wielu węzłach klastra Kafka. Konsumenci odczytywać tematów platformy Kafka w ich cykl i wybrać ich pozycji (przesunięciem) w dzienniku tematu.

Każdej partycji platformy Kafka jest plik dziennika w systemie i wątki producentów mogą zapisywać dane w wielu dzienników jednocześnie. Podobnie ponieważ każdy wątek odbiorców odczytuje komunikaty z jednej partycji, korzystanie z różnych partycji odbywa się również równolegle.

Zwiększenie gęstości partycji (liczba partycji na broker) dodaje obciążenie związane z operacji na metadanych i poszczególnych partycji żądania/odpowiedzi między lidera partycji i jego obserwatorów. Nawet w przypadku braku dane przepływają przez replik partycji nadal pobrać dane z liderów, co skutkuje dodatkowego przetwarzania wysyłania i odbierania żądań za pośrednictwem sieci.

Apache Kafka klastrów 1.1 powyżej w HDInsight, zaleca się do maksymalnie 1000 partycji przypadających na brokera, łącznie z repliki. Zwiększenie liczby partycji przypadających na brokera zmniejsza przepustowość i może również spowodować niedostępność tematu. Aby uzyskać więcej informacji dotyczących obsługi różnych partycji platformy Kafka, zobacz [oficjalne wpis w blogu platformy Apache Kafka wzrost liczby partycji obsługiwane w wersji 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Aby uzyskać więcej informacji na temat modyfikowania tematów, zobacz [platformy Apache Kafka: modyfikowanie tematy](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Liczba replik

Wyższy współczynnik replikacji powoduje dodatkowe żądania między lidera partycji i obserwatorów. W związku z tym, wyższy współczynnik replikacji zużywa więcej dysku i procesora CPU do obsługi dodatkowych żądań zwiększenia zapisu opóźnienia i zmniejszenie przepływności.

Zalecamy użycie co najmniej 3 x replikacji dla platformy Kafka w usłudze Azure HDInsight. Większości regionów platformy Azure mają trzy domeny błędów, ale w regionach z tylko dwie domeny błędów, użytkownicy powinni używać 4 x replikacji.

Aby uzyskać więcej informacji na temat replikacji, zobacz [platformy Apache Kafka: replikacja](https://kafka.apache.org/documentation/#replication) i [platformy Apache Kafka: zwiększenie współczynnika replikacji](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Kolejne kroki

* [Processing trillions of events per day with Apache Kafka on Azure (Przetwarzanie bilionów zdarzeń dziennie za pomocą platformy Apache Kafka na platformie Azure)](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co to jest platforma Apache Kafka w HDInsight?](apache-kafka-introduction.md)
