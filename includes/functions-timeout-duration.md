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
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131343"
---
## <a name="timeout"></a>Funkcja aplikacji limitu czasu 

Wartość limitu czasu jest aplikacja funkcji jest definiowany przez właściwość functionTimeout [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) pliku projektu. W poniższej tabeli przedstawiono wartości domyślne i maksymalna w minutach, aby oba plany i obie wersje środowiska uruchomieniowego:

| Plan | Wersja środowiska uruchomieniowego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Użycie | 1.x | 5 | 10 |
| Użycie | 2.x | 5 | 10 |
| App Service | 1.x | Nieograniczone | Nieograniczone |
| App Service | 2.x | 30 | Nieograniczone |

> [!NOTE] 
> Bez względu na ustawienie limitu czasu funkcji aplikacji 230 sekund jest maksymalną ilość czasu, prowadzące przez funkcję wyzwalaną przez protokół HTTP do odpowiedzi na żądanie. Jest to z powodu [domyślny limit czasu bezczynności trwający usługi Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Wydłużenie czasu przetwarzania, rozważ użycie [wzorca asynchronicznego funkcje trwałe](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) lub [Odrocz rzeczywistą pracę i zwracają natychmiastowej reakcji](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
