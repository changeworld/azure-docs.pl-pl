---
title: Zmienne systemowe w Azure Data Factory | Microsoft Docs
description: W tym artykule opisano zmienne systemowe obsługiwane przez Azure Data Factory. Te zmienne można używać w wyrażeniach podczas definiowania jednostek Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: b6c04edb3fc843dfe1b9176dc06bf312473591c3
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376442"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Zmienne systemowe obsługiwane przez Azure Data Factory
W tym artykule opisano zmienne systemowe obsługiwane przez Azure Data Factory. Te zmienne można używać w wyrażeniach podczas definiowania jednostek Data Factory.

## <a name="pipeline-scope"></a>Zakres potoku
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON potoku.

| Nazwa zmiennej | Opis |
| --- | --- |
| @pipeline().DataFactory |Nazwa fabryki danych, w której działa uruchomienie potoku |
| @pipeline().Pipeline |Nazwa potoku |
| @pipeline().RunId | Identyfikator określonego uruchomienia potoku |
| @pipeline().TriggerType | Typ wyzwalacza, który wywołał potok (ręczny, Scheduler) |
| @pipeline().TriggerId| Identyfikator wyzwalacza, który wywołuje potok |
| @pipeline().TriggerName| Nazwa wyzwalacza, który wywołuje potok |
| @pipeline().TriggerTime| Czas, gdy wyzwalacz, który wywołał potok. Czas wyzwalacza jest rzeczywistym czasem uruchomienia, a nie zaplanowanym czasem. Na przykład, `13:20:08.0149599Z` jest zwracany zamiast`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Zakres wyzwalania harmonogramu
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON wyzwalacza, jeśli wyzwalacz jest typu: "ScheduleTrigger."

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().scheduledTime |Czas, w którym wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Na przykład dla wyzwalacza, który jest uruchamiany co 5 minut, ta zmienna zwróci `2017-06-01T22:20:00Z` `2017-06-01T22:30:00Z` , `2017-06-01T22:25:00Z`odpowiednio.|
| @trigger().startTime |Czas, po którym **wyzwalacz jest** uruchamiany w celu wywołania uruchomienia potoku. Na przykład dla wyzwalacza, który jest uruchamiany co 5 minut, ta zmienna może zwrócić coś podobnego `2017-06-01T22:25:00.7958577Z`do `2017-06-01T22:30:00.9935483Z` tego `2017-06-01T22:20:00.4061448Z`, odpowiednio. (Uwaga: Sygnatura czasowa jest domyślnie w formacie ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Zakres wyzwalacza okna wirowania
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON wyzwalacza, jeśli wyzwalacz jest typu: "TumblingWindowTrigger."
(Uwaga: Sygnatura czasowa jest domyślnie w formacie ISO 8601)

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().outputs.windowStartTime |Początek okna, gdy wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Jeśli wyzwalacz okna wirowania ma częstotliwość "co godzinę", będzie to godzina na początku godziny.|
| @trigger().outputs.windowEndTime |Koniec okna, gdy wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Jeśli wyzwalacz okna wirowania ma częstotliwość "co godzinę", będzie to godzina po upływie godziny.|
## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o tym, jak te zmienne są używane w wyrażeniach, zobacz [Language Expression & Functions](control-flow-expression-language-functions.md).
