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
ms.openlocfilehash: 47c2429363945cf1529cee6e49947aaea95b7022
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597370"
---
## <a name="timeout"></a>Czas trwania aplikacji funkcji 

Czas trwania aplikacji funkcji jest definiowany przez właściwość functionTimeout w pliku projektu [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . W poniższej tabeli przedstawiono wartości domyślne i maksymalne w minutach dla obu planów i w obu wersjach środowiska uruchomieniowego:

| Plan | Wersja środowiska uruchomieniowego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| Zużycie | 3. x (wersja zapoznawcza) | 5 | 10 |
| App Service | 1.x | Bez ograniczeń | Bez ograniczeń |
| App Service | 2.x | 30 | Bez ograniczeń |
| App Service | 3. x (wersja zapoznawcza) | 30 | Bez ograniczeń |

> [!NOTE] 
> Niezależnie od ustawienia limitu czasu aplikacji funkcji, 230 sekund to maksymalny czas, jaki może wykonać funkcja wyzwalana przez protokół HTTP w celu odpowiedzi na żądanie. Jest to spowodowane [domyślnym limitem czasu bezczynności wynoszącym Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Aby wydłużyć czas przetwarzania, rozważ użycie [wzorca Durable Functions Async](../articles/azure-functions/durable/durable-functions-overview.md#async-http) lub [odroczenie rzeczywistej pracy i zwrócenie natychmiastowej odpowiedzi](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
