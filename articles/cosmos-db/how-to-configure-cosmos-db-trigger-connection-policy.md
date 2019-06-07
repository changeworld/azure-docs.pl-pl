---
title: Zasady połączenia w usłudze Azure Cosmos DB wyzwalacza
description: Informacje o sposobie konfigurowania zasad połączenia używane przez usługi Azure Cosmos DB wyzwalacza
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755332"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Jak skonfigurować zasady połączenia używane przez usługi Azure Cosmos DB wyzwalacza

W tym artykule opisano sposób konfigurowania zasad połączenia za pomocą wyzwalacza usługi Azure Cosmos DB, nawiąż połączenie z kontem usługi Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Dlaczego ważne jest, zasady połączenia?

Istnieją dwa tryby połączenia — w trybie bezpośrednim i tryb bramy. Aby dowiedzieć się więcej na temat trybów połączenia, zobacz [porady dotyczące wydajności](./performance-tips.md#networking) artykułu. Domyślnie **bramy** jest używany do ustanawiania wszystkich połączeń na wyzwalacz usługi Azure Cosmos DB. Jednak może nie być najlepszą opcją w przypadku scenariuszy opartych na wydajność.

## <a name="changing-the-connection-mode-and-protocol"></a>Zmiana trybu połączenia i protokół

Istnieją dwa ustawienia konfiguracji kluczy można skonfigurować zasady połączeń klientów — **tryb połączenia** i **Protokół połączenia**. Możesz zmienić domyślny tryb połączenia i protokół używany przez wyzwalacz usługi Azure Cosmos DB i wszystkich [powiązań usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Aby zmienić ustawienia domyślne, musisz zlokalizować `host.json` pliku w projekcie usługi Azure Functions lub aplikacji usługi Azure Functions i Dodaj następujący kod [bardzo ustawienie](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Gdzie `connectionMode` musi mieć tryb połączenie żądaną (bezpośrednio lub brama) i `protocol` żądane połączenie protokołu (Tcp lub Https). 

Jeśli projekt funkcji platformy Azure działa ze środowiskiem uruchomieniowym usługi Azure Functions w wersji 1, konfiguracja zawiera różnica nieznaczne nazwy, należy użyć `documentDB` zamiast `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Podczas pracy z planem hostingu planu zużycie funkcji platformy Azure, każde wystąpienie ma limit ilości połączeń gniazda, która może obsługiwać. Podczas pracy z bezpośrednich / trafień trybu TCP, projektowania większej liczby połączeń są tworzone i może [Plan zużycie limit](../azure-functions/manage-connections.md#connection-limit), w którym to przypadku można użyć trybu bramy lub uruchamianie usługi Azure Functions w [tryb usługi aplikacji](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Kolejne kroki

* [Limity połączeń w usłudze Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](./performance-tips.md)
* [Przykłady kodu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
