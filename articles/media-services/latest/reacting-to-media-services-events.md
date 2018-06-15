---
title: Reaguje na zdarzenia usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Użyj siatki zdarzeń Azure, aby subskrybować zdarzenia usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788151"
---
# <a name="reacting-to-media-services-events"></a>Reaguje na zdarzenia usługi Media Services

Zdarzenia usługi Media Services umożliwiają aplikacjom zareagować na różnych zdarzeń (na przykład zadanie zdarzeniu zmiany stanu) przy użyciu nowoczesnych architekturach niekorzystającą. Robi to bez konieczności skomplikowane, kodu lub mało wydajne i ekonomiczne sondowania usług. Zamiast tego zdarzenia są usuwane [siatki zdarzeń Azure](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego elementu Webhook i tylko płatności co Możesz użyć. Aby uzyskać informacje o cenach, zobacz [cennik siatki zdarzeń](https://azure.microsoft.com/pricing/details/event-grid/).

Dostępność usługi Media Services zdarzenia jest powiązany z siatki zdarzeń [dostępności](../../event-grid/overview.md) i będą dostępne w różnych regionach, tak jak w przypadku zdarzeń siatki.  

## <a name="available-media-services-events"></a>Dostępne zdarzenia usługi Media Services

Siatka zdarzeń używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do wyznaczania tras wiadomościom zdarzeń do subskrybentów.  Obecnie subskrypcji zdarzeń usługi Media Services może zawierać następującego typu zdarzenia:  

|Nazwa zdarzenia|Opis|
|----------|-----------|
| Microsoft.Media.JobStateChange| Wywoływane, gdy stan zmiany zadania. |

## <a name="event-schema"></a>Schematu zdarzeń

Zdarzenia usługi Media Services zawierają wszystkich informacji potrzebnych do reagowania na zmiany w danych.  Można zidentyfikować zdarzeń usługi Media Services, ponieważ właściwość eventType rozpoczyna się od "Microsoft.Media.".

Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzeń

Aplikacje obsługujące zdarzenia usługi Media Services należy wykonać kilka zaleceń:

* Jak wiele subskrypcji można skonfigurować do trasy zdarzenia do tej procedury obsługi zdarzeń, jest ważne, nie przyjęto założenie, że są zdarzenia z danego źródła, ale sprawdź temat wiadomości, aby upewnić się, że pochodzą z konta magazynu, które są oczekiwane.
* Podobnie Sprawdź, czy zdarzenia jest jednym są przygotowane do procesu, a nie założono, że wszystkie zdarzenia, które otrzymujesz będzie oczekiwane typy.
* Ignoruj pola, które nie zrozumieć.  Takie rozwiązanie będzie zapewnić Ci odporne na nowe funkcje, które mogą zostać dodane w przyszłości.
* Użyj dopasowań prefiksu i sufiksu "temat", aby ograniczyć zdarzenia do konkretnego zdarzenia.

## <a name="next-steps"></a>Kolejne kroki

[Zadanie stanu zdarzenia](job-state-events-cli-how-to.md)
