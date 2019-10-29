---
title: Architektury sterowane zdarzeniami na brzegu — Azure Event Grid na IoT Edge
description: Użyj Azure Event Grid jako modułu na IoT Edge na potrzeby przesyłania dalej zdarzeń między modułami, urządzeniami brzegowymi i chmurą.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: e03429ed3df5bd3518d5e5194bd842b9a4f290ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991946"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Co to jest Azure Event Grid na Azure IoT Edge?
Event Grid na IoT Edge zapewnia moc i elastyczność Azure Event Grid do krawędzi dla wszystkich scenariuszy dotyczących publikowania/podzadań i zdarzeń. Twórz tematy, Publikuj zdarzenia i Subskrybuj wiele miejsc docelowych, niezależnie od tego, czy są one modułami na tym samym urządzeniu, innymi urządzeniami brzegowymi lub usługami w chmurze.

Podobnie jak w przypadku chmury, Event Grid na IoT Edge module obsługuje routing, filtrowanie i niezawodne dostarczanie zdarzeń na dużą skalę. Filtruj zdarzenia, aby upewnić się, że tylko odpowiednie zdarzenia są wysyłane do różnych programów obsługi zdarzeń przy użyciu zaawansowanego ciągu, numerycznego i filtrów logicznych. Logika ponawiania sprawdza, czy zdarzenie osiągnie moduł docelowy, Urządzenie brzegowe lub usługę w chmurze, nawet jeśli nie jest dostępna w momencie publikacji. Umożliwia ona korzystanie Event Grid na IoT Edge jako zaawansowany magazyn i mechanizm do przesyłania dalej.

Event Grid na IoT Edge obsługuje zarówno CloudEvents v 1.0, jak i schematy zdarzeń niestandardowych. Obsługuje ona również tę samą semantykę publikowania i subskrybowania, co Event Grid w chmurze w celu zapewnienia łatwego współdziałania z platformą Azure i innymi firmami.

Ten artykuł zawiera omówienie Azure Event Grid w IoT Edge. Aby uzyskać instrukcje krok po kroku dotyczące korzystania z tego modułu na urządzeniach brzegowych, zobacz [publikowanie, subskrybowanie zdarzeń lokalnie](pub-sub-events-webhook-local.md). 

![Event Grid IoT Edge modelu źródeł i programów obsługi](../media/edge-overview/functional-model.png)

Na tym obrazie przedstawiono niektóre sposoby używania Event Grid na IoT Edge i nie jest to pełna lista obsługiwanych funkcji.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Kiedy używać Event Grid na IoT Edge

Event Grid na IoT Edge został utworzony w celu zapewnienia jednolitego, łatwego w użyciu i niezawodnego modelu zdarzeń do tworzenia architektur opartych na zdarzeniach między krawędzią a chmurą.

Event Grid na IoT Edge został utworzony przy użyciu symetrycznego obszaru środowiska uruchomieniowego w usłudze w chmurze platformy Azure, dzięki czemu można używać tych samych zdarzeń i wywołań interfejsu API wszędzie tam, gdzie jest to potrzebne. Niezależnie od tego, czy w chmurze zostanie zapisana/podrzędna, na granicy, czy między nimi Event Grid na IoT Edge można teraz wybrać jedno z nich.

Użyj Event Grid na IoT Edge, aby wyzwolić proste przepływy pracy między modułami. Na przykład utwórz temat i Opublikuj zdarzenia "Magazyn obiektów BLOB utworzone" z modułu magazynu w temacie. Teraz można subskrybować jedną lub kilka funkcji lub modułów niestandardowych w tych tematach.

Zwiększ funkcjonalność między urządzeniami brzegowymi. Jeśli publikujesz zdarzenia modułu obiektów blob i chcesz korzystać z mocy obliczeniowej wielu blisko urządzeń brzegowych, Utwórz subskrypcje między urządzeniami.

Na koniec Połącz się z chmurą. Jeśli zdarzenia modułu obiektów BLOB mają być okresowo synchronizowane z chmurą, użyj większej ilości obliczeń dostępnych w chmurze lub Wyślij przetworzone dane w górę, a następnie Utwórz dodatkowe subskrypcje usług w chmurze.

Event Grid na IoT Edge zapewnia elastyczną i niezawodną architekturę zdarzeń.

## <a name="event-sources"></a>Źródła zdarzeń

Podobnie jak w chmurze, Event Grid na IoT Edge umożliwia bezpośrednie integrację modułów do tworzenia architektur opartych na zdarzeniach. Obecnie zdarzenia mogą być wysyłane do Event Grid na IoT Edge z:

* Usługa Azure Blob Storage w usłudze IoT Edge
* Źródła CloudEvents
* Moduły niestandardowe & kontenerów za pośrednictwem protokołu HTTP POST

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Event Grid na IoT Edge został utworzony w celu wysyłania zdarzeń do dowolnego miejsca. Obecnie obsługiwane są następujące miejsca docelowe:

* Inne moduły, w tym IoT Hub, funkcje i moduły niestandardowe
* Inne urządzenia brzegowe
* Usługi hostowane w chmurze, w tym Azure Event Grid i Azure Functions
* Elementy webhook

## <a name="supported-environments"></a>Obsługiwane środowiska
Obecnie obsługiwane są środowiska Windows 64-bitowe, Linux 64-bit i ARM 32-bit.

## <a name="concepts"></a>Pojęcia

Istnieje pięć koncepcji w Azure Event Grid, które umożliwiają rozpoczęcie pracy:

* **Zdarzenia** — co się stało.
* **Źródła zdarzeń** — w przypadku których miało miejsce zdarzenie.
* **Tematy** — punkt końcowy, w którym wydawcy wysyłają zdarzenia.
* **Subskrypcje zdarzeń** — punkt końcowy lub wbudowany mechanizm do kierowania zdarzeń, czasami do więcej niż jednego programu obsługi. Subskrypcje są również używane przez procedury obsługi w celu inteligentnego filtrowania zdarzeń przychodzących.
* **Programy obsługi zdarzeń** — aplikacja lub usługa, która reaguje na zdarzenie.

## <a name="cost"></a>Koszt

Event Grid on IoT Edge jest bezpłatny w publicznej wersji zapoznawczej.

## <a name="issues"></a>Problemy
Zgłoś wszelkie problemy związane z używaniem Event Grid na IoT Edge w [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).

## <a name="next-steps"></a>Następne kroki

* [Publikuj, Subskrybuj zdarzenia lokalnie](pub-sub-events-webhook-local.md)
* [Publikowanie, subskrybowanie zdarzeń w chmurze](pub-sub-events-webhook-cloud.md)
* [Prześlij dalej zdarzenia do Event Grid chmury](forward-events-event-grid-cloud.md)
* [Przekazuj zdarzenia do IoTHub](forward-events-iothub.md)
* [Reagowanie na zdarzenia Blob Storage lokalnie](react-blob-storage-events-locally.md)