---
title: Routing zdarzeń i komunikatów za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie routingu zdarzeń i komunikatów z punktami końcowymi usługi za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324289"
---
# <a name="routing-events-and-messages"></a>Routing zdarzeń i komunikatów

Rozwiązania IoT często łączą kilka zaawansowanych usług, w tym magazyn, analizy i inne. W tym artykule opisano sposób łączenia aplikacji Twins cyfrowych platformy Azure do usług platformy Azure analytics, sztucznej Inteligencji i magazynu wzbogacić je przy użyciu bardziej szczegółowe informacje i funkcje.

## <a name="route-types"></a>Typy trasy

Cyfrowy Twins oferuje zdarzeń IoT integrowanie innych usług platformy Azure lub aplikacje biznesowe na dwa sposoby:

* **Zdarzenia routingu cyfrowego bliźniaczych reprezentacji**: zdarzenia Twins cyfrowych platformy Azure mogą być wyzwalane, gdy obiekt w zmian grafu przestrzennych, po odebraniu danych telemetrycznych, lub gdy zdefiniowane przez użytkownika funkcja tworzy powiadomienia na podstawie wstępnie zdefiniowanych warunków. Użytkownicy mogą wysyłać te zdarzenia w celu [usługi Event Hubs](https://azure.microsoft.com/services/event-hubs/), [tematów usługi Service Bus](https://azure.microsoft.com/services/service-bus/), lub [siatki zdarzeń](https://azure.microsoft.com/services/event-grid/) do dalszego przetwarzania.

* **Routing danych telemetrycznych z urządzenia**: Oprócz routingu zdarzeń Twins cyfrowych platformy Azure również komunikaty można kierować do urządzenie niesformatowane telemetrii do usługi Event Hubs do dalszych informacji i analiz. Tego rodzaju wiadomości nie są przetwarzane przez Twins cyfrowych, a tylko są one przekazywane do **Centrum zdarzeń**.

Użytkownicy mogą określić jeden lub więcej punktów końcowych ruch wychodzący do wysyłania zdarzeń lub do przekazywania wiadomości. Zdarzenia i komunikaty będą wysyłane do punktów końcowych, zgodnie z tych wstępnie zdefiniowanych preferencji routingu. Innymi słowy użytkownicy mogą określić pewne jeden punkt końcowy do odbierania zdarzeń operacji programu graph, innej, aby odbierać zdarzenia telemetrii urządzenia i tak dalej.

![Cyfrowy bliźniaczych reprezentacji zdarzenia, Routing][1]

Routing do **usługi Event Hubs** będzie utrzymywać kolejność, w których dane telemetryczne są wysyłane wiadomości, tak aby były odbierane pierwotnie przychodzących w punkcie końcowym w takiej samej kolejności. **Usługa Event Grid** i **usługi Service Bus** nie gwarantuje punktów końcowych zostaną odebrane zdarzenia w tej samej kolejności ich wystąpienia. Schemat zdarzeń jednak zawierać sygnaturę czasową, który może służyć do identyfikowania kolejności po przyjeździe zdarzenia do punktu końcowego.

## <a name="route-implementation"></a>Implementacja trasy

Usługa Azure cyfrowego bliźniaczych reprezentacji obecnie obsługuje następujące **EndpointTypes**:

* **Centrum EventHub**: jest punktem końcowym parametry połączenia Centrum zdarzeń.
* **Usługi ServiceBus**: jest punktem końcowym parametry połączenia usługi Service Bus.
* **EventGrid**: jest punktem końcowym parametry połączenia usługi Event Grid.

Twins cyfrowych platformy Azure obsługuje obecnie następujące **EventTypes** który będą wysyłane do wybranego punktu końcowego:

* **DeviceMessages**: są komunikaty telemetryczne wysyłane z urządzeń użytkowników i przekazywane przez system.
* **TopologyOperation**: operacje, które zmieniają wykres lub metadanych wykresu. Na przykład dodawanie lub usuwanie jednostki, takie jak spacja.
* **SpaceChange**: są zmiany do obszaru obliczona wartość wyniku komunikaty telemetryczne urządzenia.
* **SensorChange**: są zmiany w czujnik obliczona wartość wyniku komunikaty telemetryczne urządzenia.
* **UdfCustom**: niestandardowych powiadomień w funkcji zdefiniowanej przez użytkownika.

> [!IMPORTANT]
> Nie wszystkie **EndpointTypes** obsługują wszystkie **EventTypes**.
> Zobacz w poniższej tabeli **EventTypes** dozwolone dla każdego **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **magistrali usług**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia punktów końcowych i przykłady schematu zdarzeń, zobacz [punktów końcowych i wychodzący](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat limitów publicznej wersji zapoznawczej, przeczytaj [Twins cyfrowych platformy Azure w wersji zapoznawczej limity](concepts-service-limits.md).

Aby wypróbować przykładowe Twins cyfrowych platformy Azure, przeczytaj [szybkiego startu, aby znaleźć dostępne pokoje](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png