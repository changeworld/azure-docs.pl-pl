---
title: Jak utworzyć wiele niezależnych wyzwalaczy Azure Functions dla Cosmos DB
description: Dowiedz się, jak skonfigurować wiele niezależnych wyzwalaczy Azure Functions dla Cosmos DB do tworzenia architektur opartych na zdarzeniach.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 315ac1025a2b05ec7b16f7f0b14b66f224905d92
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335681"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Utwórz wiele wyzwalaczy Azure Functions dla Cosmos DB

W tym artykule opisano, jak można skonfigurować wiele wyzwalaczy Azure Functions, aby Cosmos DB działały równolegle i niezależnie reagować na zmiany.

![Funkcje oparte na zdarzeniach bezserwerowych działające z wyzwalaczem Azure Functions na potrzeby Cosmos DB i udostępniania kontenera dzierżawy](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Wymagania dotyczące architektury opartej na zdarzeniach

Podczas kompilowania architektur bezserwerowych za pomocą [Azure Functions](../azure-functions/functions-overview.md) [zaleca](../azure-functions/functions-best-practices.md#avoid-long-running-functions) się utworzenie małych zestawów funkcji, które współpracują ze sobą, zamiast dużych długotrwałych funkcji.

Podczas tworzenia przepływów bezserwerowych opartych na zdarzeniach przy użyciu [wyzwalacza Azure Functions dla Cosmos DB](./change-feed-functions.md), należy uruchomić w scenariuszu, w którym chcesz wykonać wiele czynności, gdy istnieje nowe zdarzenie w konkretnym [kontenerze usługi Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers). Jeśli akcje, które mają być wyzwalane, są niezależne od siebie, idealnym rozwiązaniem jest **utworzenie jednego Azure Functions wyzwalaczy dla Cosmos DB na akcję** , którą chcesz wykonać, i wszystkie nasłuchiwanie zmian w tym samym kontenerze usługi Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optymalizowanie kontenerów dla wielu wyzwalaczy

Uwzględniając *wymagania* wyzwalacza Azure Functions dla Cosmos DB, potrzebujemy drugiego kontenera do przechowywania stanu, nazywanego również kontenerem dzierżawy . Czy oznacza to, że potrzebujesz oddzielnego kontenera dzierżaw dla każdej funkcji platformy Azure?

Dostępne są dwie opcje:

* Utwórz **jeden kontener dzierżawy na funkcję**: Takie podejście może przełożyć na dodatkowe koszty, chyba że jest używana [udostępniona baza danych przepływności](./set-throughput.md#set-throughput-on-a-database). Należy pamiętać, że minimalna przepływność na poziomie kontenera to 400 [jednostek żądań](./request-units.md), a w przypadku kontenera dzierżawy jest on używany tylko do tworzenia punktów kontrolnych postępu i utrzymania stanu.
* Mieć **jeden kontener dzierżawy i udostępnić go** wszystkim funkcjom: Druga opcja zapewnia lepsze wykorzystanie jednostek żądań inicjowanych w kontenerze, ponieważ umożliwia wielu Azure Functions współużytkowanie i używanie tej samej zainicjowanej przepływności.

Celem tego artykułu jest przeprowadzenie drugiej opcji.

## <a name="configuring-a-shared-leases-container"></a>Konfigurowanie kontenera udostępnione dzierżawy

Aby skonfigurować kontener udostępnione dzierżawy, jedyną dodatkową konfiguracją, którą `LeaseCollectionPrefix` należy wykonać w wyzwalaczach, jest dodanie [atrybutu](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) , jeśli używasz języka JavaScript C# lub `leaseCollectionPrefix` [atrybutu](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) . Wartość atrybutu powinna być logicznym deskryptorem tego konkretnego wyzwalacza.

Na przykład jeśli masz trzy wyzwalacze: jeden, który wysyła wiadomości e-mail, które wykonuje agregację, aby utworzyć widok z materiałami, a drugi, który wysyła zmiany do innego magazynu, na potrzeby późniejszej analizy można przypisać `LeaseCollectionPrefix` "wiadomości e-mail" do pierwszej, " z materiału "do drugiego i" Analytics "na trzecią.

Ważna część polega na tym, że wszystkie trzy wyzwalacze **mogą korzystać z tej samej konfiguracji kontenera dzierżaw** (konta, bazy danych i nazwy kontenera).

Bardzo prosty przykład kodu, który `LeaseCollectionPrefix` używa atrybutu w C#, będzie wyglądać następująco:

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

W przypadku języka JavaScript można zastosować konfigurację `function.json` do pliku `leaseCollectionPrefix` z atrybutem:

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
> Zawsze Monitoruj jednostki żądania udostępniane w kontenerze dzierżawy udostępnione. Każdy wyzwalacz, który go udostępnia, zwiększy zużycie na średnią przepływność, więc może być konieczne zwiększenie przepływności w miarę zwiększania liczby używanych Azure Functions.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z pełną konfiguracją [wyzwalacza Azure Functions dla Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Zapoznaj się z rozszerzoną [listą próbek](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) dla wszystkich języków.
* Aby uzyskać więcej przykładów, odwiedź te przepisy bezserwerowe, korzystając z Azure Cosmos DB i Azure Functions [repozytorium GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) .