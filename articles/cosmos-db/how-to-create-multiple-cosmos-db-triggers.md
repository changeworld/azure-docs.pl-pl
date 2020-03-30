---
title: Tworzenie wielu niezależnych wyzwalaczy usług Azure Functions dla usługi Cosmos DB
description: Dowiedz się, jak skonfigurować wiele niezależnych wyzwalaczy usługi Azure Functions dla usługi Cosmos DB w celu tworzenia architektur opartych na zdarzeniach.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604942"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Tworzenie wielu wyzwalaczy funkcji platformy Azure dla usługi Cosmos DB

W tym artykule opisano sposób konfigurowania wielu wyzwalaczy usługi Azure Functions dla usługi Cosmos DB tak, aby działały równolegle i niezależnie reagowały na zmiany.

![Funkcje oparte na zdarzeniach bezserwerowe współpracujące z wyzwalaczem usług Azure Functions dla usługi Cosmos DB i współużytkowane kontenerem dzierżaw](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Wymagania dotyczące architektury opartej na zdarzeniach

Podczas tworzenia architektury bezserwerowej za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md)zaleca [się](../azure-functions/functions-best-practices.md#avoid-long-running-functions) tworzenie małych zestawów funkcji, które współpracują ze sobą zamiast dużych funkcji długotrwałych.

Podczas tworzenia przepływów opartych na zdarzeniach bezserwerowych przy użyciu [wyzwalacza usługi Azure Functions dla usługi Cosmos DB,](./change-feed-functions.md)zostanie uruchomiony scenariusz, w którym chcesz wykonać wiele czynności, gdy pojawi się nowe zdarzenie w określonym [kontenerze usługi Azure Cosmos.](./databases-containers-items.md#azure-cosmos-containers) Jeśli akcje, które chcesz wyzwolić, są niezależne od siebie, idealnym rozwiązaniem byłoby **utworzenie jednego wyzwalaczy usługi Azure Functions dla usługi Cosmos DB na akcję,** którą chcesz wykonać, wszystkie nasłuchiwanie zmian w tym samym kontenerze usługi Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optymalizacja kontenerów pod kątem wielu wyzwalaczy

Biorąc pod uwagę *wymagania* wyzwalacza usługi Azure Functions dla usługi Cosmos DB, potrzebujemy drugiego kontenera do przechowywania stanu, nazywanego również *kontenerem dzierżaw.* Czy to oznacza, że potrzebujesz oddzielnego kontenera dzierżawy dla każdej funkcji platformy Azure?

Tutaj masz dwie opcje:

* Utwórz **jeden kontener dzierżawy na funkcję:** Takie podejście może przełożyć się na dodatkowe koszty, chyba że używasz [udostępnionej bazy danych przepływności.](./set-throughput.md#set-throughput-on-a-database) Należy pamiętać, że minimalna przepływność na poziomie kontenera jest 400 [jednostek żądań](./request-units.md), a w przypadku kontenera dzierżawy jest używany tylko do punktu kontrolnego postępu i stanu utrzymania.
* Mieć **jeden kontener dzierżawy i udostępnić go** dla wszystkich funkcji: Ta druga opcja lepiej korzysta z aprowizowanych jednostek żądań w kontenerze, ponieważ umożliwia wiele funkcji platformy Azure do udostępniania i używania tej samej aprowizowanej przepływności.

Celem tego artykułu jest poprowadzenie cię do osiągnięcia drugiej opcji.

## <a name="configuring-a-shared-leases-container"></a>Konfigurowanie kontenera dzierżawy udostępnionej

Aby skonfigurować kontener dzierżawy udostępnionej, jedyną dodatkową konfiguracją, którą `LeaseCollectionPrefix` należy wprowadzić w wyzwalaczach, jest dodanie [atrybutu,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) jeśli używasz języka C# lub `leaseCollectionPrefix` [atrybutu,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) jeśli używasz języka JavaScript. Wartość atrybutu powinna być logicznym deskryptorem tego określonego wyzwalacza.

Na przykład jeśli masz trzy wyzwalacze: jeden, który wysyła wiadomości e-mail, jeden, który wykonuje agregację w celu utworzenia zmaterializowanego widoku, i taki, który wysyła zmiany do innego magazynu, do `LeaseCollectionPrefix` późniejszej analizy, można przypisać "wiadomości e-mail" do pierwszego, "zmaterializowane" do drugiego i "analizy" do trzeciego.

Ważną częścią jest to, że wszystkie trzy wyzwalacze **mogą używać tej samej konfiguracji kontenera dzierżawy** (konto, baza danych i nazwa kontenera).

Bardzo proste przykłady kodu `LeaseCollectionPrefix` przy użyciu atrybutu w języku C#, będzie wyglądać następująco:

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

W przypadku języka JavaScript można zastosować `function.json` konfigurację `leaseCollectionPrefix` pliku z atrybutem:

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
> Zawsze monitoruj w jednostkach żądań aprowidywizacji w kontenerze dzierżawy udostępnionej. Każdy wyzwalacz, który go współużytkuje, zwiększy przepływność średnie zużycie, więc może być konieczne zwiększenie aprowizowanej przepływności w miarę zwiększania liczby usług Azure Functions, które go używają.

## <a name="next-steps"></a>Następne kroki

* Zobacz pełną konfigurację [wyzwalacza usługi Azure Functions dla usługi Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Sprawdź rozszerzoną [listę przykładów](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) dla wszystkich języków.
* Odwiedź receptury bezserwerowe z [repozytorium Usługi Azure](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) Cosmos DB i Usługi Azure Functions GitHub, aby uzyskać więcej przykładów.