---
title: Tworzenie kontenerów usługi Azure Cosmos z dużym kluczem partycji
description: Dowiedz się, jak utworzyć kontener w usłudze Azure Cosmos DB przy użyciu dużego klucza partycji przy użyciu witryny Azure portal i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887686"
---
# <a name="create-containers-with-large-partition-key"></a>Tworzenie kontenerów z dużym kluczem partycji

Usługa Azure Cosmos DB używa schematu partycjonowania opartego na mieszaniu w celu osiągnięcia skalowania w poziomie danych. Wszystkie kontenery usługi Azure Cosmos utworzone przed 3 maja 2019 r. używają funkcji mieszania, która oblicza skrót na podstawie pierwszych 100 bajtów klucza partycji. Jeśli istnieje wiele kluczy partycji, które mają te same pierwsze 100 bajtów, a następnie te partycje logiczne są uważane za tę samą partycję logiczną przez usługę. Może to prowadzić do problemów, takich jak przydział rozmiaru partycji są niepoprawne i unikatowe indeksy stosowane w kluczach partycji. Duże klucze partycji są wprowadzane do rozwiązania tego problemu. Usługa Azure Cosmos DB obsługuje teraz duże klucze partycji o wartościach do 2 KB.

Duże klucze partycji są obsługiwane przy użyciu funkcji ulepszonej wersji funkcji mieszania, która może generować unikatowy skrót z dużych kluczy partycji do 2 KB. Ta wersja skrótu jest również zalecane w scenariuszach z wysokiej kardynalności klucz partycji, niezależnie od rozmiaru klucza partycji. Kardynalność klucza partycji jest zdefiniowana jako liczba unikatowych partycji logicznych, na przykład w kolejności ~30000 partycji logicznych w kontenerze. W tym artykule opisano sposób tworzenia kontenera z dużym kluczem partycji przy użyciu witryny Azure portal i różnych zestawów SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Tworzenie dużego klucza partycji (Azure portal)

Aby utworzyć klucz partycji dużych, podczas tworzenia nowego kontenera przy użyciu witryny Azure portal, sprawdź **Mój klucz partycji jest większy niż 100 bajtów** opcji. Usuń zaznaczenie pola wyboru, jeśli nie potrzebujesz dużych kluczy partycji lub jeśli masz aplikacje uruchomione w wersji zestawów SDK starszych niż 1.18.

![Tworzenie dużych kluczy partycji za pomocą portalu Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Tworzenie dużego klucza partycji (programu PowerShell)

Aby utworzyć kontener z obsługą dużych kluczy partycji zobacz,

* [Tworzenie kontenera usługi Azure Cosmos o dużym rozmiarze klucza partycji](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Tworzenie dużego klucza partycji (.Net SDK)

Aby utworzyć kontener z dużym kluczem partycji przy użyciu `PartitionKeyDefinitionVersion.V2` pliku .NET SDK, należy określić właściwość. W poniższym przykładzie pokazano, jak określić Version właściwości w PartitionKeyDefinition obiektu i ustawić go na PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>V3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>SDK w wersji 2 .NET

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Obsługiwane wersje SDK

Duże klucze partycji są obsługiwane z następującymi minimalnymi wersjami sdków:

|Typ SDK  | Minimalna wersja   |
|---------|---------|
|.Net     |    1.18     |
|Synchronizacja java     |   2.4.0      |
|Java Async   |  2.5.0        |
| Interfejs API REST | wersja wyższa `2017-05-03` niż `x-ms-version` przy użyciu nagłówka żądania.|
| Szablon usługi Resource Manager | wersji 2 przy `"version":2` użyciu `partitionKey` właściwości w obiekcie. |

Obecnie nie można używać kontenerów z dużym kluczem partycji w usłudze Power BI i usłudze Azure Logic Apps. Można użyć kontenerów bez dużego klucza partycji z tych aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](account-overview.md)
