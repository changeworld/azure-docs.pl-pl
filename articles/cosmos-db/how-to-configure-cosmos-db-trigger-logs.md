---
title: Konfigurowanie i odczytywanie dzienników za pomocą wyzwalacza usługi Azure Functions dla usługi Cosmos DB
description: Dowiedz się, jak udostępnić dzienniki do potoku rejestrowania usług Azure Functions podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441824"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Jak skonfigurować i odczytać dzienniki podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB

W tym artykule opisano, jak skonfigurować środowisko usługi Azure Functions do wysyłania wyzwalacza usług Azure Functions dla dzienników usługi Cosmos DB do skonfigurowanego [rozwiązania do monitorowania.](../azure-functions/functions-monitoring.md)

## <a name="included-logs"></a>Dołączone dzienniki

Wyzwalacz funkcji Platformy Azure dla usługi Cosmos DB używa [biblioteki procesora kanału informacyjnego Zmiany](./change-feed-processor.md) wewnętrznie, a biblioteka generuje zestaw dzienników kondycji, które mogą służyć do monitorowania operacji wewnętrznych w [celu rozwiązywania problemów.](./troubleshoot-changefeed-functions.md)

Dzienniki kondycji opisują zachowanie wyzwalacza usługi Azure Functions dla usługi Cosmos DB podczas próby operacji podczas równoważenia obciążenia scenariuszy lub inicjowania.

## <a name="enabling-logging"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie podczas korzystania z wyzwalacza usługi `host.json` Azure Functions dla usługi Cosmos DB, zlokalizuj plik w projekcie usługi Azure Functions lub aplikacji Usługi Azure Functions i [skonfiguruj poziom wymaganego rejestrowania.](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson) Należy włączyć ślady, `Host.Triggers.CosmosDB` jak pokazano w poniższym przykładzie:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Po wdrożeniu funkcji platformy Azure przy użyciu zaktualizowanej konfiguracji zostanie wyświetlony wyzwalacz funkcji platformy Azure dla dzienników usługi Cosmos DB jako część śledzenia. Dzienniki można wyświetlać w skonfigurowanym dostawcy rejestrowania w *obszarze Kategoria* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Kwerenda dzienniki

Uruchom następującą kwerendę, aby zbadać dzienniki wygenerowane przez wyzwalacz usługi Azure Functions dla usługi Cosmos DB w [analytics usługi Azure Application Insights:](../azure-monitor/app/analytics.md)

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Następne kroki

* [Włącz monitorowanie](../azure-functions/functions-monitoring.md) w aplikacjach usługi Azure Functions.
* Dowiedz się, jak [zdiagnozować i rozwiązać typowe problemy](./troubleshoot-changefeed-functions.md) podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB.