---
title: Omówienie funkcji usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Omówienie i szczegółowe informacje o funkcjach usługi Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: sethm
ms.openlocfilehash: f16f8aa73ecfa3e0a47ce2373a2e28a7a9968ff5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248745"
---
# <a name="event-hubs-features-overview"></a>Omówienie funkcji usługi Event Hubs

Usługa Azure Event Hubs jest skalowalna zdarzeń, usługa, która wysyła strumień i przetwarza duże ilości zdarzenia i dane, z małymi opóźnieniami i wysoką niezawodnością przetwarzania. Zobacz [co to jest usługa Event Hubs?](event-hubs-what-is-event-hubs.md) szczegółowe omówienie.

W tym artykule opiera się na informacje zawarte w [artykuł z omówieniem](event-hubs-what-is-event-hubs.md)i zawiera szczegółowe informacje techniczne i wdrożenia o składnikach usługi Event Hubs i funkcje.

## <a name="event-publishers"></a>Wydawcy zdarzeń

Każda jednostka, która wysyła dane do Centrum zdarzeń jest producentem zdarzeń, lub *wydawca zdarzeń*. Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołu HTTPS lub AMQP 1.0. Wydawcy zdarzeń używają tokenu sygnatury dostępu współdzielonego w celu identyfikowania siebie w centrum zdarzeń i mogą mieć unikatową tożsamość lub używają typowego tokenu sygnatury dostępu współdzielonego.

### <a name="publishing-an-event"></a>Publikowanie zdarzenia

Zdarzenie można opublikować za pośrednictwem protokołu AMQP 1.0 lub HTTPS. Usługa Event Hubs zapewnia [bibliotek klienta i klasy](event-hubs-dotnet-framework-api-overview.md) do publikowania zdarzeń w Centrum zdarzeń od klientów platformy .NET. W przypadku innych środowisk uruchomieniowych i platform można używać dowolnego klienta protokołu AMQP 1.0, na przykład [Apache Qpid](http://qpid.apache.org/). Zdarzenia można publikować indywidualnie lub w partiach. Jedna publikacja (wystąpienie danych zdarzeń) ma limit wynoszący 256 KB, niezależnie od tego, czy jest to pojedyncze zdarzenie, czy partia. Publikowanie zdarzenia większych niż ten próg powoduje błąd. Najlepszym rozwiązaniem dla wydawców jest niebranie pod uwagę partycji w ramach centrum zdarzeń i określenie jedynie *klucza partycji* (zostanie wprowadzony w następnej sekcji) lub tożsamości za pomocą ich tokenu sygnatury dostępu współdzielonego.

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

[Przechwyć centra zdarzeń](event-hubs-capture-overview.md) umożliwia automatycznie przechwytywania dane przesyłane strumieniowo w centra zdarzeń i zapisz go na wybranym koncie magazynu obiektów Blob lub konta usługi Azure Data Lake usługi. Można włączyć przechwytywania w portalu Azure, a następnie określ minimalny rozmiar i przedział czasu, aby wykonać przechwytywania. Przy użyciu przechwytywania centra zdarzeń, określ własne konta magazynu obiektów Blob Azure i kontener lub konta usługi Azure Data Lake usługi, z których jeden jest używany do przechowywania przechwyconych danych. Przechwycone dane są zapisywane w formacie Apache Avro.

## <a name="partitions"></a>Partycje

Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów.

Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

![Event Hubs](./media/event-hubs-features/partition.png)

Centra zdarzeń przechowuje dane przez skonfigurowany czas przechowywania, która dotyczy wszystkich partycji w zdarzeniu Centrum. Zdarzenia wygasają czasowo — nie można ich jawnie usunąć. Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczby partycji nie można zmieniać, dlatego ustawiając liczbę partycji, trzeba planować długoterminowo. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Możesz zwiększyć liczbę partycji ponad 32, kontaktując się z zespołem ds. usługi Event Hubs.

Partycje są możliwe do identyfikacji i mogą być wysyłane bezpośrednio do, wysyłanie bezpośrednio do partycji nie jest zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu przedstawionych w sekcjach [Wydawca zdarzeń](#event-publishers) i [Pojemność](#capacity). 

Partycje są wypełnione sekwencją danych zdarzenia, które obejmują treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości oraz metadane, takie jak przesunięcie zdarzenia w partycji i jego numer w sekwencji strumienia.

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

W architekturze przetwarzania strumieni każda aplikacja podrzędna odpowiada grupie odbiorców. Jeśli chcesz zapisać dane zdarzenia do magazynu długoterminowego, to ta aplikacja edytora magazynu odpowiada grupie odbiorców. Przetwarzanie złożonych zdarzeń może być wtedy wykonywane przez inną, oddzielną grupę odbiorców. Dostęp do partycji można uzyskać tylko za pośrednictwem grupy odbiorców. Może istnieć maksymalnie 5 współbieżnych czytników na partycji dla każdej grupy konsumentów; jednak **zaleca się, że istnieje tylko jeden odbiornik active na partycji dla każdej grupy odbiorców**. W centrum zdarzeń zawsze istnieje domyślna grupa odbiorców, a w przypadku centrum zdarzeń warstwy Standardowa można utworzyć maksymalnie 20 grup odbiorców.

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

Jeśli czytnik rozłączy się od partycji, po swoim ponownym połączeniu rozpoczyna odczyt punktu kontrolnego, który został wcześniej przesłany przez ostatni czytnik tej partycji w danej grupie odbiorców. Gdy czytnik nawiązuje połączenie, przekazuje on to przesunięcie do centrum zdarzeń, aby określić lokalizację, w której należy rozpocząć odczyt. W ten sposób można użyć procesu tworzenia punktów kontrolnych zarówno do oznaczenia zdarzeń jako „ukończone” przez aplikacje podrzędne, jak i zapewnienia odporności zdarzenia na pracę w trybie failover między czytnikami działającymi na różnych komputerach. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia od tego procesu tworzenia punktów kontrolnych. Dzięki temu mechanizmowi tworzenie punktów kontrolnych zapewnia zarówno odporność na pracę w trybie failover, jak i powtarzanie strumienia zdarzeń.

### <a name="common-consumer-tasks"></a>Typowe zadania odbiorców

Wszystkie usługi Event Hubs konsumentów połączenie za pomocą sesji protokołu AMQP 1.0, stan dwukierunkowy kanał komunikacji. Każda partycja zawiera sesję protokołu AMQP 1.0, która ułatwia transport zdarzeń posegregowanych według partycji.

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

## <a name="capacity"></a>Pojemność

Usługa Event Hubs ma wysoce skalowalną architekturę równoległą i istnieje kilka kluczowych czynników, które należy rozważyć podczas określania rozmiaru i skalowania.

### <a name="throughput-units"></a>Jednostki przepływności

Pojemność przepływności usługi Event Hubs jest kontrolowana przez *jednostki przepływności*. Jednostki przepływności to zakupione wcześniej jednostki pojemności. Pojedyncza jednostka przepływności ma następującą pojemność:

* Ruch przychodzący: 1 MB na sekundę lub 1000 zdarzeń na sekundę (w zależności od tego, co będzie miało miejsce wcześniej)
* Ruch wychodzący: maksymalnie 2 MB na sekundę

Po przekroczeniu pojemności zakupionych jednostek przepływności ruch przychodzący jest ograniczany i jest zwracany wyjątek [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Ruch wychodzący nie generuje wyjątków ograniczania, ale też jest ograniczony do pojemności zakupionych jednostek przepływności. Jeśli wystąpią wyjątki szybkości publikowania lub oczekiwany będzie większy transfer danych wychodzących, należy sprawdzić liczbę jednostek przepływności zakupionych dla przestrzeni nazw. Jednostki przepływności można zarządzać na **skali** bloku przestrzeni nazw w [portalu Azure](https://portal.azure.com). Można również zarządzać programowo przy użyciu jednostek przepływności [interfejsów API centra zdarzeń](event-hubs-api-overview.md).

Jednostki przepływności to zakupione wcześniej i są rozliczane co godzinę. Po zakupieniu jednostki przepływności są rozliczane za co najmniej jedną godzinę. Przepływność maksymalnie 20 jednostek można zakupić dla przestrzeni nazw usługi Event Hubs i są współdzielone przez wszystkie usługi event hubs w tej przestrzeni nazw.

Więcej jednostek przepływności można kupić w blokach po 20 (maksymalnie 100 jednostek przepływności), kontaktując się z pomocą techniczną platformy Azure. Ponadto możesz kupować bloki po 100 jednostek przepływności.

Zaleca się, że Równoważenie jednostek przepływności i partycji w celu osiągnięcia optymalnej skali. Jedna partycja ma maksymalną skalę wynoszącą jedną jednostkę przepływności. Liczba jednostek przepływności powinna być mniejsza lub równa liczbie partycji w ramach centrum zdarzeń.

Aby uzyskać szczegółowe informacje o cenach za korzystanie z usługi Event Hubs, zobacz [Usługa Event Hubs — cennik](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Wprowadzenie do [usługi Event Hubs — samouczek][Event Hubs tutorial]
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady centra zdarzeń][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Przykłady centra zdarzeń]: https://github.com/Azure/azure-event-hubs/tree/master/samples
