---
title: Zmienne systemowe w fabryce danych Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmienne systemowe obsługiwane przez usługi fabryka danych Azure. Te zmienne można użyć w wyrażeniach podczas definiowania jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 43ea8703bdbfc23511c831a5f91c9461948cc254
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059074"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Fabryka danych Azure są obsługiwane zmienne systemu
W tym artykule opisano zmienne systemowe obsługiwane przez usługi fabryka danych Azure. Te zmienne można użyć w wyrażeniach podczas definiowania jednostek fabryki danych.

## <a name="pipeline-scope"></a>Zakres potoku
Te zmienne systemowe mogą odwoływać się do dowolnego miejsca kod JSON potoku.

| Nazwa zmiennej | Opis |
| --- | --- |
| @pipeline().DataFactory |Nazwa fabryki danych potoku Uruchom działa w ramach |
| @pipeline().Pipeline |Nazwa potoku |
| @pipeline().RunId | Identyfikator określonego procesu, uruchom |
| @pipeline().TriggerType | Typ wyzwalacza, który wywołał potoku (ręczne, harmonogram) |
| @pipeline().TriggerId| Identyfikator wyzwalacza, który wywołuje potoku |
| @pipeline().TriggerName| Nazwa wyzwalacza, który wywołuje potoku |
| @pipeline().TriggerTime| Godzinę wyzwalacz, który wywołał potoku. Czas wyzwalacza jest bieżącą godzinę wypalane nie zaplanowanym terminie. Na przykład `13:20:08.0149599Z` jest zwracana zamiast `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Zaplanuj zakres wyzwalacza
Te zmienne systemu może być dowolnym przywoływany w wyzwalacza JSON Jeśli wyzwalacza jest typu: "ScheduleTrigger."

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger.scheduledTime) |Czasem wyzwalacz został zaplanowanego wywoływanie potoku uruchamiania. Na przykład dla wyzwalacza generowane co 5 minut, ta zmienna zwróci `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` odpowiednio.|
| @trigger.startTime) |Godzinę wyzwalacza **faktycznie** uruchamiany wywoływanie potoku uruchamiania. Na przykład dla wyzwalacza generowane co 5 minut, ta zmienna może zwrócić przypominać `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` odpowiednio.|

## <a name="tumbling-window-trigger-scope"></a>Zakres wyzwalacza okno wirowania
Te zmienne systemu może być dowolnym przywoływany w wyzwalacza JSON Jeśli wyzwalacza jest typu: "TumblingWindowTrigger."

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger(). outputs.windowStartTime |Początek okna, jeśli wyzwalacz zostało zaplanowane do uruchomienia procesu wywołania. Jeśli częstotliwość "co godzinę" ma wyzwalacz okno wirowania będzie to godzina na początku godziny.|
| @trigger(). outputs.windowEndTime |Koniec okna, jeśli wyzwalacz zostało zaplanowane do wywołania potoku uruchamiania. Jeśli częstotliwość "co godzinę" ma wyzwalacz okno wirowania będzie to godzina na koniec godziny.|
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o używaniu tych zmiennych w wyrażeniach, zobacz [język wyrażeń & funkcje](control-flow-expression-language-functions.md).
