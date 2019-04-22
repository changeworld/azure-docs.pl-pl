---
title: Jak utworzyć wiele niezależnych wyzwalaczy bazy danych Cosmos Azure
description: Dowiedz się, jak konfigurować wiele niezależnych Azure Cosmos DB wyzwalaczy, aby tworzyć oparte na zdarzeniach architektury usługi Azure Functions.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 4/15/2019
ms.author: maquaran
ms.openlocfilehash: 7a47a928e97d52535a6d3baa808f1fcb81d9bb55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700147"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Utwórz wiele usługi Azure Cosmos DB wyzwalaczy

W tym artykule opisano sposób konfigurowania wielu Cosmos DB wyzwalaczy działać równolegle i niezależnie reagować na zmiany.

![Funkcje niewymagające użycia serwera opartego na zdarzeniach — udostępnianie kontenerze dzierżawy i Praca z wyzwalacza usługi Azure Cosmos DB](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Wymagania dotyczące architektury oparte na zdarzeniach

Podczas tworzenia architektur bez użycia serwera za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md), ma ona [zalecane](../azure-functions/functions-best-practices.md#avoid-long-running-functions) do tworzenia zestawów małych funkcji, które współpracują ze sobą, zamiast duże długo działające funkcje.

Podczas tworzenia opartego na zdarzeniach przy użyciu bezserwerowe przepływy [usługi Azure Cosmos DB wyzwalacza](./change-feed-functions.md), uruchomisz do scenariusza gdzie chcesz zrobić wiele rzeczy, zawsze, gdy jest to nowe zdarzenie w szczególności [kontenera usługi Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers). Jeśli akcji, które ma wyzwolić, są niezależne od siebie nawzajem, doskonałe rozwiązanie powinno być **utworzyć jeden wyzwalacz Cosmos DB, każdej akcji** co chcesz zrobić wszystkie nasłuchiwanie zmian na tym samym kontenerze usługi Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optymalizacja kontenery dla wielu wyzwalaczy

Biorąc pod uwagę *wymagania* wyzwalacza Cosmos DB, potrzebujemy drugi kontener do przechowywania stanu, nazywany również *dzierżawy kontenera*. To znaczy należy kontener oddzielnych dzierżaw dla każdej funkcji platformy Azure?

W tym miejscu masz dwie opcje:

* Tworzenie **jednej dzierżawy kontenera dla każdej funkcji**: Takie podejście może przekłada się na dodatkowych kosztów, chyba że [udostępnionej przepływności bazy danych](./set-throughput.md#set-throughput-on-a-database). Należy pamiętać, że minimalna przepływność na poziomie kontenera 400 [jednostek żądań](./request-units.md), a w przypadku kontenerze dzierżawy tylko jego umożliwia punktu kontrolnego postęp i zarządzania stanem.
* Ma **jednej dzierżawy kontenera i udostępnij go** dla wszystkich funkcji: Ta druga opcja sprawia, że lepsze wykorzystanie aprowizowane jednostki żądań w kontenerze, ponieważ umożliwia ona wiele funkcji platformy Azure do udostępniania i używać tych samych aprowizowanej przepływności.

Celem tego artykułu jest przeprowadzenie Cię do wykonania drugiej opcji.

## <a name="configuring-a-shared-leases-container"></a>Konfigurowanie kontenera udostępnionego dzierżawy

Do konfigurowania kontenera udostępnionego dzierżaw, tylko dodatkowych konfiguracji, musisz wprowadzić swoje wyzwalaczy jest dodanie `LeaseCollectionPrefix` [atrybut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) Jeśli używasz C# lub `leaseCollectionPrefix` [atrybut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)Jeśli używasz języka JavaScript. Wartość atrybutu powinna być deskryptora logicznego, jakie tego konkretnego wyzwalacza.

Na przykład, jeśli masz trzy wyzwalacze: jeden, który wysyła wiadomości e-mail: jeden, który wykonuje agregację na utworzenie zmaterializowanego widoku i jedną, która wysyła zmiany do innego magazynu do późniejszej analizy, można przypisać `LeaseCollectionPrefix` "wiadomości e-mail", aby pierwsza z nich, " zmaterializować"drugi i"analytics", aby trzecie.

Ważnym elementem jest wyzwalane przez wszystkich trzech **mogą używać tej samej konfiguracji kontenera dzierżawy** (nazwa konta, bazy danych i kontenera).

Przykłady kodu w bardzo prosty, za pomocą `LeaseCollectionPrefix` atrybutu w C#, będzie wyglądać następująco:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

I JavaScript, można zastosować konfiguracji na `function.json` pliku, za pomocą `leaseCollectionPrefix` atrybutu:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Stałe monitorowanie w jednostkach żądania, którego obsługę zainicjowano na kontenerze dzierżawy udostępnione. Każdy wyzwalacz, który współużytkuje, spowoduje zwiększenie użycia Średnia przepustowość, dzięki czemu może być konieczne zwiększysz aprowizowaną przepływność, jak zwiększyć liczbę usługi Azure Functions, który jest używany.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz pełną konfigurację dla [usługi Azure Cosmos DB wyzwalacza](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Sprawdź rozszerzonych [lista przykładów](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) dla wszystkich języków.
* Odwiedź przepisy bez użycia serwera, za pomocą usługi Azure Cosmos DB i Azure Functions [repozytorium GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) dla większej liczby próbek.