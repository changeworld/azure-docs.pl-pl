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
ms.openlocfilehash: 5b2618807a39f20de041a78204dcc40793b22843
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275439"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkcje i terminologią dotyczącą usługi Azure Event Hubs

Usługa Azure Event Hubs jest skalowalna Usługa przetwarzania zdarzeń, która pozyskuje i przetwarza duże ilości zdarzenia i dane, z małymi opóźnieniami i wysoką niezawodnością. Zobacz [co to jest usługa Event Hubs?](event-hubs-what-is-event-hubs.md) zapoznać się z omówieniem wysokiego poziomu.

W tym artykule opiera się na informacjach w [artykuł z omówieniem](event-hubs-what-is-event-hubs.md)oraz szczegółowe informacje techniczne i wdrożenia o składnikach usługi Event Hubs i funkcje.

## <a name="namespace"></a>Przestrzeń nazw
Przestrzeń nazw usługi Event Hubs zapewnia kontener określania zakresu unikatowy, odwołuje się jego [w pełni kwalifikowaną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), w którym tworzysz usługi event hubs lub tematów platformy Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Usługa Event Hubs dla platformy Apache Kafka

[Ta funkcja](event-hubs-for-kafka-ecosystem-overview.md) udostępnia punkt końcowy, który umożliwia klientom na komunikowanie się z usługi Event Hubs przy użyciu protokołu platformy Kafka. Ta integracja zapewnia klientom punktu końcowego platformy Kafka. Dzięki temu klienci mogą konfigurować swoje istniejące aplikacje platformy Kafka na komunikowanie się z usługi Event Hubs, zapewniając alternatywa działające własne klastry platformy Kafka. Usługa Event Hubs dla platformy Apache Kafka obsługuje protokół platformy Kafka, 1.0 lub nowszy. 

Dzięki tej integracji nie trzeba uruchomić klastry platformy Kafka lub zarządzać nimi przy użyciu dozorcy. Umożliwia to także pracować z niektórych funkcji najbardziej wymagających centrów zdarzeń, takich jak przechwytywanie automatyczne rozszerzanie i odzyskiwania po awarii geograficznie.

Ta integracja umożliwia również aplikacji, takich jak twórca dublowanie lub środowiska takiego jak połączyć Kafka pracę clusterless tylko zmian konfiguracji. 

## <a name="event-publishers"></a>Wydawcy zdarzeń

Każda jednostka, która wysyła dane do Centrum zdarzeń jest producentem zdarzeń, lub *wydawca zdarzeń*. Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołu HTTPS lub AMQP 1.0 lub platformy Kafka w wersji 1.0 lub nowszy. Wydawcy zdarzeń używają tokenu sygnatury dostępu współdzielonego w celu identyfikowania siebie w centrum zdarzeń i mogą mieć unikatową tożsamość lub używają typowego tokenu sygnatury dostępu współdzielonego.

### <a name="publishing-an-event"></a>Publikowanie zdarzenia

Można opublikować zdarzenia za pośrednictwem protokołu AMQP 1.0, platformy Kafka w wersji 1.0 (lub nowszy) lub HTTPS. Usługa Event Hubs zapewnia [bibliotek klienta i klasy](event-hubs-dotnet-framework-api-overview.md) do publikowania zdarzeń do Centrum zdarzeń z klientów programu .NET. W przypadku innych środowisk uruchomieniowych i platform można używać dowolnego klienta protokołu AMQP 1.0, na przykład [Apache Qpid](https://qpid.apache.org/). Zdarzenia można publikować indywidualnie lub w partiach. Jedna publikacja (wystąpienie danych zdarzeń) ma limit 1 MB, niezależnie od tego, czy jest to pojedyncze zdarzenie, czy partia. Publikowanie zdarzeń jest większy niż próg powoduje wystąpienie błędu. Najlepszym rozwiązaniem dla wydawców jest niebranie pod uwagę partycji w ramach centrum zdarzeń i określenie jedynie *klucza partycji* (zostanie wprowadzony w następnej sekcji) lub tożsamości za pomocą ich tokenu sygnatury dostępu współdzielonego.

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

[Funkcja przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) umożliwia automatyczne Przechwytywanie przesyłania strumieniowego danych w usłudze Event Hubs i zapisz go na wybór konta usługi Blob storage lub konto z usługą Azure Data Lake. Możesz włączyć funkcję przechwytywania w witrynie Azure portal i określ przedział czasu, aby wykonać przechwytywania i minimalnego rozmiaru. Korzystając z usługi Event Hubs Capture, określić własnego konta usługi Azure Blob Storage i kontener lub konto z usługą Azure Data Lake, jednym z nich jest używany do przechowywania przechwyconych danych. Przechwycone dane są zapisywane w formacie Apache Avro.

## <a name="partitions"></a>Partycje

Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów.

Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

![Event Hubs](./media/event-hubs-features/partition.png)

Usługa Event Hubs przechowuje dane przez skonfigurowany czas przechowywania, stosowany do wszystkich partycji w Centrum zdarzeń. Zdarzenia wygasają czasowo — nie można ich jawnie usunąć. Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczby partycji nie można zmieniać, dlatego ustawiając liczbę partycji, trzeba planować długoterminowo. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Możesz zwiększyć liczbę partycji ponad 32, kontaktując się z zespołem ds. usługi Event Hubs.

Chociaż partycje są identyfikowalne i mogą być wysyłane bezpośrednio do, wysyłając bezpośrednio do partycji nie jest zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu wprowadzonych w [wydawca zdarzeń](#event-publishers) i sekcje pojemności. 

Partycje są wypełnione sekwencją danych zdarzenia, które obejmują treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości oraz metadane, takie jak jego przesunięcie w partycji i jego numer w sekwencji strumienia.

Aby uzyskać więcej informacji na temat partycji i równowagi między dostępnością i niezawodnością, zobacz [Przewodnik dotyczący programowania w usłudze Event Hubs](event-hubs-programming-guide.md#partition-key) i artykuł [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs).

### <a name="partition-key"></a>Klucz partycji

[Klucz partycji](event-hubs-programming-guide.md#partition-key) służy do mapowania danych zdarzeń przychodzących na określone partycje na potrzeby organizowania danych. Klucz partycji to wartość podawana przez nadawcę przekazywana do centrum zdarzeń. Jest on przetwarzany przez statyczną funkcję tworzenia skrótu, za pomocą której tworzone jest przypisanie partycji. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego.

Wydawca zdarzeń ma informacje tylko o kluczu partycji, a nie partycji, na której publikowane są zdarzenia. To oddzielenie klucza od partycji powoduje, że nadawca nie musi wiedzieć zbyt dużo o przetwarzaniu podrzędnym. Unikatowa tożsamość urządzenia lub użytkownika stanowi dobry klucz partycji, ale inne atrybuty, takie jak lokalizacja geograficzna, mogą również zostać użyte do grupowania powiązanych zdarzeń w jedną partycję.

## <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Usługa Event Hubs używa *sygnatur dostępu współdzielonego*, które są dostępne na poziomie przestrzeni nazw i centrum zdarzeń. Token sygnatury dostępu współdzielonego jest generowany na podstawie klucza sygnatury dostępu współdzielonego i jest skrótem SHA adresu URL zakodowanym w określonym formacie. Przy użyciu nazwy klucza (zasady) i tokenu usługa Event Hubs może ponownie wygenerować skrót i w ten sposób uwierzytelnić nadawcę. Zwykle tokeny sygnatury dostępu współdzielonego dla wydawców zdarzeń są tworzone jedynie z uprawnieniami do **wysyłania** w określonym centrum zdarzeń. Ten mechanizm adresu URL tokenu sygnatury dostępu współdzielonego stanowi podstawę do identyfikacji wydawcy wprowadzoną w ramach zasad wydawcy. Aby uzyskać więcej informacji na temat pracy z sygnaturą dostępu współdzielonego, zobacz [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego przy użyciu usługi Service Bus).

## <a name="event-consumers"></a>Odbiorcy zdarzeń

Każda jednostka, która odczytuje dane zdarzenia z centrum zdarzeń, jest *odbiorcą zdarzenia*. Wszyscy odbiorcy usługi Event Hubs nawiązują połączenie za pomocą sesji protokołu AMQP 1.0, w ramach której zdarzenia są dostarczane, gdy tylko staną się dostępne. Klient nie musi sondować dostępności danych.

### <a name="consumer-groups"></a>Grupy odbiorców

Mechanizm publikowania/subskrypcji usługi Event Hubs jest włączany za pomocą *grup odbiorców*. Grupa odbiorców stanowi widok (stan, pozycja lub przesunięcie) całego centrum zdarzeń. Dzięki grupom odbiorców wiele aplikacji odbiorczych może mieć osobny widok strumienia zdarzeń i niezależnie odczytywać strumień we własnym tempie i przy użyciu własnego przesunięcia.

W architekturze przetwarzania strumieni każda aplikacja podrzędna odpowiada grupie odbiorców. Jeśli chcesz zapisać dane zdarzenia do magazynu długoterminowego, to ta aplikacja edytora magazynu odpowiada grupie odbiorców. Przetwarzanie złożonych zdarzeń może być wtedy wykonywane przez inną, oddzielną grupę odbiorców. Dostęp do partycji można uzyskać tylko za pośrednictwem grupy odbiorców. W centrum zdarzeń zawsze istnieje domyślna grupa odbiorców, a w przypadku centrum zdarzeń warstwy Standardowa można utworzyć maksymalnie 20 grup odbiorców.

Może istnieć maksymalnie 5 współbieżnych czytników na partycji dla każdej grupy odbiorców; jednak **zaleca się, że na partycji dla każdej grupy odbiorców jest tylko jeden aktywny odbiornik**. W ramach jednej partycji każdy czytnik odbiera wszystkie komunikaty. Jeśli masz wielu elementów odczytujących na tej samej partycji, możesz przetwarzać zduplikowanych komunikatów. Wymagana jest obsługa to w kodzie, które nie mogą być proste. Jest jednak nieprawidłowa podejście w niektórych scenariuszach.


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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs][Event Hubs tutorial]
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady usługi Event Hubs][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Przykłady usługi Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
