---
title: Routing zdarzeń i komunikatów za pomocą usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Omówienie zdarzeń routingu i komunikatów do punktów końcowych usługi przy użyciu usługi Azure Digital bliźniaczych reprezentacji
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: a013525109fe85ad70e5aaa5895da20f5abc3237
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638536"
---
# <a name="routing-events-and-messages"></a>Routing zdarzeń i komunikatów

Rozwiązania IoT często łączą kilka zaawansowanych usług, które obejmują magazyn, analizę i wiele innych. W tym artykule opisano sposób łączenia aplikacji usługi Azure Digital bliźniaczych reprezentacji z usługami Azure Analytics, AI i Storage w celu uzyskania bardziej szczegółowych informacji i funkcji.

## <a name="route-types"></a>Typy tras  

Usługa Azure Digital bliźniaczych reprezentacji oferuje dwa sposoby łączenia zdarzeń IoT z innymi usługami platformy Azure lub aplikacjami biznesowymi:

* **Routing zdarzeń usługi Azure Digital bliźniaczych reprezentacji**: Obiekt w grafie przestrzennym, który zmienia, odebrane dane telemetryczne lub funkcja zdefiniowana przez użytkownika, która tworzy powiadomienie na podstawie wstępnie zdefiniowanych warunków, może wyzwalać zdarzenia usługi Azure Digital bliźniaczych reprezentacji. Użytkownicy mogą wysyłać te zdarzenia do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [tematów Azure Service Bus](https://azure.microsoft.com/services/service-bus/)lub [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do dalszej obróbki.

* **Telemetrię urządzenia routingu**: Oprócz zdarzeń routingu usługa Azure Digital bliźniaczych reprezentacji może również kierować komunikaty telemetryczne z nieprzetworzonych urządzeń do Event Hubs w celu uzyskania dalszych szczegółowych informacji i analizy. Te typy komunikatów nie są przetwarzane przez usługę Azure Digital bliźniaczych reprezentacji. Są one przekazywane tylko do centrum zdarzeń.

Użytkownicy mogą określać jeden lub więcej punktów końcowych ruchu wychodzącego w celu wysyłania zdarzeń lub przesyłania dalej komunikatów. Zdarzenia i komunikaty będą wysyłane do punktów końcowych zgodnie z tymi wstępnie zdefiniowanymi preferencjami routingu. Innymi słowy użytkownicy mogą określić określony punkt końcowy do odbierania zdarzeń operacji wykresu, drugi do odbierania zdarzeń telemetrii urządzenia itd.

![Routing zdarzeń usługi Azure Digital bliźniaczych reprezentacji][1]

Routing do Event Hubs zachowuje kolejność, w której wysyłane są komunikaty telemetryczne. Tak więc docierają do punktu końcowego w tej samej kolejności, w jakiej zostały pierwotnie odebrane. Event Grid i Service Bus nie gwarantuje, że punkty końcowe będą odbierać zdarzenia w takiej samej kolejności, w jakiej wystąpiły. Jednak schemat zdarzenia zawiera sygnaturę czasową, która może służyć do identyfikowania kolejności po nadejściu zdarzeń w punkcie końcowym.

## <a name="route-implementation"></a>Implementacja trasy

Usługa Azure Digital bliźniaczych reprezentacji obecnie obsługuje następujące **EndpointTypes**:

* **EventHub** jest punktem końcowym parametrów połączenia Event Hubs.
* **ServiceBus** jest punktem końcowym parametrów połączenia Service Bus.
* **EventGrid** jest punktem końcowym parametrów połączenia Event Grid.

Usługa Azure Digital bliźniaczych reprezentacji obecnie obsługuje następujące elementy **EventType** , które zostaną wysłane do wybranego punktu końcowego:

* **DeviceMessages** to komunikaty telemetryczne wysyłane z urządzeń użytkowników i przekazywane przez system.
* **TopologyOperation** jest operacją, która zmienia wykres lub metadane grafu. Przykładem jest dodanie lub usunięcie jednostki, na przykład spacja.
* **SpaceChange** to zmiana obliczonej wartości miejsca, która wynika z komunikatu telemetrii urządzenia.
* **SensorChange** to zmiana obliczonej wartości czujnika, która wynika z komunikatów telemetrycznych urządzenia.
* **UdfCustom** to niestandardowe powiadomienie z funkcji zdefiniowanej przez użytkownika.

> [!IMPORTANT]  
> Nie wszystkie **EndpointTypes** obsługują wszystkie **zdarzenia**.
> Zapoznaj się z poniższą tabelą dla elementu **EventType** , które są dozwolone dla każdego elementu **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Aby uzyskać więcej informacji na temat tworzenia punktów końcowych i przykładów schematu zdarzeń, zobacz [wyjście i punkty końcowe](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o ograniczeniach [usługi](concepts-service-limits.md)Azure Digital bliźniaczych reprezentacji w wersji zapoznawczej, zapoznaj się z limitami

- Aby wypróbować próbkę Digital bliźniaczych reprezentacji na platformie Azure, zapoznaj się z [przewodnikiem Szybki Start, aby znaleźć dostępne pokoje](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
