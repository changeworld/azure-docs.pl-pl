---
title: Równoważenie obciążenia partycji w wielu wystąpieniach — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak zrównoważyć obciążenie partycji w wielu wystąpieniach aplikacji przy użyciu procesora zdarzeń i zestawu SDK usługi Azure Event Hubs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907658"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Równoważenie obciążenia partycji w wielu wystąpieniach aplikacji
Aby skalować aplikację przetwarzania zdarzeń, można uruchomić wiele wystąpień aplikacji i mieć ją zrównoważyć obciążenie między sobą. W starszych wersjach [EventProcessorHost](event-hubs-event-processor-host.md) pozwoliło na zrównoważenie obciążenia między wieloma wystąpieniami programu i zdarzeń punktu kontrolnego podczas odbierania. W nowszych wersjach (od 5.0) **EventProcessorClient** (.NET i Java) lub **EventHubConsumerClient** (Python i JavaScript) umożliwia zrobienie tego samego. Model programistyczny jest prostszy przy użyciu zdarzeń. Subskrybujesz zdarzenia, które Cię interesują, rejestrując program obsługi zdarzeń.

W tym artykule opisano przykładowy scenariusz używania wielu wystąpień do odczytywania zdarzeń z Centrum zdarzeń, a następnie zawiera szczegółowe informacje o funkcjach klienta procesora zdarzeń, który umożliwia odbieranie zdarzeń z wielu partycji jednocześnie i równoważenie obciążenia z innymi konsumentów korzystających z tego samego centrum zdarzeń i grupy konsumentów.

> [!NOTE]
> Kluczem do skalowania dla centrów zdarzeń jest idea podzielonych na partycje konsumentów. W przeciwieństwie do [wzorca konkurujących konsumentów](https://msdn.microsoft.com/library/dn568101.aspx) wzorzec konsumenta, wzorzec konsumenta podzielony na partycje umożliwia dużą skalę, usuwając wąskie gardło rywalizacji i ułatwiając równoległość końca do końca.

## <a name="example-scenario"></a>Przykładowy scenariusz

W przykładowym scenariuszu rozważmy firmę zajmującą się bezpieczeństwem w domu, która monitoruje 100 000 domów. Co minutę pobiera dane z różnych czujników, takich jak czujnik ruchu, czujnik otwierania drzwi / okien, detektor pęknięć szkła i tak dalej, zainstalowany w każdym domu. Firma udostępnia stronę internetową dla mieszkańców do monitorowania działalności ich domu w czasie zbliżonym do rzeczywistego.

Każdy czujnik wypycha dane do centrum zdarzeń. Centrum zdarzeń jest skonfigurowane z 16 partycjami. Na końcu zużywa, potrzebujesz mechanizmu, który może odczytywać te zdarzenia, konsolidować je (filtrować, agregować i tak dalej) i zrzucić agregację do obiektu blob magazynu, który jest następnie rzutowany na przyjazną dla użytkownika stronę sieci web.

## <a name="write-the-consumer-application"></a>Napisz aplikację konsumencką

Podczas projektowania konsumenta w środowisku rozproszonym scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Tworzenie wielu konsumentów, z każdego konsumenta przejęcia na własność odczytu z kilku partycji Centrum zdarzeń.
2. **Bilans obciążenia:** Dynamicznie zwiększaj lub zmniejszaj konsumentów. Na przykład, gdy do każdego domu dodawany jest nowy typ czujnika (na przykład detektor tlenku węgla), liczba zdarzeń wzrasta. W takim przypadku operator (człowiek) zwiększa liczbę wystąpień konsumentów. Następnie pula konsumentów można zrównoważyć liczbę partycji, które posiadają, aby udostępnić obciążenie nowo dodanych konsumentów.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli konsument **(konsument A**) ulegnie awarii (na przykład maszyna wirtualna obsługująca konsumenta nagle ulega awarii), inni konsumenci mogą odebrać partycje należące do **konsumenta A** i kontynuować. Ponadto punkt kontynuacji, zwany *punktem kontrolnym* lub *przesunięciem,* powinien znajdować się dokładnie w punkcie, w którym **konsument A** nie powiódł się lub nieco wcześniej.
4. **Zużywają zdarzenia:** Podczas gdy poprzednie trzy punkty dotyczą zarządzania konsumentem, musi istnieć kod, aby korzystać ze zdarzeń i zrobić coś przydatnego z nim. Na przykład zagregować go i przekazać go do magazynu obiektów blob.

## <a name="event-processor-or-consumer-client"></a>Procesor zdarzeń lub klient konsumencki

Nie trzeba tworzyć własne rozwiązanie, aby spełnić te wymagania. Zestaw SDK usługi Azure Event Hubs udostępnia tę funkcję. W plikach .NET lub Java SDK można użyć klienta procesora zdarzeń (EventProcessorClient), a w pythonie i java script SDK, używasz EventHubConsumerClient. W starej wersji SDK, to host procesora zdarzeń (EventProcessorHost), który obsługiwał te funkcje.

W większości scenariuszy produkcyjnych zaleca się używanie klienta procesora zdarzeń do odczytu i przetwarzania zdarzeń. Klient procesora jest przeznaczony do zapewnienia niezawodnego środowiska przetwarzania zdarzeń we wszystkich partycjach centrum zdarzeń w sposób wydajny i odporny na uszkodzenia, zapewniając jednocześnie środki do punktu kontrolnego jego postęp. Klienci procesora zdarzeń są również w stanie współpracować w kontekście grupy konsumentów dla danego centrum zdarzeń. Klienci będą automatycznie zarządzać dystrybucją i równoważeniem pracy, gdy wystąpienia staną się dostępne lub niedostępne dla grupy.

## <a name="partition-ownership-tracking"></a>Śledzenie własności partycji

Wystąpienie procesora zdarzeń zazwyczaj jest właścicielem i przetwarza zdarzenia z jednej lub więcej partycji. Własność partycji jest równomiernie rozłożona między wszystkie wystąpienia procesora zdarzeń aktywnych skojarzone z koncentratorem zdarzeń i kombinacją grup odbiorców. 

Każdy procesor zdarzeń otrzymuje unikatowy identyfikator i żąda własności partycji, dodając lub aktualizując wpis w magazynie punktów kontrolnych. Wszystkie wystąpienia procesora zdarzeń komunikują się z tym magazynem okresowo, aby zaktualizować swój własny stan przetwarzania, a także dowiedzieć się więcej o innych aktywnych wystąpieniach. Dane te są następnie używane do równoważenia obciążenia między aktywnymi procesorami. Nowe wystąpienia można dołączyć do puli przetwarzania, aby skalować w górę. Gdy wystąpienia zejdą w dół, z powodu awarii lub skalowania w dół, własność partycji jest bezpiecznie przenoszona do innych aktywnych procesorów.

Rekordy własności partycji w magazynie punktów kontrolnych przechowują informacje o obszarze nazw Centrum zdarzeń, nazwie centrum zdarzeń, grupie odbiorców, identyfikatorze procesora zdarzeń (nazywanym również właścicielem), identyfikatorze partycji i ostatnim zmodyfikowanym czasie.



| Przestrzeń nazw usługi Event Hubs               | Nazwa centrum zdarzeń | **Grupa konsumentów** | Właściciel                                | Identyfikator partycji | Czas ostatniej modyfikacji  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | grupa myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | grupa myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | grupa myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | grupa myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Każde wystąpienie procesora zdarzeń przejmuje własność partycji i rozpoczyna przetwarzanie partycji z ostatniego znanego [punktu kontrolnego](# Checkpointing). Jeśli procesor ulegnie awarii (maszyna wirtualna zostanie zamknięta), inne wystąpienia wykryją to, patrząc na ostatni zmodyfikowany czas. Inne wystąpienia próbują uzyskać własność partycji wcześniej własnością nieaktywne wystąpienie i magazynu punktów kontrolnych gwarantuje, że tylko jedno z wystąpień uda się przejęcia własności partycji. Tak więc, w danym momencie, jest co najwyżej jeden procesor odbieranie zdarzeń z partycji.

## <a name="receive-messages"></a>Odbieranie komunikatów

Podczas tworzenia procesora zdarzeń należy określić funkcje, które będą przetwarzać zdarzenia i błędy. Każde wywołanie funkcji, która przetwarza zdarzenia dostarcza pojedyncze zdarzenie z określonej partycji. Twoim obowiązkiem jest obsługa tego wydarzenia. Jeśli chcesz upewnić się, że konsument przetwarza każdą wiadomość co najmniej raz, musisz napisać własny kod z logiką ponawiania. Ale bądź ostrożny w sprawie zatrutych wiadomości.

Zalecamy, aby robić rzeczy stosunkowo szybko. Oznacza to, że zrobić jak najmniej przetwarzania, jak to możliwe. Jeśli chcesz zapisać do magazynu i zrobić kilka routingu, lepiej użyć dwóch grup odbiorców i mieć dwa procesory zdarzeń.

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

*Punkty kontrolne* to proces, w którym procesor zdarzeń oznacza lub zatwierdza położenie ostatniego pomyślnie przetworzonego zdarzenia w partycji. Oznaczanie punktu kontrolnego jest zazwyczaj wykonywane w ramach funkcji, która przetwarza zdarzenia i występuje na podstawie partycji w grupie odbiorców. 

Jeśli procesor zdarzeń rozłącza się z partycją, inne wystąpienie może wznowić przetwarzanie partycji w punkcie kontrolnym, który został wcześniej zatwierdzony przez ostatni procesor tej partycji w tej grupie odbiorców. Po podłączeniu procesora przekazuje przesunięcie do centrum zdarzeń, aby określić lokalizację, w której należy rozpocząć odczyt. W ten sposób można użyć punktów kontrolnych, aby oznaczyć zdarzenia jako "ukończone" przez aplikacje podrzędne i zapewnić odporność, gdy procesor zdarzeń ujmuje się. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia od tego procesu tworzenia punktów kontrolnych. 

Po wykonaniu punktu kontrolnego, aby oznaczyć zdarzenie jako przetworzone, wpis w magazynie punktów kontrolnych jest dodawany lub aktualizowany o przesunięcie zdarzenia i numer sekwencyjny. Użytkownicy powinni zdecydować o częstotliwości aktualizowania punktu kontrolnego. Aktualizowanie po każdym pomyślnie przetworzonym zdarzeniu może mieć wpływ na wydajność i koszty, ponieważ wyzwala operację zapisu do podstawowego magazynu punktów kontrolnych. Ponadto punkt kontrolny każdego zdarzenia wskazuje na wzorzec obsługi wiadomości w kolejce, dla którego kolejka usługi Service Bus może być lepszym rozwiązaniem niż centrum zdarzeń. Ideą Centrum zdarzeń jest to, że otrzymujesz "co najmniej raz" dostawę na dużą skalę. Dzięki temu, że systemy podrzędne są idempotentne, można łatwo odzyskać po awarii lub ponownym uruchomieniu, które powodują, że te same zdarzenia są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Bezpieczeństwo wątków i wystąpienia procesora

Domyślnie procesor zdarzeń lub konsument jest bezpieczny dla wątków i zachowuje się w sposób synchroniczne. Gdy zdarzenia przychodzą dla partycji, funkcja, która przetwarza zdarzenia jest wywoływana. Kolejne komunikaty i wywołania tej funkcji kolejkują się w górę za kulisami, ponieważ pompa komunikatów nadal działa w tle w innych wątkach. To bezpieczeństwo gwintów eliminuje potrzebę kolekcji bezpiecznych dla wątków i znacznie zwiększa wydajność.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące szybkie starty:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-node-send-v2.md)
