---
title: Zmiennych systemowych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmienne systemowe, obsługiwany przez usługę Azure Data Factory. Podczas definiowania jednostek usługi Data Factory, można użyć tych zmiennych w wyrażeniach.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 9a4d5acfe16a2fdbb3b631cb8baf6cb8e90a7d58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935708"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Zmienne systemowe, obsługiwany przez usługę Azure Data Factory
W tym artykule opisano zmienne systemowe, obsługiwany przez usługę Azure Data Factory. Podczas definiowania jednostek usługi Data Factory, można użyć tych zmiennych w wyrażeniach.

## <a name="pipeline-scope"></a>Zakres potoku
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON potoku.

| Nazwa zmiennej | Opis |
| --- | --- |
| @pipeline().DataFactory |Nazwa fabryki danych uruchomienia potoku jest uruchomiona w ramach |
| @pipeline().Pipeline |Nazwa potoku |
| @pipeline().RunId | Identyfikator określonego uruchomienia potoku |
| @pipeline().TriggerType | Typ wyzwalacza, która wywołała potoku (ręcznego, harmonogram) |
| @pipeline().TriggerId| Identyfikator wyzwalacza, który wywołuje potok |
| @pipeline().TriggerName| Nazwa wyzwalacza, który wywołuje potok |
| @pipeline().TriggerTime| Czas, kiedy wyzwalacza, która wywołała potoku. Czas wyzwalacza jest rzeczywisty czas wyzwolenia nie zaplanowanym terminie. Na przykład `13:20:08.0149599Z` jest zwracana zamiast `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Zakres wyzwalacza harmonogramu
Te zmienne systemowe mogą być przywoływane gdziekolwiek w wyzwalaczu JSON wyzwalacza jest typu: "ScheduleTrigger."

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().scheduledTime |Czas, gdy wyzwalacz została zaplanowana do wywołania uruchomienia potoku. Na przykład wyzwalacz, który jest uruchamiany co 5 minut, ta zmienna zwróci `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` odpowiednio.|
| @trigger().startTime |Czas, gdy wyzwalacz **faktycznie** uruchamiane do wywołania uruchomienia potoku. Na przykład wyzwalacz, który jest uruchamiany co 5 minut, ta zmienna może zwrócić podobny do poniższego `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` odpowiednio.|

## <a name="tumbling-window-trigger-scope"></a>Zakres zarówno wyzwalacz okna wirowania
Te zmienne systemowe mogą być przywoływane gdziekolwiek w wyzwalaczu JSON wyzwalacza jest typu: "TumblingWindowTrigger."

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().outputs.windowStartTime |Początek okna, jeśli wyzwalacz została zaplanowana do uruchomienia potoku do wywołania. Jeśli zarówno wyzwalacz okna wirowania ma częstotliwość "co godzinę" będzie to czas na początku godziny.|
| @trigger().outputs.windowEndTime |Koniec okna, jeśli wyzwalacz została zaplanowana do uruchomienia potoku do wywołania. Jeśli zarówno wyzwalacz okna wirowania ma częstotliwość "co godzinę" będzie to czas na koniec godziny.|
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje dotyczące używania tych zmiennych w wyrażeniach, zobacz [język wyrażeń & funkcje](control-flow-expression-language-functions.md).
