---
title: Routing zdarzeń i komunikatów za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie routingu zdarzeń i komunikatów z punktami końcowymi usługi za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: b7ace0718ea0fad0b746a40c90acff487ae314d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926296"
---
# <a name="routing-events-and-messages"></a>Routing zdarzeń i komunikatów

Rozwiązania IoT często łączą kilka zaawansowanych usług, które obejmują magazyn, analizy i inne. W tym artykule opisano sposób łączenia aplikacji Twins cyfrowych platformy Azure do usług platformy Azure analytics, sztucznej Inteligencji i magazynu ciągowych lepszy wgląd i funkcje.

## <a name="route-types"></a>Typy trasy  

Twins cyfrowych platformy Azure oferuje zdarzeń IoT integrowanie innych usług platformy Azure lub aplikacje biznesowe na dwa sposoby:

* **Zdarzenia routingu Twins cyfrowych platformy Azure**: Obiekt w przestrzenne programu graph tej zmiany, dane telemetryczne, które zostanie odebrana, lub funkcji zdefiniowanej przez użytkownika, który tworzy powiadomienia na podstawie wstępnie zdefiniowanych warunków można wyzwalać zdarzenia Twins cyfrowych platformy Azure. Użytkownicy mogą wysyłać te zdarzenia w celu [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [tematów usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/), lub [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do dalszego przetwarzania.

* **Routing danych telemetrycznych z urządzenia**: Oprócz routingu zdarzeń Twins cyfrowych platformy Azure również komunikaty można kierować do urządzenie niesformatowane telemetrii do usługi Event Hubs do dalszych informacji i analiz. Tego rodzaju komunikaty nie są przetwarzane przez Twins cyfrowych platformy Azure. I tylko są one przekazywane do Centrum zdarzeń.

Użytkownicy mogą określić jeden lub więcej punktów końcowych ruch wychodzący do wysyłania zdarzeń lub do przekazywania wiadomości. Zdarzenia i komunikaty będą wysyłane do punktów końcowych, zgodnie z tych wstępnie zdefiniowanych preferencji routingu. Innymi słowy użytkownicy mogą określić niektórych punkt końcowy do odbierania zdarzeń z operacji programu graph, innej, aby odbierać zdarzenia telemetrii urządzenia i tak dalej.

![Azure — wydarzenia cyfrowego Twins routingu][1]

Routing do usługi Event Hubs zachowuje kolejności, w której są wysyłane komunikaty telemetryczne. Dlatego przychodzących w punkcie końcowym w takiej samej kolejności jak początkowo zostały odebrane. Event Grid i Service Bus nie gwarantuje punktów końcowych zostaną odebrane zdarzenia w tej samej kolejności ich wystąpienia. Jednak schematu zdarzeń zawiera sygnaturę czasową, który może służyć do identyfikowania kolejności po przyjeździe zdarzenia do punktu końcowego.

## <a name="route-implementation"></a>Implementacja trasy

Usługa Azure cyfrowego bliźniaczych reprezentacji obecnie obsługuje następujące **EndpointTypes**:

* **Centrum EventHub** jest punktem końcowym parametry połączenia usługi Event Hubs.
* **Usługi ServiceBus** jest punktem końcowym parametry połączenia usługi Service Bus.
* **EventGrid** jest punktem końcowym parametry połączenia usługi Event Grid.

Twins cyfrowych platformy Azure obsługuje obecnie następujące **EventTypes** który będą wysyłane do wybranego punktu końcowego:

* **DeviceMessages** są komunikaty telemetryczne wysyłane z urządzeń użytkowników i przekazywane przez system.
* **TopologyOperation** jest operacją, która zmienia się wykres lub metadanych wykresu. Przykładem jest dodanie lub usunięcie jednostki, takie jak spacja.
* **SpaceChange** powoduje zmianę miejsca obliczona wartość, która wynika z komunikaty telemetryczne urządzenia.
* **SensorChange** powoduje zmianę czujnik obliczona wartość, która wynika z komunikaty telemetryczne urządzenia.
* **UdfCustom** niestandardowe powiadomienie z funkcji zdefiniowanych przez użytkownika.

> [!IMPORTANT]  
> Nie wszystkie **EndpointTypes** obsługują wszystkie **EventTypes**.
> Zobacz w poniższej tabeli **EventTypes** dozwolone dla każdego **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Aby uzyskać więcej informacji na temat tworzenia punktów końcowych i przykłady schematu zdarzeń, zobacz [wychodzących oraz punktów końcowych](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Kolejne kroki

- Informacje na temat Twins cyfrowych platformy Azure w wersji zapoznawczej limity, zobacz [limity usługi publicznej wersji zapoznawczej](concepts-service-limits.md).

- Aby wypróbować przykładowe Twins cyfrowych platformy Azure, zobacz [szybkiego startu, aby znaleźć dostępne pokoje](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
