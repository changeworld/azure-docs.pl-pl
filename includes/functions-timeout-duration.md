---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963587"
---
## <a name="timeout"></a>Czas trwania aplikacji funkcji 

Czas trwania aplikacji funkcji jest definiowany przez właściwość functionTimeout w pliku projektu [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . W poniższej tabeli przedstawiono wartości domyślne i maksymalne w minutach dla obu planów i w obu wersjach środowiska uruchomieniowego:

| Plan | Wersja środowiska uruchomieniowego | Domyślny | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| App Service | 1.x | Nieograniczona liczba | Nieograniczona liczba |
| App Service | 2.x | 30 | Nieograniczona liczba |

> [!NOTE] 
> Niezależnie od ustawienia limitu czasu aplikacji funkcji, 230 sekund to maksymalny czas, jaki może wykonać funkcja wyzwalana przez protokół HTTP w celu odpowiedzi na żądanie. Jest to spowodowane [domyślnym limitem czasu bezczynności wynoszącym Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Aby wydłużyć czas przetwarzania, rozważ użycie [wzorca Durable Functions Async](../articles/azure-functions/durable/durable-functions-overview.md#async-http) lub [odroczenie rzeczywistej pracy i zwrócenie natychmiastowej odpowiedzi](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
