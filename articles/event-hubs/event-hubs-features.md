---
title: Omówienie funkcji — Usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje o funkcjach i terminologia usługi Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357082"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkcje i terminologią dotyczącą usługi Azure Event Hubs

Usługa Azure Event Hubs jest skalowalna Usługa przetwarzania zdarzeń, która pozyskuje i przetwarza duże ilości zdarzenia i dane, z małymi opóźnieniami i wysoką niezawodnością. Zobacz [co to jest Event Hubs?](event-hubs-what-is-event-hubs.md) , aby zapoznać się z ogólnym omówieniem.

W tym artykule opisano informacje zawarte w [artykule Omówienie](event-hubs-what-is-event-hubs.md)i przedstawiono techniczne i szczegółowe informacje dotyczące Event Hubs składników i funkcji.

## <a name="namespace"></a>Przestrzeń nazw
Przestrzeń nazw Event Hubs zapewnia unikatowy kontener określania zakresu, do którego odwołuje się jego w [pełni kwalifikowana nazwa domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), w którym można utworzyć jeden lub więcej centrów zdarzeń lub tematów Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Usługa Event Hubs dla platformy Apache Kafka

[Ta funkcja](event-hubs-for-kafka-ecosystem-overview.md) udostępnia punkt końcowy, który umożliwia klientom komunikowanie się z Event Hubs przy użyciu protokołu Kafka. Ta integracja zapewnia klientom punktu końcowego platformy Kafka. Dzięki temu klienci mogą konfigurować swoje istniejące aplikacje platformy Kafka na komunikowanie się z usługi Event Hubs, zapewniając alternatywa działające własne klastry platformy Kafka. Usługa Event Hubs dla platformy Apache Kafka obsługuje protokół platformy Kafka, 1.0 lub nowszy. 

W ramach tej integracji nie trzeba uruchamiać klastrów Kafka ani zarządzać nimi za pomocą dozorcy. Umożliwia to także pracować z niektórych funkcji najbardziej wymagających centrów zdarzeń, takich jak przechwytywanie automatyczne rozszerzanie i odzyskiwania po awarii geograficznie.

Ta integracja umożliwia również aplikacji, takich jak twórca dublowanie lub środowiska takiego jak połączyć Kafka pracę clusterless tylko zmian konfiguracji. 

## <a name="event-publishers"></a>Wydawcy zdarzeń

Każda jednostka, która wysyła dane do centrum zdarzeń, jest producentem zdarzeń lub *wydawcą zdarzeń*. Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołu HTTPS lub AMQP 1.0 lub platformy Kafka w wersji 1.0 lub nowszy. Wydawcy zdarzeń używają tokenu sygnatury dostępu współdzielonego w celu identyfikowania siebie w centrum zdarzeń i mogą mieć unikatową tożsamość lub używają typowego tokenu sygnatury dostępu współdzielonego.

### <a name="publishing-an-event"></a>Publikowanie zdarzenia

Można opublikować zdarzenia za pośrednictwem protokołu AMQP 1.0, platformy Kafka w wersji 1.0 (lub nowszy) lub HTTPS. Event Hubs udostępnia [biblioteki i klasy klienta](event-hubs-dotnet-framework-api-overview.md) do publikowania zdarzeń w centrum zdarzeń z klientów platformy .NET. W przypadku innych środowisk uruchomieniowych i platform można używać dowolnego klienta protokołu AMQP 1.0, na przykład [Apache Qpid](https://qpid.apache.org/). Zdarzenia można publikować indywidualnie lub w partiach. Jedna publikacja (wystąpienie danych zdarzeń) ma limit 1 MB, niezależnie od tego, czy jest to pojedyncze zdarzenie, czy partia. Publikowanie zdarzeń jest większy niż próg powoduje wystąpienie błędu. Najlepszym rozwiązaniem dla wydawców jest niebranie pod uwagę partycji w ramach centrum zdarzeń i określenie jedynie *klucza partycji* (zostanie wprowadzony w następnej sekcji) lub tożsamości za pomocą ich tokenu sygnatury dostępu współdzielonego.

Decyzja o korzystaniu z protokołu AMQP lub HTTPS jest specyficzna dla scenariusza użycia. Protokół AMQP wymaga ustanowienia trwałego gniazda dwukierunkowego oprócz protokołu TLS lub SSL/ TLS. Protokół AMQP zużywa więcej zasobów sieciowych przy inicjowaniu sesji, jednak protokół HTTPS wymaga dla każdego żądania dodatkowego narzutu na protokół SSL. Protokół AMQP charakteryzują się wyższą wydajnością dla częstych wydawców.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Usługa Event Hubs zapewnia, że wszystkie zdarzenia współużytkujące wartość klucza partycji są poprawnie dostarczane na tę samą partycję. Jeśli klucze partycji są używane wraz z zasadami wydawcy, to tożsamość wydawcy i wartość klucza partycji muszą być zgodne. W przeciwnym razie wystąpi błąd.

### <a name="publisher-policy"></a>Zasady wydawcy

Usługa Event Hubs umożliwia szczegółową kontrolę nad wydawcami zdarzeń za pomocą *zasad wydawcy*. Zasady wydawcy to funkcje środowiska uruchomieniowego zaprojektowane w celu ułatwienia działania dużej liczby niezależnych wydawców zdarzeń. Dzięki zasadom wydawcy każdy wydawca używa swojego unikatowego identyfikatora podczas publikowania zdarzeń w centrum zdarzeń przy użyciu następującego mechanizmu:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Nie jest konieczne wcześniejsze tworzenie nazw wydawców, ale muszą one być zgodne z tokenem sygnatury dostępu współdzielonego użytym podczas publikowania zdarzenia w celu zapewnienia niezależnych tożsamości wydawcy. Podczas używania zasad wydawcy wartość **PartitionKey** jest ustawiana na nazwę wydawcy. Aby zapewnić prawidłowe działanie, te wartości muszą być zgodne.

## <a name="capture"></a>Przechwytywanie

[Przechwytywanie Event Hubs](event-hubs-capture-overview.md) umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo w Event Hubs i zapisywanie ich w wybranym przez siebie koncie usługi BLOB Storage lub koncie usług Azure Data Lake. Możesz włączyć funkcję przechwytywania w witrynie Azure portal i określ przedział czasu, aby wykonać przechwytywania i minimalnego rozmiaru. Korzystając z usługi Event Hubs Capture, określić własnego konta usługi Azure Blob Storage i kontener lub konto z usługą Azure Data Lake, jednym z nich jest używany do przechowywania przechwyconych danych. Przechwycone dane są zapisywane w formacie Apache Avro.

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Usługa Event Hubs używa *sygnatur dostępu współdzielonego*, które są dostępne na poziomie przestrzeni nazw i centrum zdarzeń. Token sygnatury dostępu współdzielonego jest generowany na podstawie klucza sygnatury dostępu współdzielonego i jest skrótem SHA adresu URL zakodowanym w określonym formacie. Przy użyciu nazwy klucza (zasady) i tokenu usługa Event Hubs może ponownie wygenerować skrót i w ten sposób uwierzytelnić nadawcę. Zwykle tokeny sygnatury dostępu współdzielonego dla wydawców zdarzeń są tworzone jedynie z uprawnieniami do **wysyłania** w określonym centrum zdarzeń. Ten mechanizm adresu URL tokenu sygnatury dostępu współdzielonego stanowi podstawę do identyfikacji wydawcy wprowadzoną w ramach zasad wydawcy. Aby uzyskać więcej informacji na temat pracy z sygnaturą dostępu współdzielonego, zobacz [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego przy użyciu usługi Service Bus).

## <a name="event-consumers"></a>Odbiorcy zdarzeń

Każda jednostka, która odczytuje dane zdarzenia z centrum zdarzeń, jest *odbiorcą zdarzenia*. Wszyscy odbiorcy usługi Event Hubs nawiązują połączenie za pomocą sesji protokołu AMQP 1.0, w ramach której zdarzenia są dostarczane, gdy tylko staną się dostępne. Klient nie musi sondować dostępności danych.

### <a name="consumer-groups"></a>Grupy odbiorców

Mechanizm publikowania/subskrypcji usługi Event Hubs jest włączany za pomocą *grup odbiorców*. Grupa odbiorców stanowi widok (stan, pozycja lub przesunięcie) całego centrum zdarzeń. Dzięki grupom odbiorców wiele aplikacji odbiorczych może mieć osobny widok strumienia zdarzeń i niezależnie odczytywać strumień we własnym tempie i przy użyciu własnego przesunięcia.

W architekturze przetwarzania strumieni każda aplikacja podrzędna odpowiada grupie odbiorców. Jeśli chcesz zapisać dane zdarzenia do magazynu długoterminowego, to ta aplikacja edytora magazynu odpowiada grupie odbiorców. Przetwarzanie złożonych zdarzeń może być wtedy wykonywane przez inną, oddzielną grupę odbiorców. Dostęp do partycji można uzyskać tylko za pośrednictwem grupy odbiorców. W centrum zdarzeń zawsze istnieje domyślna grupa odbiorców, a w przypadku centrum zdarzeń warstwy Standardowa można utworzyć maksymalnie 20 grup odbiorców.

Na partycji dla każdej grupy odbiorców może znajdować się maksymalnie 5 współbieżnych czytników. jednak **zaleca się, aby tylko jeden aktywny odbiornik został skonfigurowany na partycji dla każdej grupy odbiorców**. W ramach jednej partycji każdy czytnik odbiera wszystkie komunikaty. Jeśli masz wielu elementów odczytujących na tej samej partycji, możesz przetwarzać zduplikowanych komunikatów. Wymagana jest obsługa to w kodzie, które nie mogą być proste. Jest jednak nieprawidłowa podejście w niektórych scenariuszach.


Oto przykłady konwencji identyfikatora URI grupy odbiorców:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Na poniższym rysunku przedstawiono architekturę przetwarzania strumienia usługi Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Przesunięcia strumienia

*Przesunięcie* to pozycja zdarzenia w partycji. Przesunięcie można traktować jako kursor po stronie klienta. Przesunięcie to numer bajtu zdarzenia. To przesunięcie umożliwi odbiorcy zdarzeń (czytnikowi) określenie punktu w strumieniu zdarzeń, od którego ma zostać rozpoczęte odczytywanie zdarzeń. Przesunięcie można określić jako sygnaturę czasową lub wartość przesunięcia. Odbiorcy są zobowiązani do przechowywania własnych wartości przesunięcia poza usługą Event Hubs. W ramach partycji każde zdarzenie zawiera przesunięcie.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Tworzenie punktów kontrolnych

*Tworzenie punktów kontrolnych* jest procesem, za pomocą którego czytniki oznaczają lub zatwierdzają swoją pozycję w sekwencji zdarzeń partycji. Odpowiedzialność za tworzenie punktów kontrolnych spoczywa na odbiorcy i odbywa się dla każdej partycji w ramach grupy odbiorców. Ta odpowiedzialność oznacza, że dla każdej grupy odbiorców każdy czytnik partycji musi śledzić swoją bieżącą pozycję w strumieniu zdarzeń i może poinformować usługi, gdy uzna, że strumień danych jest pełny.

Jeśli czytnik rozłączy się od partycji, po swoim ponownym połączeniu rozpoczyna odczyt punktu kontrolnego, który został wcześniej przesłany przez ostatni czytnik tej partycji w danej grupie odbiorców. Gdy czytnik nawiązuje połączenie, przekazuje on to przesunięcie do Centrum zdarzeń, aby określić lokalizację, w której należy rozpocząć odczyt. W ten sposób można użyć procesu tworzenia punktów kontrolnych zarówno do oznaczenia zdarzeń jako „ukończone” przez aplikacje podrzędne, jak i zapewnienia odporności zdarzenia na pracę w trybie failover między czytnikami działającymi na różnych komputerach. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia od tego procesu tworzenia punktów kontrolnych. Dzięki temu mechanizmowi tworzenie punktów kontrolnych zapewnia zarówno odporność na pracę w trybie failover, jak i powtarzanie strumienia zdarzeń.

### <a name="common-consumer-tasks"></a>Typowe zadania odbiorców

Wszyscy odbiorcy usługi Event Hubs połączenie za pomocą sesji protokołu AMQP 1.0, stan dwukierunkowy kanał komunikacyjny. Każda partycja zawiera sesję protokołu AMQP 1.0, która ułatwia transport zdarzeń posegregowanych według partycji.

#### <a name="connect-to-a-partition"></a>Nawiązywanie połączenia z partycją

Częstą praktyką jest, aby podczas nawiązywania połączenia z partycjami używany był mechanizm dzierżawienia w celu skoordynowania połączeń czytników z określonymi partycjami. Dzięki temu możliwe jest, aby dla każdej partycji w grupie odbiorców istniał tylko jeden aktywny czytnik. Tworzenie punktów kontrolnych, dzierżawienie i zarządzanie czytnikami jest upraszczane przez użycie klasy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) dla klientów programu .NET. Host procesora zdarzeń to inteligentny agent odbiorcy.

#### <a name="read-events"></a>Zdarzenia odczytywania

Po otwarciu sesji i linku protokołu AMQP 1.0 dla określonej partycji zdarzenia są dostarczane do klienta protokołu AMQP 1.0 przez usługę Event Hubs. Ten mechanizm dostarczania zapewnia wyższą przepływność i mniejsze opóźnienia niż mechanizmy oparte na ściąganiu, na przykład HTTP GET. Podczas wysyłania zdarzeń do klienta każde wystąpienie danych zdarzenia zawiera ważne metadane, takie jak przesunięcie i numer sekwencji, które są używane do ułatwienia tworzenia punktów kontrolnych sekwencji zdarzeń.

Dane zdarzenia:
* Przesunięcie
* Numer sekwencyjny
* Treść
* Właściwości użytkownika
* Właściwości systemu

Zarządzanie przesunięciem jest Twoim obowiązkiem.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady Event Hubs][]

[Przykłady Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
