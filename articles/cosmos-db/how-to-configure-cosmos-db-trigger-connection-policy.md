---
title: Wyzwalacz usługi Azure Functions dla zasad połączeń usługi Cosmos DB
description: Dowiedz się, jak skonfigurować zasady połączenia używane przez wyzwalacz usługi Azure Functions dla usługi Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604968"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Jak skonfigurować zasady połączenia używane przez wyzwalacz usługi Azure Functions dla usługi Cosmos DB

W tym artykule opisano, jak skonfigurować zasady połączenia podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB, aby połączyć się z kontem usługi Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Dlaczego zasady połączeń są ważne?

Dostępne są dwa tryby połączenia - tryb bezpośredni i tryb bramy. Aby dowiedzieć się więcej o tych trybach połączenia, zobacz artykuł [dotyczący wskazówek dotyczących wydajności.](./performance-tips.md#networking) Domyślnie **brama** jest używana do ustanawiania wszystkich połączeń w wyzwalaczu usługi Azure Functions dla usługi Cosmos DB. Jednak może nie być najlepszym rozwiązaniem dla scenariuszy opartych na wydajności.

## <a name="changing-the-connection-mode-and-protocol"></a>Zmiana trybu połączenia i protokołu

Dostępne są dwa kluczowe ustawienia konfiguracji do skonfigurowania zasad połączenia klienta – **tryb połączenia** i **protokół połączenia.** Można zmienić domyślny tryb połączenia i protokół używany przez wyzwalacz usługi Azure Functions dla usługi Cosmos DB i wszystkich [powiązań usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Aby zmienić ustawienia domyślne, `host.json` musisz zlokalizować plik w projekcie usługi Azure Functions lub aplikacji Usługi Azure Functions i dodać następujące [dodatkowe ustawienie:](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Gdzie `connectionMode` musi być żądany tryb połączenia `protocol` (bezpośredni lub brama) i żądany protokół połączenia (Tcp lub Https). 

Jeśli projekt usługi Azure Functions działa ze środowiska uruchomieniowego usługi Azure Functions `documentDB` V1, `cosmosDB`konfiguracja ma niewielką różnicę nazw, należy użyć zamiast:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Podczas pracy z planem hostingu planu hostingu usługi Azure Functions, każde wystąpienie ma limit liczby połączeń z gniazdami, które może obsługiwać. Podczas pracy z trybem Direct / TCP, zgodnie z projektem, powstaje więcej połączeń i można osiągnąć [limit planu zużycia](../azure-functions/manage-connections.md#connection-limit), w którym to przypadku można użyć trybu bramy lub uruchomić usługę Azure Functions w [trybie usługi app service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Następne kroki

* [Limity połączeń w usłudze Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](./performance-tips.md)
* [Przykłady kodu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
