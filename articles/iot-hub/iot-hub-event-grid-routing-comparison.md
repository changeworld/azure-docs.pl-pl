---
title: Porównanie siatki zdarzeń, routing dla Centrum IoT | Dokumentacja firmy Microsoft
description: Centrum IoT zapewnia własnej komunikat usługi routingu, ale integruje się również z siatki zdarzeń do publikowania zdarzeń. Porównaj te dwie funkcje.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 320320687e441a1296065eb9d0b7b12771036459
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636175"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porównaj rozsyłania wiadomości i siatki zdarzeń do Centrum IoT

Centrum IoT Azure oferuje możliwość strumienia danych z podłączonych urządzeń i integrowanie danych aplikacji biznesowych. Centrum IoT zapewnia dwie metody zintegrować zdarzenia IoT innych usług platformy Azure lub aplikacji biznesowych. W tym artykule omówiono te dwie funkcje zapewniające tej możliwości, dzięki czemu można wybrać, która opcja jest najlepsza w przypadku danego scenariusza.

* **Rozsyłanie wiadomości Centrum IoT**: funkcja tego Centrum IoT umożliwia użytkownikom [kierowania wiadomości urządzenia do chmury](iot-hub-devguide-messages-read-custom.md) do punktów końcowych usług, takich jak kontenery magazynu Azure, usługa Event Hubs, kolejek usługi Service Bus i tematów usługi Service Bus. Reguły routingu umożliwiają przeprowadzenie tras oparte na zapytaniach. Również włączyć [alerty krytyczne](iot-hub-devguide-messages-d2c.md) wyzwolenia akcji za pomocą zapytań i mogą być oparte na nagłówkach wiadomości oraz i treść. 
* **Centrum IoT integracji z siatki zdarzeń**: Azure zdarzeń siatki jest zdarzenie w pełni zarządzana usługa routingu, która wykorzystuje publikowanie-subskrypcji modelu. Centrum IoT i siatki zdarzeń współpracują ze sobą, aby [zintegrować zdarzenia Centrum IoT Azure i usług innych niż Azure](iot-hub-event-grid.md), w czasie niemal rzeczywistym. 

## <a name="similarities-and-differences"></a>Podobieństw i różnic

Podczas rozsyłania wiadomości i siatki zdarzeń włączyć konfiguracji alertu, istnieją pewne podstawowe różnice między nimi. Można skorzystać z poniższej tabeli, aby uzyskać szczegółowe informacje:

| Cecha | Rozsyłanie wiadomości Centrum IoT | Centrum IoT integracji z siatki zdarzeń |
| ------- | --------------- | ---------- |
| **Komunikaty** | Tak, routing wiadomości może służyć do danych telemetrycznych. | Nie, siatki zdarzeń tylko może służyć do zdarzenia Centrum IoT z systemem innym niż telemetrii. |
| **Typ zdarzenia** | Tak, routing wiadomości może raportować dwie zmiany i zdarzeń cyklu życia urządzenia. | Gdy urządzenia są zarejestrowane w Centrum IoT i usunięcia urządzenia tak, zgłosić zdarzenie siatki. |
| **Kolejność** | Tak, uporządkowania zdarzeń jest obsługiwana.  | Nie, kolejność zdarzeń nie jest gwarantowana. | 
| **Maksymalny rozmiar wiadomości** | 256 KB, device-to-cloud | 64 KB |
| **Filtrowanie** | Zaawansowane filtrowanie przy użyciu języka przypominającego SQL obsługuje filtrowanie nagłówki komunikatów i treść. Aby uzyskać przykłady, zobacz [język zapytań Centrum IoT](iot-hub-devguide-query-language.md). | Filtrowanie na podstawie sufiksu/prefiks identyfikatory urządzeń, które działa dobrze dla hierarchicznych usług takich jak magazynu. |
| **Punkty końcowe** | <ul><li>Centrum zdarzeń</li> <li>Obiektu blob magazynu</li> <li>Kolejka magistrali usług</li> <li>Tematy usługi Service Bus</li></ul><br>Płatną SKU Centrum IoT (S1, S2 i S3) są ograniczone do 10 niestandardowe punkty końcowe. może zostać utworzony 100 tras na Centrum IoT. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Centrum zdarzeń</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Usług innych firm za pomocą elementów Webhook</li></ul><br>Aby uzyskać najbardziej aktualne listy punktów końcowych, zobacz [obsługi zdarzeń siatki zdarzeń](../event-grid/overview.md#event-handlers). |
| **Koszty** | Jest bezpłatna oddzielne do rozsyłania wiadomości. Rozliczany tylko ruch przychodzący dane telemetryczne w Centrum IoT. Na przykład jeśli masz wiadomość kierowane do trzech różnych punktów końcowych są rozliczane dla tylko jednej wiadomości. | Brak bez żadnych opłat z Centrum IoT. Siatki zdarzeń oferuje pierwszych 100 000 operacji miesięcznie bezpłatnie, a następnie $: 0,60 na mln operacji po. |

Rozsyłanie wiadomości Centrum IoT i siatki zdarzeń mają podobieństwa zbyt, niektóre z nich są szczegółowo opisane w poniższej tabeli:

| Cecha | Rozsyłanie wiadomości Centrum IoT | Centrum IoT integracji z siatki zdarzeń |
| ------- | --------------- | ---------- |
| **Niezawodność** | Wysoki: Dostarcza każdy komunikat do punktu końcowego, co najmniej raz dla każdej trasy. Wygasa wszystkie komunikaty, które nie zostały dostarczone w ciągu godziny. | Wysoki: Dostarcza każdy komunikat do elementu webhook co najmniej raz dla każdej subskrypcji. Wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. | 
| **Skalowalność** | Wysoki: Zoptymalizowane pod kątem obsługi miliony równocześnie połączonych urządzeń wysyłania miliardów wiadomości. | Wysoki: Funkcją routingu 10 000 000 zdarzeń na sekundę na region. |
| **Opóźnienie** | Niski: Czasie niemal rzeczywistym. | Niski: Czasie niemal rzeczywistym. |
| **Wyślij do wielu punktów końcowych** | Tak, Wyślij wiadomość jeden do wielu punktów końcowych. | Tak, Wyślij wiadomość jeden do wielu punktów końcowych.  | 
| **Bezpieczeństwo** | Centrum iot zapewnia na urządzenie tożsamości i kontroli dostępu odwoływalny. Aby uzyskać więcej informacji, zobacz [kontrolę dostępu Centrum IoT](iot-hub-devguide-security.md). | Zdarzenie siatki udostępnia weryfikację w trzech miejscach: subskrypcji zdarzeń, publikowania zdarzeń i dostarczania zdarzeń elementu webhook. Aby uzyskać więcej informacji, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak wybrać

Rozsyłanie wiadomości Centrum IoT i Centrum IoT integracji siatki zdarzenia wykonaj różne akcje, aby to zrobić. One zarówno uwzględnienia informacji z rozwiązania IoT Hub i przekaż go, aby mogły zareagować innych usług. W jaki sposób użytkownik zdecyduje które? Oprócz danych z poprzedniej sekcji Użyj poniższych pytań w celu ułatwienia decyzji: 

* **Jakiego rodzaju dane są wysyłane na punkty końcowe?**

   Użyj Centrum IoT wiadomości routingu podczas wysyłania danych telemetrycznych do innych usług. Komunikat routingu również umożliwia wykonywanie kwerend nagłówki komunikatów i treści wiadomości. 

   Integracja Centrum IoT z siatki zdarzeń współpracuje z zdarzenia występujące w usłudze IoT Hub. Tych zdarzeń Centrum IoT to urządzenie, tworzenie i usuwanie. 

* **Jakie punkty końcowe mają otrzymywać te informacje?**

   Rozsyłanie wiadomości Centrum IoT obsługuje ograniczone punktów końcowych, ale można tworzyć łączników, aby przekierować dane i zdarzenia do dodatkowe punkty końcowe. Aby uzyskać pełną listę obsługiwanych punktów końcowych zobacz tabelę w poprzedniej sekcji. 

   Integracja Centrum IoT z siatki zdarzeń obsługuje więcej punktów końcowych. Współdziała również z elementów webhook, aby rozszerzyć routing poza ekosystem usługi Azure i w aplikacjach innych firm. 

* **Istotne jest, jeśli dane w kolejności?**

   Rozsyłanie wiadomości Centrum IoT zachowuje kolejność, w którym są wysyłane wiadomości, dzięki czemu przychodzących w taki sam sposób.

   Zdarzenie siatki nie gwarantuje punkty końcowe będą otrzymywać zdarzeń w tej samej kolejności ich wystąpienia. Jednak schematu zdarzeń obejmują sygnatury czasowej, który może służyć do identyfikowania kolejność po odebrania zdarzeń w punkcie końcowym. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [rozsyłania wiadomości Centrum IoT](iot-hub-devguide-messages-d2c.md) i [punkty końcowe Centrum IoT](iot-hub-devguide-endpoints.md).

* Dowiedz się więcej o [Azure zdarzeń siatki](../event-grid/overview.md)

* Wypróbuj integracji siatki zdarzeń przez [wysyłanie powiadomień e-mail o zdarzeniach Centrum IoT Azure za pomocą aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md)