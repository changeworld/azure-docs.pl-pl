---
title: Architektury oparte na zdarzeniach na krawędzi — usługa Azure Event Grid w usłudze IoT Edge
description: Użyj usługi Azure Event Grid jako modułu w usłudze IoT Edge do przesyłania dalej zdarzeń między modułami, urządzeniami brzegowymi i chmurą.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844664"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Co to jest usługa Azure Event Grid w usłudze Azure IoT Edge?
Usługa Event Grid on IoT Edge zapewnia możliwości i elastyczność usługi Azure Event Grid na krawędzi. Twórz tematy, publikuj zdarzenia i subskrybuj wiele miejsc docelowych, niezależnie od tego, czy są to moduły na tym samym urządzeniu, innych urządzeniach brzegowych czy usługach w chmurze.

Podobnie jak w chmurze, usługa Event Grid na ioT Edge moduł obsługuje routingu, filtrowania i niezawodne dostarczanie zdarzeń na dużą skalę. Filtruj zdarzenia, aby upewnić się, że tylko odpowiednie zdarzenia są wysyłane do różnych programów obsługi zdarzeń przy użyciu zaawansowanych filtrów ciągów, numerycznych i logicznych. Logika ponawiania czasu pracy zapewnia, że zdarzenie dociera do miejsca docelowego, nawet jeśli nie jest dostępne w momencie publikowania. Umożliwia użycie siatki zdarzeń w uiszczanym przez IoT Edge jako mechanizmu magazynu i do przodu.

Usługa Event Grid on IoT Edge obsługuje zarówno cloudevents w wersji 1.0, jak i niestandardowe schematy zdarzeń. Obsługuje również te same semantyka Pub/Sub jako event grid w chmurze dla łatwej współdziałania.

Ten artykuł zawiera omówienie usługi Azure Event Grid w usłudze IoT Edge. Aby uzyskać instrukcje krok po kroku dotyczące używania tego modułu na krawędzi, zobacz [Publikowanie, subskrybowanie zdarzeń lokalnie](pub-sub-events-webhook-local.md). 

![Siatka zdarzeń w modelu usługi IoT Edge źródeł i programów obsługi](../media/edge-overview/functional-model.png)

Ten obraz przedstawia niektóre sposoby korzystania z siatki zdarzeń w u klienta IoT Edge i nie jest wyczerpującą listą obsługiwanych funkcji.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Kiedy używać siatki zdarzeń na krawędzi IoT Edge

Usługa Event Grid on IoT Edge zapewnia łatwy w użyciu, niezawodny model zdarzeń między krawędzią a chmurą.

Usługa Event Grid on IoT Edge jest zbudowana z symetrycznego obszaru środowiska uruchomieniowego w usłudze w chmurze platformy Azure, dzięki czemu można używać tych samych zdarzeń i wywołań interfejsu API w dowolnym miejscu. Niezależnie od tego, czy robisz pub/sub w chmurze, na krawędzi, czy między nimi, usługa Event Grid na ioT Edge może być teraz twoim rozwiązaniem.

Użyj siatki zdarzeń na ioT Edge, aby wyzwolić proste przepływy pracy między modułami. Na przykład utwórz temat i opublikuj zdarzenia "utworzony obiekt magazynu" z modułu magazynu do tematu. Teraz można subskrybować jedną lub kilka funkcji lub modułów niestandardowych do tych tematów.

Rozszerz funkcjonalność między urządzeniami brzegowymi. Jeśli publikujesz zdarzenia modułu obiektów blob i chcesz użyć mocy obliczeniowej wielu urządzeń w pobliżu krawędzi, utwórz subskrypcje między urządzeniami.

Na koniec połącz się z chmurą. Jeśli zdarzenia modułu obiektu blob mają być okresowo synchronizowane z chmurą, użyj większej ilości obliczeń dostępnych w chmurze lub wyślij przetworzone dane, utwórz dodatkowe subskrypcje usług w chmurze.

Usługa Event Grid on IoT Edge zapewnia elastyczną i niezawodną architekturę zdarzeń oddzielonych.

## <a name="event-sources"></a>Źródła zdarzeń

Podobnie jak w chmurze, usługa Event Grid w usłudze IoT Edge umożliwia bezpośrednią integrację między modułami w celu tworzenia architektur opartych na zdarzeniach. Obecnie zdarzenia mogą być wysyłane do siatki zdarzeń na IoT Edge z:

* Usługa Azure Blob Storage w usłudze IoT Edge
* Źródła CloudEvents
* Moduły niestandardowe & kontenery za pośrednictwem protokołu HTTP POST

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Usługa Event Grid w usłudze IoT Edge jest przeznaczona do wysyłania zdarzeń w dowolnym miejscu. Obecnie obsługiwane są następujące miejsca docelowe:

* Inne moduły, w tym IoT Hub, funkcje i moduły niestandardowe
* Inne urządzenia brzegowe
* Elementy webhook
* Usługa w chmurze usługi usługi usługi azure event grid
* Usługa Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus
* Kolejki usługi Storage

## <a name="supported-environments"></a>Obsługiwane środowiska
Obecnie obsługiwane są środowiska 64-bitowe, Linux 64-bitowe i ARM 32-bitowe.

## <a name="concepts"></a>Pojęcia

W usłudze Azure Event Grid dostępnych jest pięć koncepcji, które umożliwiają rozpoczęcie pracy:

* **Wydarzenia** — Co się stało.
* **Źródła zdarzeń** — gdzie miało miejsce zdarzenie.
* **Tematy** — punkt końcowy, w którym wydawcy wysyłają zdarzenia.
* **Subskrypcje zdarzeń** — punkt końcowy lub wbudowany mechanizm do kierowania zdarzeń, czasami do więcej niż jednego programu obsługi. Subskrypcje są również używane przez procedury obsługi w celu inteligentnego filtrowania zdarzeń przychodzących.
* **Programy obsługi zdarzeń** — aplikacja lub usługa, która reaguje na zdarzenie.

## <a name="cost"></a>Koszty

Usługa Event Grid na ioT Edge jest bezpłatna podczas publicznej wersji zapoznawczej.

## <a name="issues"></a>Problemy
Zgłoś wszelkie problemy z używaniem [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)siatki zdarzeń w umysłek IoT Edge pod adresem .

## <a name="next-steps"></a>Następne kroki

* [Publikowanie, subskrybowanie wydarzeń lokalnie](pub-sub-events-webhook-local.md)
* [Publikowanie, subskrybowanie zdarzeń w chmurze](pub-sub-events-webhook-cloud.md)
* [Przesyłanie dalej zdarzeń do chmury usługi Event Grid](forward-events-event-grid-cloud.md)
* [Przesyłanie dalej wydarzeń do usługi IoTHub](forward-events-iothub.md)
* [Reagowanie na zdarzenia usługi Blob Storage lokalnie](react-blob-storage-events-locally.md)