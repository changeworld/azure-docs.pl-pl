---
title: Reagowanie na zdarzenia usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Usługa Azure Event Grid do subskrybowania zdarzenia usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465541"
---
# <a name="reacting-to-media-services-events"></a>Reagowanie na zdarzenia usługi Media Services

Zdarzenia usługi Media Services umożliwiają aplikacjom reagować na różne zdarzenia (na przykład zadanie zdarzeniu zmiany stanu) przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług. Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego elementu Webhook, a tylko płatność co Możesz użyć. Aby uzyskać informacje o cenach, zobacz [cen usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Dostępność dla zdarzeń usługi Media Services jest powiązany z usługi Event Grid [dostępności](../../event-grid/overview.md) i będą dostępne w innych regionach, jak usługa Event Grid.  

## <a name="available-media-services-events"></a>Dostępne zdarzeń usługi Media Services

Korzysta z usługi Event grid [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów.  Obecnie subskrypcje zdarzeń usługi Media Services mogą obejmować następujące zdarzenia:  

|Nazwa zdarzenia|Opis|
|----------|-----------|
| Microsoft.Media.JobStateChange| Wywoływane, gdy stan zmiany zadania. |
| Microsoft.Media.LiveEventConnectionRejected | Kodera połączenie jest odrzucane. |
| Microsoft.Media.LiveEventEncoderConnected | Koder ustanawia połączenie z wydarzenia na żywo. |
| Microsoft.Media.LiveEventEncoderDisconnected | Odłącza kodera. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Serwer multimediów fragmentu danych spada, ponieważ jest za późno lub ma nakładające się sygnatura czasowa (sygnatura czasowa nowych fragmentów danych jest mniejsza niż czas zakończenia poprzedniego fragmentu danych). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media serwer odbiera pierwszy fragment danych dla każdej ścieżki, w usłudze stream ani połączenia. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Serwer multimediów wykrywa audio i wideo strumieni nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Serwer multimediów wykrywa, że jedną z dwóch strumieni wideo pochodzących z kodera nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikowany co 20 sekund dla każdej ścieżki, w przypadku zdarzenia na żywo jest uruchomiona. Zapewnia obsługę podsumowanie kondycji. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Serwer multimediów wykrywa przerwa w ścieżce przychodzącej. |

## <a name="event-schema"></a>Schemat zdarzenia

Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych.  Zdarzenia usługi Media Services można zidentyfikować, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.Media.".

Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje obsługujące zdarzeń usługi Media Services, należy wykonać kilka zalecanych rozwiązań:

* Zgodnie z wieloma subskrypcjami można skonfigurować w celu kierowanie zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, nie przyjęto założenie, że są zdarzenia z konkretnego źródła, ale Sprawdź tematu wiadomości, aby upewnić się, że pochodzi on z konta magazynu, które są oczekiwane.
* Podobnie upewnij się, że zdarzenia jest przygotowany do procesów, a nie należy zakładać, że wszystkie zdarzenia, które otrzymujesz będzie typów, których oczekujesz.
* Ignoruj pola, których nie rozumie.  Praktyka ta będzie zapewnić Ci odporne na błędy do nowych funkcji, które mogłyby zostać dodane w przyszłości.
* Użyj dopasowania prefiksu i sufiksu "temat", aby ograniczyć zdarzeń do określonego zdarzenia.

## <a name="next-steps"></a>Kolejne kroki

[Pobierz zdarzenia stanu zadania](job-state-events-cli-how-to.md)
