---
title: Rejestruje wyzwalacz usługi Azure Cosmos DB
description: Dowiedz się, jak udostępnić dzienniki usługi Azure Cosmos DB wyzwalacza do potoku rejestrowania usługi Azure Functions
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515081"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Jak skonfigurować i odczytywać dzienniki usługi Azure Cosmos DB wyzwalacza

W tym artykule opisano sposób konfigurowania środowiska usługi Azure Functions, aby wysyłać dzienniki usługi Azure Cosmos DB wyzwalacza usługi skonfigurowanego [rozwiązanie do monitorowania](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Uwzględnione dzienników

Wyzwalacz usługi Azure Cosmos DB używa [bibliotece procesora kanału informacyjnego zmian](./change-feed-processor.md) wewnętrznie i Biblioteka generuje zestaw dzienników dotyczących kondycji, które mogą służyć do monitorowania wewnętrznego operacji [rozwiązywania problemów](./troubleshoot-changefeed-functions.md).

Dzienniki kondycji opisano, jak wyzwalacz usługi Azure Cosmos DB zachowuje się podczas próby wykonania operacji podczas scenariusze równoważenia obciążenia lub inicjowania.

## <a name="enabling-logging"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie w usłudze Azure Cosmos DB wyzwalacza, zlokalizuj `host.json` pliku w projekcie usługi Azure Functions lub aplikacji usługi Azure Functions i [skonfigurować poziom rejestrowania wymagane](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Należy włączyć ślady `Host.Triggers.CosmosDB` jak pokazano w następującym przykładzie:

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

Po wdrożeniu funkcji platformy Azure z zaktualizowaną konfiguracją widoczne będą dzienniki usługi Azure Cosmos DB wyzwalacza jako część Twoje ślady. Można przeglądać dzienniki dostawcy rejestrowania skonfigurowanych w ramach *kategorii* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Zapytanie dzienniki

Uruchom następujące zapytanie do zapytania dzienniki generowane przez wyzwalacz usługi Azure Cosmos DB w [usługi Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Kolejne kroki

* [Aby włączyć monitorowanie](../azure-functions/functions-monitoring.md) w aplikacjach usługi Azure Functions.
* Dowiedz się, jak [diagnozowanie i rozwiązywanie typowych problemów z](./troubleshoot-changefeed-functions.md) podczas korzystania z usługi Azure Cosmos DB wyzwalacza w usłudze Azure Functions.