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
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996173"
---
# <a name="handling-event-grid-events"></a>Obsługa zdarzeń usługi Event Grid

Zdarzenia usługi Media Services umożliwiają aplikacjom reagować na różne zdarzenia (na przykład zadanie zdarzeniu zmiany stanu) przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług. Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego elementu Webhook, a tylko płatność co Możesz użyć. Aby uzyskać informacje o cenach, zobacz [cen usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Dostępność dla zdarzeń usługi Media Services jest powiązany z usługi Event Grid [dostępności](../../event-grid/overview.md) i będą dostępne w innych regionach, jak usługa Event Grid.  

## <a name="media-services-events-and-schemas"></a>Schematy i zdarzeń usługi Media Services

Korzysta z usługi Event grid [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Zdarzenia usługi Media Services można zidentyfikować, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.Media.".

Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje obsługujące zdarzeń usługi Media Services, należy wykonać kilka zalecanych rozwiązań:

* Zgodnie z wieloma subskrypcjami można skonfigurować w celu kierowanie zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, nie przyjęto założenie, że są zdarzenia z konkretnego źródła, ale Sprawdź tematu wiadomości, aby upewnić się, że pochodzi on z konta magazynu, które są oczekiwane.
* Podobnie upewnij się, że zdarzenia jest przygotowany do procesów, a nie należy zakładać, że wszystkie zdarzenia, które otrzymujesz będzie typów, których oczekujesz.
* Ignoruj pola, których nie rozumie.  Praktyka ta będzie zapewnić Ci odporne na błędy do nowych funkcji, które mogłyby zostać dodane w przyszłości.
* Użyj dopasowania prefiksu i sufiksu "temat", aby ograniczyć zdarzeń do określonego zdarzenia.

## <a name="next-steps"></a>Kolejne kroki

[Pobierz zdarzenia stanu zadania](job-state-events-cli-how-to.md)
