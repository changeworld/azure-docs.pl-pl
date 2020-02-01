---
title: Równoważenie obciążenia partycji w wielu wystąpieniach — Event Hubs platformy Azure | Microsoft Docs
description: Opisuje sposób równoważenia obciążenia partycji w wielu wystąpieniach aplikacji przy użyciu procesora zdarzeń i zestawu Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907658"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Równoważenie obciążenia partycji w wielu wystąpieniach aplikacji
Aby skalować aplikację do przetwarzania zdarzeń, można uruchomić wiele wystąpień aplikacji i zrównoważyć obciążenie między sobą. W starszych wersjach [klasy eventprocessorhost](event-hubs-event-processor-host.md) można zrównoważyć obciążenie między wieloma wystąpieniami zdarzeń programu i punktów kontrolnych podczas otrzymywania. W nowszych wersjach (5,0), **EventProcessorClient** (.NET i Java) lub **EventHubConsumerClient** (Python i JavaScript) umożliwiają wykonywanie tych samych czynności. Model programistyczny jest prostszy przy użyciu zdarzeń. Zasubskrybuj zdarzenia, które Cię interesują, rejestrując procedurę obsługi zdarzeń.

W tym artykule opisano przykładowy scenariusz używany przez wiele wystąpień do odczytu zdarzeń z centrum zdarzeń, a następnie udostępniamy szczegółowe informacje o funkcjach klienta procesora zdarzeń, co umożliwia odbieranie zdarzeń z wielu partycji jednocześnie i równoważenie obciążenia z innymi konsumenci korzystający z tego samego centrum zdarzeń i grupy konsumentów.

> [!NOTE]
> Klucz skalowanie dla usługi Event Hubs jest podzielone na partycje konsumentów. W przeciwieństwie do [konkurujący odbiorcy](https://msdn.microsoft.com/library/dn568101.aspx) wzorzec, partycjonowanego wzorca odbiorców umożliwia dużej skali przez usunięcie wąskich gardeł rywalizacji o zasoby i ułatwienia równoległości typu end to end.

## <a name="example-scenario"></a>Przykładowy scenariusz

Jako przykładowy scenariusz należy wziąć pod uwagę firmy macierzystego zabezpieczeń, która monitoruje domów 100 000. Co minutę pobiera dane z różnych czujników, takich jak wykrywanie ruchu, czujnik otwartych drzwi/okien, detektory szkła i tak dalej, które są instalowane w każdym domu. Firma udostępnia witryny sieci web dla mieszkańców monitorować aktywność ich DOM w czasie zbliżonym do rzeczywistego.

Każdy czujnik wypycha dane do Centrum zdarzeń. Centrum zdarzeń jest skonfigurowany z 16 partycji. W celu korzystania z programu wymagany jest mechanizm, który może odczytywać te zdarzenia, konsolidować je (filtrowanie, agregowanie itd.) i zrzutować agregacji do magazynu obiektów blob, który jest następnie rzutowany na stronę sieci Web przyjaznych dla użytkownika.

## <a name="write-the-consumer-application"></a>Napisanie aplikacji klienta

Podczas projektowania klienta w środowisku rozproszonym, scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** tworzenie wielu odbiorców za pomocą każdy odbiorca przejmowania własności odczytywanie ich z kilku partycje usługi Event Hubs.
2. **Możliwość równoważenia obciążenia:** Zwiększ lub Zmniejsz konsumentów dynamicznie. Na przykład po dodaniu nowego typu czujnika (na przykład wykrywanie tlenku węgla) do każdej strony głównej zwiększa liczbę zdarzeń. W takiej sytuacji — operator (człowieka) powoduje zwiększenie liczby wystąpień odbiorców. Następnie pulę odbiorców można ponownie zrównoważyć liczby partycji są właścicielami, aby udostępnić obciążenia nowo dodanych użytkowników.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli odbiorca (**konsument a**) zakończy się niepowodzeniem (na przykład w maszynie wirtualnej, na której znajdują się nieoczekiwane awarie klienta), inni klienci mogą wybrać partycje należące do **konsumenta a** i kontynuować. Również utrzymania punktu, o nazwie *punktu kontrolnego* lub *przesunięcie*, powinien być wyświetlany dokładny moment, w którym **konsumenta A** nie powiodło się, lub nieco wcześniej.
4. **Korzystaj z zdarzeń:** Mimo że poprzednie trzy punkty zajmują się zarządzaniem konsumentem, musi istnieć kod, który umożliwia korzystanie z tych zdarzeń i jest przydatne w odróżnieniu od siebie. Na przykład Agreguj go i Przekaż do magazynu obiektów BLOB.

## <a name="event-processor-or-consumer-client"></a>Procesor zdarzeń lub klient klienta

Nie musisz tworzyć własnego rozwiązania, aby spełnić te wymagania. Te funkcje są oferowane przez zestawy SDK platformy Azure Event Hubs. W zestawach SDK platformy .NET lub Java można używać klienta procesora zdarzeń (EventProcessorClient), a także w zestawach SDK skryptów języka Python i Java, korzystając z EventHubConsumerClient. W starej wersji zestawu SDK była to host procesora zdarzeń (klasy eventprocessorhost), który obsługuje te funkcje.

W przypadku większości scenariuszy produkcyjnych zaleca się używanie klienta procesora zdarzeń do odczytu i przetwarzania zdarzeń. Klient procesora ma zapewnić niezawodne środowisko do przetwarzania zdarzeń we wszystkich partycjach centrum zdarzeń w sposób bezpieczny i odporny na uszkodzenia, a jednocześnie zapewniając możliwość tworzenia punktów kontrolnych postępu. Klienci procesora zdarzeń mogą również pracować wspólnie w kontekście grupy odbiorców dla danego centrum zdarzeń. Klienci będą automatycznie zarządzać dystrybucją i zrównoważeniem pracy, ponieważ wystąpienia stają się dostępne lub niedostępne dla grupy.

## <a name="partition-ownership-tracking"></a>Własność partycji śledzenia

Wystąpienie procesora zdarzeń zwykle jest właścicielem i przetwarza zdarzenia z co najmniej jednej partycji. Własność partycji jest równomiernie dystrybuowana wśród wszystkich aktywnych wystąpień procesora zdarzeń skojarzonych z połączeniem centrum zdarzeń i grupy konsumentów. 

Każdy procesor zdarzeń otrzymuje unikatowy identyfikator i oświadczenia własność partycji przez dodanie lub zaktualizowanie wpisu w magazynie punktów kontrolnych. Wszystkie wystąpienia procesora zdarzeń komunikują się z tym magazynem okresowo, aby zaktualizować własny stan przetwarzania oraz poznać inne aktywne wystąpienia. Te dane są następnie używane do zrównoważenia obciążenia między aktywnymi procesorami. Nowe wystąpienia mogą przyłączyć się do puli przetwarzania w celu skalowania w górę. Gdy wystąpienia przechodzą w dół, z powodu błędów lub skalowania w dół, własność partycji jest bezpiecznie przekazywana do innych aktywnych procesorów.

Rekordy własności partycji w magazynie punktów kontrolnych śledzą Event Hubs przestrzeni nazw, nazwy centrum zdarzeń, grupy odbiorców, identyfikatora procesora zdarzeń (nazywanego również właścicielem), identyfikatora partycji i czas ostatniej modyfikacji.



| Przestrzeń nazw usługi Event Hubs               | Nazwa centrum zdarzeń | **Grupa konsumentów** | Właściciel                                | Identyfikator partycji | Godzina ostatniej modyfikacji  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Każde wystąpienie procesora zdarzeń uzyskuje własność partycji i zaczyna przetwarzanie partycji od ostatniego znanego [punktu kontrolnego](# Checkpointing). Jeśli procesor ulegnie awarii (zamykanie maszyny wirtualnej), inne wystąpienia wykrywają ten sposób, sprawdzając czas ostatniej modyfikacji. Inne wystąpienia próbują uzyskać własność partycji, które wcześniej należały do nieaktywnego wystąpienia, i magazyn punktów kontrolnych gwarantuje, że tylko jedno z wystąpień powiodło się w przypadku zajmowania własności partycji. Tak więc w danym momencie istnieje co najwyżej jeden procesor, który pobiera zdarzenia z partycji.

## <a name="receive-messages"></a>Odbieranie komunikatów

Podczas tworzenia procesora zdarzeń należy określić funkcje, które będą przetwarzać zdarzenia i błędy. Każde wywołanie funkcji, która przetwarza zdarzenia, zapewnia pojedyncze zdarzenie z określonej partycji. Jest odpowiedzialny za obsługę tego zdarzenia. Aby upewnić się, że konsument przetwarza każdy komunikat co najmniej raz, należy napisać własny kod z logiką ponawiania. Należy zachować ostrożność w przypadku skażonych komunikatów.

Zalecamy, aby można było stosunkowo szybko pracować. Oznacza to, że możliwie jak najmniejszej ilości przetwarzania. Jeśli zachodzi potrzeba zapisu do magazynu i przeprowadzenia pewnych routingu, lepiej jest użyć dwóch grup konsumenckich i dwóch procesorów zdarzeń.

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

*Punkt kontrolny* jest procesem, przez który procesor zdarzeń oznacza lub zatwierdza pozycję ostatniego pomyślnie przetworzonego zdarzenia w ramach partycji. Oznaczanie punktu kontrolnego jest zwykle wykonywane w ramach funkcji, która przetwarza zdarzenia i występuje w odniesieniu do partycji w obrębie grupy odbiorców. 

Jeśli procesor zdarzeń rozłącza połączenie z partycji, inne wystąpienie może wznowić przetwarzanie partycji w punkcie kontrolnym, który został wcześniej przekazany przez ostatni procesor tej partycji w tej grupie odbiorców. Po nawiązaniu połączenia z procesorem przekazuje przesunięcie do centrum zdarzeń, aby określić lokalizację, w której ma zostać rozpoczęte odczytywanie. W ten sposób można użyć punktów kontrolnych do obu oznaczania zdarzeń jako "ukończone" przez aplikacje podrzędne i zapewnienia odporności, gdy procesor zdarzeń ulegnie awarii. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia od tego procesu tworzenia punktów kontrolnych. 

Gdy punkt kontrolny jest wykonywany do oznaczania zdarzenia jako przetworzone, wpis w magazynie punktów kontrolnych jest dodawany lub aktualizowany z przesunięciem i numerem sekwencyjnym zdarzenia. Użytkownicy powinni określić częstotliwość aktualizowania punktu kontrolnego. Aktualizacja po każdym pomyślnie przetworzonym zdarzeniu może mieć wpływ na wydajność i koszty, ponieważ wyzwala operację zapisu w źródłowym magazynie punktów kontrolnych. Ponadto punkt kontrolny każdego pojedynczego zdarzenia ma wskaźnik wzorca komunikatów umieszczonych w kolejce, dla którego Kolejka Service Bus może być lepszym rozwiązaniem niż centrum zdarzeń. Ideą usługi Event Hubs jest, skorzystaj z "co najmniej raz" dostarczania na dużą skalę. Wprowadzając swoje systemy klienckie idempotentne, można łatwo odzyskać sprawność po awarii lub ponowne uruchomienie wynik w te same zdarzenia, które są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Wątek wystąpień bezpieczeństwa i procesora

Domyślnie procesor zdarzeń lub odbiorca jest bezpieczny wątkowo i zachowuje się synchronicznie. Po nadejściu zdarzeń dla partycji jest wywoływana funkcja, która przetwarza zdarzenia. Kolejne komunikaty i wywołania tej funkcji są kolejki w tle, gdy pompa komunikatów działa w tle w innych wątkach. Bezpieczeństwo wątków, to eliminuje potrzebę kolekcje obsługujące wielowątkowość i znacznie zwiększa wydajność.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przewodniki szybki start:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
