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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131343"
---
## <a name="timeout"></a>Funkcja aplikacji limitu czasu 

Wartość limitu czasu jest aplikacja funkcji jest definiowany przez właściwość functionTimeout [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) pliku projektu. W poniższej tabeli przedstawiono wartości domyślne i maksymalna w minutach, aby oba plany i obie wersje środowiska uruchomieniowego:

| Planowanie | Wersja środowiska uruchomieniowego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| App Service | 1.x | Nieograniczona liczba | Nieograniczona liczba |
| App Service | 2.x | 30 | Nieograniczona liczba |

> [!NOTE] 
> Bez względu na ustawienie limitu czasu funkcji aplikacji 230 sekund jest maksymalną ilość czasu, prowadzące przez funkcję wyzwalaną przez protokół HTTP do odpowiedzi na żądanie. Jest to z powodu [domyślny limit czasu bezczynności trwający usługi Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Wydłużenie czasu przetwarzania, rozważ użycie [wzorca asynchronicznego funkcje trwałe](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) lub [Odrocz rzeczywistą pracę i zwracają natychmiastowej reakcji](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
