---
title: Porównanie usługi Event Grid routingu dla usługi IoT Hub | Dokumentacja firmy Microsoft
description: Usługi IoT Hub udostępnia swoje własne usługi routingu wiadomości, ale integruje się również z usługi Event Grid do publikowania zdarzeń. Porównaj dwie funkcje.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: af03f737c082a7fda90104303e018f7b417729b9
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143797"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porównanie routingu komunikatów i Event Grid dla Centrum IoT Hub

Usługa Azure IoT Hub umożliwia przesyłanie strumieniowe danych z połączonych urządzeń i integrowanie danych z aplikacji biznesowych. IoT Hub udostępnia dwie metody zdarzeń IoT integrowanie innych usług platformy Azure lub aplikacje biznesowe. W tym artykule omówiono te dwie funkcje, które zapewniają tej funkcji, tak, aby można było wybrać, która opcja jest najlepsza w przypadku danego scenariusza.

* **Routing komunikatów usługi IoT Hub**: funkcja tego Centrum IoT Hub umożliwia użytkownikom [kierowanie komunikatów z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md) do punktów końcowych usługi, takich jak kontenery usługi Azure Storage, usługa Event Hubs, kolejki usługi Service Bus i tematów usługi Service Bus. Reguły routingu zapewniają elastyczność wykonaj tras oparte na zapytaniach. Pozwalają też [alerty krytyczne](iot-hub-devguide-messages-d2c.md) wyzwalać akcje za pomocą zapytań i mogą być oparte na nagłówki komunikatów i treść. 
* **Integracja usługi IoT Hub przy użyciu usługi Event Grid**: Azure Event Grid to w pełni zarządzanej usługi routingu zdarzeń korzystającej publikowania-subskrypcji modelu. Usługa IoT Hub i Event Grid współpracują ze sobą, aby [integrowanie zdarzeń usługi IoT Hub platformy Azure i spoza tej platformy](iot-hub-event-grid.md), w czasie niemal rzeczywistym. 

## <a name="similarities-and-differences"></a>Podobieństwa i różnice

Chociaż routingu komunikatów i Event Grid włączone podczas konfigurowania alertu, istnieją niektóre podstawowe różnice między nimi. Można znaleźć w poniższej tabeli, aby uzyskać szczegółowe informacje:

| Cecha | Routing komunikatów usługi IoT Hub | Integracja usługi IoT Hub przy użyciu usługi Event Grid |
| ------- | --------------- | ---------- |
| **Komunikaty z urządzenia** | Tak, routing komunikatów może służyć do danych telemetrycznych. | Nie, usługa Event Grid należy używać tylko nietelemetrycznych zdarzeń usługi IoT Hub. |
| **Typ zdarzenia** | Tak, routing komunikatów może zgłaszać bliźniaczej reprezentacji zmiany i zdarzenia cyklu życia urządzenia. | Tak, usługa Event Grid może Raportuj, gdy urządzenia są tworzone, usunięte, połączonych i odłączony od usługi IoT Hub |
| **Określanie kolejności** | Tak, jest utrzymywany porządkowania zdarzeń.  | Nie, nie jest gwarantowana kolejność zdarzeń. | 
| **Maksymalny rozmiar komunikatu** | 256 KB, device-to-cloud | 64 KB |
| **Filtrowanie** | Zaawansowane filtrowanie przy użyciu języka przypominającego SQL obsługuje filtrowanie komunikatów nagłówki i treść. Aby uzyskać przykłady, zobacz [język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md). | Filtrowanie na podstawie sufiksu/prefiks identyfikatory urządzeń, które działa dobrze dla hierarchicznych usług, takich jak magazyn. |
| **Punkty końcowe** | <ul><li>Event Hubs</li> <li>Obiekt blob magazynu</li> <li>Kolejka magistrali usług</li> <li>Tematy usługi Service Bus</li></ul><br>IoT Hub produktów płatnych (S1, S2 i S3) są ograniczone do 10 niestandardowych punktów końcowych. 100 tras mogą być tworzone dla usługi IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Tematy niestandardowe</li> <li>Usługi innych firm przy użyciu elementów Webhook</li></ul><br>Aby uzyskać najbardziej aktualną listę punktów końcowych, zobacz [procedury obsługi zdarzeń usługi Event Grid](../event-grid/overview.md#event-handlers). |
| **Koszty** | Nie ma osobnych opłat do rozsyłania wiadomości. Tylko przychodzące dane telemetryczne do Centrum IoT Hub są naliczane opłaty. Na przykład w przypadku wiadomości kierowane do trzech różnych punktów końcowych są rozliczane dla tylko jednej wiadomości. | Nie ma opłat z usługi IoT Hub. Usługa Event Grid oferuje pierwszych 100 000 operacji miesięcznie za darmo, a następnie $: 0,60 za milion operacji później. |

Routing komunikatów usługi IoT Hub i Event Grid mają podobieństwa zbyt, niektóre z nich są szczegółowo opisane w poniższej tabeli:

| Cecha | Routing komunikatów usługi IoT Hub | Integracja usługi IoT Hub przy użyciu usługi Event Grid |
| ------- | --------------- | ---------- |
| **Niezawodność** | Wysoka: Dostarcza każdy komunikat do punktu końcowego, co najmniej raz dla każdej trasy. Wygasa wszystkie komunikaty, które nie zostały dostarczone w ciągu jednej godziny. | Wysoka: Dostarcza każdy komunikat do elementu webhook, co najmniej raz dla każdej subskrypcji. Wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. | 
| **Skalowalność** | Wysoki: Zoptymalizowana pod kątem obsługi milionów równocześnie połączonych urządzeń wysyłające miliardy komunikatów. | Wysoki: Obsługą routingu 10 000 000 zdarzeń na sekundę na region. |
| **Opóźnienie** | Niski: Niemal w czasie rzeczywistym. | Niski: Niemal w czasie rzeczywistym. |
| **Wyślij do wielu punktów końcowych** | Tak, wysyłaj jednego komunikatu do wielu punktów końcowych. | Tak, wysyłaj jednego komunikatu do wielu punktów końcowych.  | 
| **Bezpieczeństwo** | Iot Hub udostępnia tożsamość na urządzenie i kontroli dostępu odwoływalny. Aby uzyskać więcej informacji, zobacz [kontroli dostępu w usłudze IoT Hub](iot-hub-devguide-security.md). | Usługa Event Grid udostępnia weryfikację w trzech miejscach: subskrypcji zdarzeń, publikowanie zdarzeń i dostarczania zdarzeń elementu webhook. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak wybrać

Routing komunikatów usługi IoT Hub i integracji usługi IoT Hub przy użyciu usługi Event Grid wykonywać różne akcje, aby to zrobić. Zarówno pobierają informacje z rozwiązania usługi IoT Hub i przekazać go tak, aby inne usługi pozwala reagować. W jaki sposób zdecydujesz, który z nich do użycia? Oprócz danych z poprzedniej sekcji, użyj poniższych pytań się pomocne decyzji: 

* **Jakiego rodzaju dane jest wysyłany do punktów końcowych?**

   Użyj routing komunikatów usługi IoT Hub podczas wysyłania danych telemetrycznych z innymi usługami. Komunikat o routingu również umożliwia wysyłanie zapytań dotyczących nagłówki komunikatów i treści wiadomości. 

   Integracja usługi IoT Hub przy użyciu usługi Event Grid w programach zdarzeń występujących w usłudze IoT Hub. Te zdarzenia usługi IoT Hub obejmują urządzeń utworzone, usunięte, połączonych i odłączony. 

* **Punkty końcowe potrzebnych do otrzymywania tych informacji?**

   Routing komunikatów usługi IoT Hub obsługuje ograniczone punkty końcowe, ale możesz tworzyć łączniki do przekierowywania danych i zdarzeń dodatkowe punkty końcowe. Aby uzyskać pełną listę obsługiwanych punktów końcowych zobacz tabelę w poprzedniej sekcji. 

   Integracja usługi IoT Hub przy użyciu usługi Event Grid obsługuje dodatkowe punkty końcowe. Działa z elementami webhook w celu rozszerzenia routingu poza ekosystem usługi platformy Azure i w aplikacjach innych firm. 

* **Ważne jest, jeśli dane w kolejności?**

   Routing komunikatów usługi IoT Hub utrzymuje kolejność, w której wiadomości są wysyłane, tak, aby pojawić się w taki sam sposób.

   Usługa Event Grid nie gwarantuje punktów końcowych zostaną odebrane zdarzenia w tej samej kolejności ich wystąpienia. Schemat zdarzeń jednak zawierać sygnaturę czasową, który może służyć do identyfikowania kolejności po przyjeździe zdarzenia do punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [routing komunikatów usługi IoT Hub](iot-hub-devguide-messages-d2c.md) i [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

* Dowiedz się więcej o [usługi Azure Event Grid](../event-grid/overview.md)

* Wypróbuj integracji usługi Event Grid przez [wysyłanie wiadomości e-mail powiadomień o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md)