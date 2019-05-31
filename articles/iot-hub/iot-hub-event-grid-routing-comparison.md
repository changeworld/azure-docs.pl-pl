---
title: Porównanie usługi Event Grid routingu dla usługi IoT Hub | Dokumentacja firmy Microsoft
description: Usługi IoT Hub udostępnia swoje własne usługi routingu wiadomości, ale integruje się również z usługi Event Grid do publikowania zdarzeń. Porównaj dwie funkcje.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 0b17a87fa02c382ae19cca6e4abcfff2ec475450
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252691"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porównanie routingu komunikatów i Event Grid dla Centrum IoT Hub

Usługa Azure IoT Hub umożliwia przesyłanie strumieniowe danych z połączonych urządzeń i integrowanie danych z aplikacji biznesowych. IoT Hub udostępnia dwie metody zdarzeń IoT integrowanie innych usług platformy Azure lub aplikacje biznesowe. W tym artykule omówiono te dwie funkcje, które zapewniają tej funkcji, tak, aby można było wybrać, która opcja jest najlepsza w przypadku danego scenariusza.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Routing komunikatów usługi IoT Hub](iot-hub-devguide-messages-d2c.md)** : Ta funkcja usługi IoT Hub umożliwia użytkownikom kierowanie komunikatów z urządzenia do chmury do punktów końcowych usług, takich jak kontenery usługi Azure Storage, usługa Event Hubs, kolejki usługi Service Bus i tematów usługi Service Bus. Routing udostępnia również możliwość wykonywanie zapytania do filtrowania danych przed przesłaniem go do punktów końcowych. Oprócz danych telemetrycznych z urządzeń, możesz również wysłać [zdarzenia nietelemetrycznych](iot-hub-devguide-messages-d2c.md#non-telemetry-events) można wyzwalać akcje. 

**Integracja usługi IoT Hub przy użyciu usługi Event Grid**: Azure Event Grid to w pełni zarządzanej usługi routingu zdarzeń korzystającej Publikuj — Subskrybuj modelu. Usługa IoT Hub i Event Grid współpracują ze sobą, aby [integrowanie zdarzeń usługi IoT Hub platformy Azure i spoza tej platformy](iot-hub-event-grid.md), w czasie niemal rzeczywistym. Usługa IoT Hub publikuje [zdarzenia urządzenia](iot-hub-event-grid.md#event-types), które są ogólnie dostępne, a teraz są również publikowane dane telemetryczne zdarzeń, która znajduje się w publicznej wersji zapoznawczej.

## <a name="differences"></a>Różnice

Chociaż routingu komunikatów i Event Grid włączone podczas konfigurowania alertu, istnieją niektóre podstawowe różnice między nimi. Można znaleźć w poniższej tabeli, aby uzyskać szczegółowe informacje:

| Cecha | Routing komunikatów usługi IoT Hub | Integracja usługi IoT Hub przy użyciu usługi Event Grid |
| ------- | --------------- | ---------- |
| **Komunikaty z urządzenia i zdarzenia** | Tak, routing komunikatów może służyć do danych telemetrycznych i zmiany w bliźniaczej reprezentacji raportów oraz zdarzenia cyklu życia urządzenia. | Tak, usługa Event Grid może służyć do danych telemetrycznych, ale może również zgłosić, gdy urządzenia są tworzone, usunięte, połączonych i odłączony od usługi IoT Hub |
| **Określanie kolejności** | Tak, jest utrzymywany porządkowania zdarzeń.  | Nie, nie jest gwarantowana kolejność zdarzeń. | 
| **Filtrowanie** | Zaawansowane filtrowanie właściwości aplikacji komunikatu, komunikat właściwości systemu, treści wiadomości, tagów bliźniaczych reprezentacji urządzeń i urządzeń bliźniacza reprezentacja właściwości. Aby uzyskać przykłady, zobacz [składni zapytań routingu komunikatów](iot-hub-devguide-routing-query-syntax.md). | Filtrowanie na podstawie typu zdarzenia, typ podmiotu i atrybuty w każdym przypadku. Aby uzyskać przykłady, zobacz [zrozumienie filtrowania zdarzeń w subskrypcje usługi Event Grid](../event-grid/event-filtering.md). Podczas subskrybowania zdarzenia telemetrii, można zastosować dodatkowe filtry danych filtr właściwości komunikatu, komunikat bliźniaczej reprezentacji treści i urządzeń w usłudze IoT Hub, przed opublikowaniem w usłudze Event Grid. Zobacz [sposób filtrowania zdarzeń](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Punkty końcowe** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Kolejki usługi Service Bus</li> <li>Tematy usługi Service Bus</li></ul><br>IoT Hub produktów płatnych (S1, S2 i S3) są ograniczone do 10 niestandardowych punktów końcowych. 100 tras mogą być tworzone dla usługi IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Tematy niestandardowe</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Usługi innych firm przy użyciu elementów Webhook</li></ul><br>500 punktów końcowych usługi IoT Hub są obsługiwane. Aby uzyskać najbardziej aktualną listę punktów końcowych, zobacz [procedury obsługi zdarzeń usługi Event Grid](../event-grid/overview.md#event-handlers). |
| **Koszty** | Nie ma osobnych opłat do rozsyłania wiadomości. Tylko przychodzące dane telemetryczne do Centrum IoT Hub są naliczane opłaty. Na przykład w przypadku wiadomości kierowane do trzech różnych punktów końcowych są rozliczane dla tylko jednej wiadomości. | Nie ma opłat z usługi IoT Hub. Usługa Event Grid oferuje pierwszych 100 000 operacji miesięcznie za darmo, a następnie $: 0,60 za milion operacji później. |

## <a name="similarities"></a>Podobieństwa

Routing komunikatów usługi IoT Hub i Event Grid mają podobieństwa zbyt, niektóre z nich są szczegółowo opisane w poniższej tabeli:

| Cecha | Routing komunikatów usługi IoT Hub | Integracja usługi IoT Hub przy użyciu usługi Event Grid |
| ------- | --------------- | ---------- |
| **Maksymalny rozmiar komunikatu** | 256 KB, device-to-cloud | 256 KB, device-to-cloud |
| **Niezawodność** | Wysoka: Dostarcza każdy komunikat do punktu końcowego, co najmniej raz dla każdej trasy. Wygasa wszystkie komunikaty, które nie zostały dostarczone w ciągu jednej godziny. | Wysoka: Dostarcza każdy komunikat do elementu webhook, co najmniej raz dla każdej subskrypcji. Wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. | 
| **Skalowalność** | Wysoka: Zoptymalizowane na potrzeby obsługi milionów równocześnie połączonych urządzeń wysyłające miliardy komunikatów. | Wysoka: Zdolność do routingu 10 000 000 zdarzeń na sekundę na region. |
| **Opóźnienie** | Niski: Niemal w czasie rzeczywistym. | Niski: Niemal w czasie rzeczywistym. |
| **Wyślij do wielu punktów końcowych** | Tak, wysyłaj jednego komunikatu do wielu punktów końcowych. | Tak, wysyłaj jednego komunikatu do wielu punktów końcowych.  
| **Zabezpieczenia** | Iot Hub udostępnia tożsamość na urządzenie i kontroli dostępu odwoływalny. Aby uzyskać więcej informacji, zobacz [kontroli dostępu w usłudze IoT Hub](iot-hub-devguide-security.md). | Usługa Event Grid udostępnia weryfikację w trzech miejscach: subskrypcji zdarzeń, publikowanie zdarzeń i dostarczania zdarzeń elementu webhook. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak wybrać

Routing komunikatów usługi IoT Hub i integracji usługi IoT Hub przy użyciu usługi Event Grid wykonywać różne akcje, aby to zrobić. Zarówno pobierają informacje z rozwiązania usługi IoT Hub i przekazać go tak, aby inne usługi pozwala reagować. W jaki sposób zdecydujesz, który z nich do użycia? Należy rozważyć następujące kwestie jako przewodnik decyzji: 

* **Jakiego rodzaju dane jest wysyłany do punktów końcowych?**

   Użyj routing komunikatów usługi IoT Hub podczas wysyłania danych telemetrycznych z innymi usługami. Komunikat o routingu również umożliwia wysyłanie zapytań dotyczących właściwości komunikatu aplikacji i systemu, treści wiadomości, tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń.

   Integracja usługi IoT Hub przy użyciu usługi Event Grid w programach zdarzeń występujących w usłudze IoT Hub. Te zdarzenia usługi IoT Hub zawierają dane telemetryczne, urządzenie utworzone, usunięte, połączonych i odłączony. Podczas subskrybowania zdarzenia telemetrii, można zastosować dodatkowe filtry danych filtr właściwości komunikatu, komunikat bliźniaczej reprezentacji treści i urządzeń w usłudze IoT Hub, przed opublikowaniem w usłudze Event Grid. Zobacz [sposób filtrowania zdarzeń](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Punkty końcowe potrzebnych do otrzymywania tych informacji?**

   Routing komunikatów usługi IoT Hub obsługuje ograniczoną liczbę unikatowych punktów końcowych i typy punktów końcowych, ale możesz tworzyć łączniki do przekierowywania danych i zdarzeń dodatkowe punkty końcowe. Aby uzyskać pełną listę obsługiwanych punktów końcowych zobacz tabelę w poprzedniej sekcji. 

   Integracja usługi IoT Hub przy użyciu usługi Event Grid obsługuje 500 punktów końcowych usługi IoT Hub oraz większe różne typy punktów końcowych. Naively integruje się z usługą Azure Functions, Logic Apps, kolejki magazynu i usługi Service Bus, a także współdziała z elementami webhook w celu rozszerzenia wysłanie danych poza ekosystem usługi platformy Azure i w aplikacjach innych firm.

* **Ważne jest, jeśli dane w kolejności?**

   Routing komunikatów usługi IoT Hub utrzymuje kolejność, w której wiadomości są wysyłane, tak, aby pojawić się w taki sam sposób.

   Usługa Event Grid nie gwarantuje punktów końcowych zostaną odebrane zdarzenia w tej samej kolejności ich wystąpienia. Dla tych przypadków, w których bezwzględne kolejność komunikatów jest ważna i/lub w którym użytkownik musi zaufanego Unikatowy identyfikator wiadomości zaleca się przy użyciu routingu komunikatów. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Routing komunikatów usługi IoT Hub](iot-hub-devguide-messages-d2c.md) i [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).
* Dowiedz się więcej na temat usługi [Azure Event Grid](../event-grid/overview.md).
* Aby dowiedzieć się, jak utworzyć trasy wiadomości, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](../iot-hub/tutorial-routing.md) samouczka.
* Wypróbuj integracji usługi Event Grid przez [wysyłanie wiadomości e-mail powiadomień o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md).