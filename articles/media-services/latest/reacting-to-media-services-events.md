---
title: Reagowanie na zdarzenia usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania usługi Azure Event Grid do subskrybowania zdarzeń usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887888"
---
# <a name="handling-event-grid-events"></a>Obsługa zdarzeń usługi Event Grid

Zdarzenia usługi Media Services umożliwiają aplikacjom reagowanie na różne zdarzenia (na przykład zdarzenie zmiany stanu zadania) przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak usługi Azure [Functions,](https://azure.microsoft.com/services/functions/) [Usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), a nawet do własnego elementu Webhook i płacisz tylko za to, czego używasz. Aby uzyskać informacje o cenach, zobacz [Ustalanie cen w siatce zdarzeń](https://azure.microsoft.com/pricing/details/event-grid/).

Dostępność zdarzeń usługi Media Services jest powiązana z [dostępnością](../../event-grid/overview.md) usługi Event Grid i stanie się dostępna w innych regionach, tak jak w przypadku usługi Event Grid.  

## <a name="media-services-events-and-schemas"></a>Zdarzenia i schematy usługi Media Services

Siatka zdarzeń używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania wiadomości zdarzeń do subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie usługi Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft.Media.".

Aby uzyskać więcej informacji, zobacz [Schematy zdarzeń usługi Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Praktyki dotyczące spożywania zdarzeń

Aplikacje obsługujące zdarzenia usługi Media Services powinny stosować się do kilku zalecanych rozwiązań:

* Ponieważ wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat wiadomości, aby upewnić się, że pochodzi z oczekiwanego konta magazynu.
* Podobnie sprawdź, czy eventType jest jednym jesteś przygotowany do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymasz będą typy, których oczekujesz.
* Ignoruj pola, których nie rozumiesz.  Ta praktyka pomoże Ci zachować odporność na nowe funkcje, które mogą być dodawane w przyszłości.
* Użyj prefiksu "tematu" i dopasowania sufiksu, aby ograniczyć zdarzenia do określonego zdarzenia.

> [!NOTE]
> Zdarzenia podlegają umowie dotyczącej poziomu usług sieci zdarzeń [(SLA).](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) Jeśli chcesz otrzymywać powiadomienia o zdarzeniach przy użyciu interfejsów API, zobacz przykłady dotyczące sposobu korzystania ze zdarzeń za pomocą [pliku .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) lub [java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie zdarzeń - portal](monitor-events-portal-how-to.md)
* [Monitorowanie zdarzeń — interfejs wiersza polecenia](job-state-events-cli-how-to.md)
