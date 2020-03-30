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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198330"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Czas trwania limitu czasu aplikacji funkcji 

Czas trwania limitu czasu aplikacji funkcji `functionTimeout` jest zdefiniowany przez właściwość w pliku projektu [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) W poniższej tabeli przedstawiono wartości domyślne i maksymalne w minutach dla obu planów i różnych wersji środowiska wykonawczego:

| Planowanie | Wersja środowiska wykonawczego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| Zużycie | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Unlimited (nieograniczony) |
| Premium | 2.x | 30 | Unlimited (nieograniczony) |
| Premium | 3.x | 30 | Unlimited (nieograniczony) |
| App Service | 1.x | Unlimited (nieograniczony) | Unlimited (nieograniczony) |
| App Service | 2.x | 30 | Unlimited (nieograniczony) |
| App Service | 3.x | 30 | Unlimited (nieograniczony) |

> [!NOTE] 
> Niezależnie od ustawienia limitu czasu aplikacji funkcji, 230 sekund to maksymalny czas, jaki może zająć funkcja wyzwalana przez HTTP, aby odpowiedzieć na żądanie. Wynika to z [domyślnego limitu czasu bezczynności modułu Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Aby uzyskać dłuższy czas przetwarzania, należy rozważyć użycie [wzorca asynchronii funkcji trwałych](../articles/azure-functions/durable/durable-functions-overview.md#async-http) lub [odroczyć rzeczywistą pracę i zwrócić natychmiastową odpowiedź](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
