---
title: Porównaj siatkę zdarzeń, routing dla Usługi IoT Hub | Dokumenty firmy Microsoft
description: Usługa IoT Hub oferuje własną usługę routingu wiadomości, ale także integruje się z usługą Event Grid do publikowania zdarzeń. Porównaj dwie funkcje.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906794"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porównanie routingu wiadomości i siatki zdarzeń dla Usługi IoT Hub

Usługa Azure IoT Hub umożliwia przesyłanie strumieniowe danych z podłączonych urządzeń i integrowanie tych danych z aplikacjami biznesowymi. Usługa IoT Hub oferuje dwie metody integrowania zdarzeń IoT z innymi usługami platformy Azure lub aplikacjami biznesowymi. W tym artykule omówiono dwie funkcje, które zapewniają tę możliwość, dzięki czemu można wybrać, która opcja jest najlepsza dla twojego scenariusza.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Routing komunikatów usługi IoT Hub:](iot-hub-devguide-messages-d2c.md)** Ta funkcja Usługi IoT Hub umożliwia użytkownikom kierowanie komunikatów z urządzenia do chmury do punktów końcowych usługi, takich jak kontenery usługi Azure Storage, centra zdarzeń, kolejki usługi Service Bus i tematy usługi Service Bus. Routing zapewnia również możliwość przeszukiwania danych przed przekierowaniem go do punktów końcowych. Oprócz danych telemetrycznych urządzenia można również wysyłać [zdarzenia nie telemetryczne,](iot-hub-devguide-messages-d2c.md#non-telemetry-events) które mogą być używane do wyzwalania akcji. 

**Integracja usługi IoT Hub z usługą Event Grid**: Usługa Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania i subskrybowania. Usługa IoT Hub i usługa Event Grid współpracują ze sobą w celu [zintegrowania zdarzeń usługi IoT Hub z usługami platformy Azure i usług innych niż Azure](iot-hub-event-grid.md)w czasie zbliżonym do rzeczywistego. Centrum IoT hub publikuje zdarzenia [urządzenia](iot-hub-event-grid.md#event-types) i zdarzenia telemetryczne.

## <a name="differences"></a>Różnice

Podczas gdy zarówno routing wiadomości, jak i siatka zdarzeń włączają konfigurację alertów, istnieją pewne kluczowe różnice między nimi. Szczegółowe informacje można znaleźć w poniższej tabeli:

| Funkcja | Routing komunikatów w centrum IoT | Integracja usługi IoT Hub z siatką zdarzeń |
| ------- | --------------- | ---------- |
| **Komunikaty i zdarzenia urządzenia** | Tak, routingu wiadomości może być używany do danych telemetrycznych, zmian bliźniaczych urządzeń raportu, zdarzeń cyklu życia urządzenia i zdarzeń zmiany podwójnej cyfrowej (część [publicznej wersji zapoznawczej IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md) | Tak, siatka zdarzeń może być używana do danych telemetrycznych, ale może również raportować, gdy urządzenia są tworzone, usuwane, podłączane i odłączane od Usługi IoT Hub |
| **Szeregowanie** | Tak, kolejność zdarzeń jest zachowywana.  | Nie, kolejność wydarzeń nie jest gwarantowana. | 
| **Filtrowanie** | Zaawansowane filtrowanie właściwości aplikacji wiadomości, właściwości systemu wiadomości, treść wiadomości, tagi bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia. Filtrowanie nie jest stosowane do cyfrowych zdarzeń zmiany bliźniaczej reprezentacji. Przykłady można znaleźć w [składni kwerendy routingu wiadomości](iot-hub-devguide-routing-query-syntax.md). | Filtrowanie na podstawie typu zdarzenia, typu tematu i atrybutów w każdym zdarzeniu. Aby uzyskać przykłady, zobacz [Opis zdarzeń filtrowania w sekcji Subskrypcje w siatce zdarzeń](../event-grid/event-filtering.md). Podczas subskrybowania zdarzeń telemetrycznych, można zastosować dodatkowe filtry danych do filtrowania właściwości wiadomości, treści wiadomości i bliźniaczej reprezentacji urządzenia w centrum IoT Hub, przed opublikowaniem w usztywnienia zdarzeń. Zobacz, [jak filtrować zdarzenia](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Punkty końcowe** | <ul><li>Usługa Event Hubs</li> <li>Azure Blob Storage</li> <li>Kolejka usługi Service Bus</li> <li>Tematy usługi Service Bus</li></ul><br>Płatne jednostki SKU usługi IoT Hub (S1, S2 i S3) są ograniczone do 10 niestandardowych punktów końcowych. Na centrum IoT można utworzyć 100 tras. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Usługa Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Tematy niestandardowe</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Usługi innych firm za pośrednictwem elementów WebHook</li></ul><br>Obsługiwanych jest 500 punktów końcowych na centrum IoT Hub. Aby uzyskać najbardziej aktualną listę punktów końcowych, zobacz [Programy obsługi zdarzeń w ucho.](../event-grid/overview.md#event-handlers) |
| **Koszty** | Nie ma oddzielnej opłaty za routing wiadomości. Opłata jest pobierana tylko przychodzących danych telemetrycznych do usługi IoT Hub. Na przykład jeśli masz wiadomość kierowane do trzech różnych punktów końcowych, są naliczane tylko dla jednej wiadomości. | Nie ma żadnych opłat z Usługi IoT Hub. Usługa Event Grid oferuje pierwsze 100 000 operacji miesięcznie za darmo, a następnie 0,60 USD za milion operacji. |

## <a name="similarities"></a>Podobieństwa

Routing komunikatów usługi IoT Hub i siatka zdarzeń mają również podobieństwa, z których niektóre są szczegółowo opisane w poniższej tabeli:

| Funkcja | Routing komunikatów w centrum IoT | Integracja usługi IoT Hub z siatką zdarzeń |
| ------- | --------------- | ---------- |
| **Maksymalny rozmiar wiadomości** | 256 KB, urządzenie do chmury | 256 KB, urządzenie do chmury |
| **Niezawodność** | Wysoki: Dostarcza każdą wiadomość do punktu końcowego co najmniej raz dla każdej trasy. Wygasa wszystkie wiadomości, które nie są dostarczane w ciągu jednej godziny. | Wysoka: dostarcza każdą wiadomość do elementu webhook co najmniej raz dla każdej subskrypcji. Wygasa wszystkie zdarzenia, które nie zostaną dostarczone w ciągu 24 godzin. | 
| **Skalowalność** | Wysoki: zoptymalizowany pod kątem obsługi milionów jednocześnie podłączonych urządzeń wysyłających miliardy wiadomości. | Wysoka: możliwość routingu 10 000 000 zdarzeń na sekundę na region. |
| **Opóźnienie** | Niski: W czasie zbliżonym do rzeczywistego. | Niski: W czasie zbliżonym do rzeczywistego. |
| **Wysyłanie do wielu punktów końcowych** | Tak, wyślij pojedynczą wiadomość do wielu punktów końcowych. | Tak, wyślij pojedynczą wiadomość do wielu punktów końcowych.  
| **Zabezpieczenia** | Usługa Iot Hub zapewnia tożsamość na urządzenie i odwołalną kontrolę dostępu. Aby uzyskać więcej informacji, zobacz [kontrolę dostępu usługi IoT Hub](iot-hub-devguide-security.md). | Usługa Event Grid zapewnia sprawdzanie poprawności w trzech punktach: subskrypcje zdarzeń, publikowanie zdarzeń i dostarczanie zdarzeń elementu webhook. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak wybrać

Routing komunikatów usługi IoT Hub i integracja usługi IoT Hub z usługą Event Grid wykonują różne akcje w celu uzyskania podobnych wyników. Obie one biorą informacje z rozwiązania Usługi IoT Hub i przekazują je dalej, aby inne usługi mogły reagować. Więc jak zdecydować, który z nich użyć? Zastanów się nad następującymi pytaniami, które pomogą Ci w podejmowaniu decyzji: 

* **Jakiego rodzaju dane wysyłasz do punktów końcowych?**

   Użyj routingu komunikatów usługi IoT Hub, gdy trzeba wysłać dane telemetryczne do innych usług. Routing wiadomości umożliwia również wykonywanie zapytań o właściwości aplikacji i systemu wiadomości, treść wiadomości, tagi bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia.

   Integracja usługi IoT Hub z siatką zdarzeń działa ze zdarzeniami występującymi w usłudze Centrum IoT. Te zdarzenia usługi IoT Hub obejmują dane telemetryczne, urządzenie utworzone, usunięte, połączone i rozłączone. Podczas subskrybowania zdarzeń telemetrycznych, można zastosować dodatkowe filtry danych do filtrowania właściwości wiadomości, treści wiadomości i bliźniaczej reprezentacji urządzenia w centrum IoT Hub, przed opublikowaniem w usztywnienia zdarzeń. Zobacz, [jak filtrować zdarzenia](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Jakie punkty końcowe muszą otrzymać te informacje?**

   Routing komunikatów usługi IoT Hub obsługuje ograniczoną liczbę unikatowych punktów końcowych i typów punktów końcowych, ale można tworzyć łączniki, aby przekierować dane i zdarzenia do dodatkowych punktów końcowych. Aby uzyskać pełną listę obsługiwanych punktów końcowych, zobacz tabelę w poprzedniej sekcji. 

   Integracja usługi IoT Hub z siatką zdarzeń obsługuje 500 punktów końcowych na centrum IoT Hub i większą różnorodność typów punktów końcowych. Natywnie integruje się z kolejkami usługi Azure Functions, Logic Apps, Storage i Service Bus, a także współpracuje z elementami webhook, aby rozszerzyć wysyłanie danych poza ekosystem usług platformy Azure i do aplikacji biznesowych innych firm.

* **Czy ma to znaczenie, jeśli twoje dane dotrą do porządku?**

   Routing komunikatów usługi IoT Hub zachowuje kolejność wysyłania wiadomości, dzięki czemu docierają w ten sam sposób.

   Usługa Event Grid nie gwarantuje, że punkty końcowe będą odbierać zdarzenia w tej samej kolejności, w jakiej wystąpiły. W przypadkach, w których bezwzględna kolejność wiadomości jest znacząca i/lub w których konsument potrzebuje wiarygodnego unikatowego identyfikatora wiadomości, zaleca się używanie routingu wiadomości. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [routingu komunikatów w centrum IoT](iot-hub-devguide-messages-d2c.md) i [punktach końcowych Usługi IoT Hub](iot-hub-devguide-endpoints.md).
* Dowiedz się więcej na temat usługi [Azure Event Grid](../event-grid/overview.md).
* Aby dowiedzieć się, jak utworzyć trasy komunikatów, zobacz [process IoT Hub device-to-cloud messages using routes](../iot-hub/tutorial-routing.md) tutorial.
* Wypróbuj integrację z siatką zdarzeń, [wysyłając powiadomienia e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki.](../event-grid/publish-iot-hub-events-to-logic-apps.md)
