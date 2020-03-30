---
title: Zdarzenia i wiadomości routingu — cyfrowe bliźniacze usługi Azure | Dokumenty firmy Microsoft
description: Omówienie zdarzeń routingu i komunikatów do punktów końcowych usługi za pomocą usługi Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862360"
---
# <a name="routing-iot-events-and-messages"></a>Routing zdarzeń i wiadomości IoT

Rozwiązania Internetu rzeczy często łączą kilka zaawansowanych usług, które obejmują pamięć masową, analitykę i inne. W tym artykule opisano sposób łączenia aplikacji usługi Azure Digital Twins z usługami analizy platformy Azure, sztucznej inteligencji i magazynu, aby zapewnić im głębsze szczegółowe informacje i funkcje.

## <a name="route-types"></a>Typy tras  

Usługa Azure Digital Twins oferuje dwa sposoby łączenia zdarzeń IoT z innymi usługami platformy Azure lub aplikacjami biznesowymi:

* **Routing zdarzeń usługi Azure Digital Twins:** Obiekt na wykresie przestrzennym, który się zmienia, dane telemetryczne, które są odbierane lub funkcja zdefiniowana przez użytkownika, która tworzy powiadomienie na podstawie wstępnie zdefiniowanych warunków może wyzwalać zdarzenia usługi Azure Digital Twins. Użytkownicy mogą wysyłać te zdarzenia do [usługi Azure Event Hubs, tematów](https://azure.microsoft.com/services/event-hubs/) [usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/)lub usługi Azure [Event Grid](https://azure.microsoft.com/services/event-grid/) w celu dalszego przetwarzania.

* **Telemetria urządzenia routingu:** Oprócz zdarzeń routingu usługa Azure Digital Twins może również kierować nieprzetworzone komunikaty telemetryczne urządzenia do centrów zdarzeń w celu uzyskania dalszych szczegółowych informacji i analiz. Te typy wiadomości nie są przetwarzane przez usługę Azure Digital Twins. I są one przekazywane tylko do centrum zdarzeń.

Użytkownicy mogą określić jeden lub więcej punktów końcowych wychodzących do wysyłania zdarzeń lub przesyłania dalej wiadomości. Zdarzenia i komunikaty będą wysyłane do punktów końcowych zgodnie z tymi wstępnie zdefiniowanymi preferencjami routingu. Innymi słowy użytkownicy mogą określić określony punkt końcowy do odbierania zdarzeń operacji wykresu, inny do odbierania zdarzeń telemetrycznych urządzenia i tak dalej.

[![Routing zdarzeń usługi Azure Digital Twins](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Routing do centrów zdarzeń zachowuje kolejność wysyłania wiadomości telemetrycznych. Więc docierają do punktu końcowego w tej samej kolejności, w jakiej zostały pierwotnie odebrane. 

Usługa Siatka zdarzeń i usługa Service Bus nie gwarantują, że punkty końcowe będą odbierać zdarzenia w tej samej kolejności, w jakiej wystąpiły. Jednak schemat zdarzenia zawiera sygnaturę czasową, która może służyć do identyfikowania zamówienia po dojściu do punktu końcowego.

## <a name="route-implementation"></a>Realizacja trasy

Usługa Azure Digital Twins obsługuje obecnie następujące **typy punktów końcowych:**

* **EventHub** jest punktem końcowym ciągu połączenia usługi Event Hubs.
* **ServiceBus** jest punktem końcowym ciągu połączenia usługi Service Bus.
* **EventGrid** jest punktem końcowym połączenia sieci zdarzeń.

Usługa Azure Digital Twins obsługuje obecnie następujące **typy zdarzeń,** które zostaną wysłane do wybranego punktu końcowego:

* **DeviceMessages** są wiadomości telemetryczne wysyłane z urządzeń użytkowników i przekazywane przez system.
* **TopologyOperation** to operacja, która zmienia wykres lub metadane wykresu. Przykładem jest dodawanie lub usuwanie jednostki, takich jak spacja.
* **SpaceChange** to zmiana wartości obliczeniowej przestrzeni, która wynika z komunikatu telemetryczego urządzenia.
* **SensorChange** to zmiana wartości obliczeniowej czujnika, która wynika z komunikatu telemetrycznego urządzenia.
* **UdfCustom** to niestandardowe powiadomienie z funkcji zdefiniowanej przez użytkownika.

> [!IMPORTANT]  
> Nie wszystkie **typy punktów końcowych** obsługują wszystkie **typy zdarzeń**.
> Przejrzyj poniższą tabelę dla **eventTypes,** które są dozwolone dla każdego **typu punktu końcowego**.

|             | UrządzeniaSessages | TopologiaOperacja | SpaceChange (Zmiany w przestrzeni) | Zmiany czujników | UdfCustom (Polski) |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid ( EventGrid )|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Aby uzyskać więcej informacji na temat tworzenia punktów końcowych i przykładów schematu zdarzeń, przeczytaj pozycję [Ruch wychodzący i punkty końcowe](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o limitach wersji zapoznawczej usługi Azure Digital Twins, przeczytaj [artykuł Limity usług w wersji Zapoznawczej.](concepts-service-limits.md)

- Aby wypróbować przykład usługi Azure Digital Twins, przeczytaj [przewodnik Szybki start, aby znaleźć dostępne pokoje.](quickstart-view-occupancy-dotnet.md)