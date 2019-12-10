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
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941727"
---
## <a name="timeout"></a>Czas trwania aplikacji funkcji 

Czas trwania aplikacji funkcji jest definiowany przez właściwość functionTimeout w pliku projektu [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . W poniższej tabeli przedstawiono wartości domyślne i maksymalne w minutach dla obu planów i w obu wersjach środowiska uruchomieniowego:

| Plan | Wersja środowiska uruchomieniowego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| Zużycie | wersji | 5 | 10 |
| App Service | 1.x | Bez ograniczeń | Bez ograniczeń |
| App Service | 2.x | 30 | Bez ograniczeń |
| App Service | wersji | 30 | Bez ograniczeń |

> [!NOTE] 
> Niezależnie od ustawienia limitu czasu aplikacji funkcji, 230 sekund to maksymalny czas, jaki może wykonać funkcja wyzwalana przez protokół HTTP w celu odpowiedzi na żądanie. Jest to spowodowane [domyślnym limitem czasu bezczynności wynoszącym Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Aby wydłużyć czas przetwarzania, rozważ użycie [wzorca Durable Functions Async](../articles/azure-functions/durable/durable-functions-overview.md#async-http) lub [odroczenie rzeczywistej pracy i zwrócenie natychmiastowej odpowiedzi](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
