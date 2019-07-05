---
title: Tworzenie kontenerów w usłudze Azure Cosmos przy użyciu klucza partycji dużych, przy użyciu witryny Azure portal i różnych zestawów SDK.
description: Dowiedz się, jak utworzyć kontener w usłudze Azure Cosmos DB przy użyciu klucza partycji dużych przy użyciu witryny Azure portal i różne zestawy SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566017"
---
# <a name="create-containers-with-large-partition-key"></a>Tworzenie kontenerów przy użyciu klucza partycji duże

Usługa Azure Cosmos DB używa schematu partycjonowania bazujących na skrótach do osiągnięcia, skalowanie w poziomie danych. Wszystkie kontenery z usługi Azure Cosmos utworzonych przed maja 2019 3 użyj funkcji skrótu, który oblicza skrót oparte na pierwszych 100 bajtów klucza partycji. W przypadku wielu kluczy partycji, które mają ten sam pierwsze 100 bajtów te partycje logiczne są traktowane w jako jednej partycji logicznej przez usługę. Może to prowadzić do problemów, takich jak przydział rozmiaru partycji jest nieprawidłowy i indeksy unikatowe Trwa stosowanie różnych kluczy partycji. Kluczy dużych partycji są wprowadzane do rozwiązania tego problemu. Usługa Azure Cosmos DB teraz Obsługa kluczy dużych partycji o wartości 2 KB.

Duża partycja klucze są obsługiwane za pomocą funkcji rozbudowaną wersją funkcji mieszania, który można wygenerować unikatowy skrót z dużą partycję kluczy maksymalnie 2 KB. Ta wersja skrótu zaleca się dla scenariuszy z partycji wysoką kardynalność klucza niezależnie od rozmiaru klucza partycji. Kardynalność klucza partycji jest zdefiniowany jako liczba unikatowych partycjami logicznymi, na przykład zgodnie z kolejnością partycji logicznej ~ 30000 w kontenerze. W tym artykule opisano sposób tworzenia kontenera za pomocą klucza partycji dużych przy użyciu witryny Azure portal i różne zestawy SDK. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Utwórz klucz duża partycja (.Net SDK w wersji 2)

Aby utworzyć kontener za pomocą klucza partycji dużych przy użyciu zestawu SDK platformy .NET, należy określić `PartitionKeyDefinitionVersion.V2` właściwości. Poniższy przykład pokazuje, jak określić właściwość wersja w obiekcie PartitionKeyDefinition i ustaw ją na PartitionKeyDefinitionVersion.V2.

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

## <a name="create-a-large-partition-key-azure-portal"></a>Utwórz klucz duża partycja (witryna Azure portal) 

Aby utworzyć klucz duża partycja, podczas tworzenia nowego kontenera w witrynie Azure portal, zapoznaj się z **mój klucz partycji jest większy niż 100 bajtów** opcji. Domyślnie nowe kontenery zgłoszono do przy użyciu kluczy dużych partycji. Usuń zaznaczenie pola wyboru, jeśli nie potrzebujesz kluczy dużych partycji, lub jeśli masz aplikacje uruchomione na starsze niż 1.18 wersji zestawów SDK.

![Tworzenie kluczy dużych partycji przy użyciu witryny Azure portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Utwórz klucz duża partycja (PowerShell)

Aby utworzyć kontener za pomocą klucza partycji dużych przy użyciu programu PowerShell, należy dołączyć `"version" = 2` dla `partitionKey` obiektu.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Obsługiwane wersje zestawu SDK

Kluczy dużych partycji są obsługiwane przez następujące minimalne wersje zestawów SDK:

|Typ zestawu SDK  | Minimalna wersja   |
|---------|---------|
|.Net     |    1.18     |
|Synchronizacja języka Java     |   2.4.0      |
|Asynchroniczne języka Java   |  2.5.0        |
| Interfejs API REST | wyższa niż wersja `2017-05-03` przy użyciu `x-ms-version` nagłówek żądania.|

Obecnie nie można używać kontenerów przy użyciu klucza duża partycja w obrębie w usłudze Power BI i Azure Logic Apps. Możesz używać kontenerów bez klucza partycji dużych aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
* [Praca z konta usługi Azure Cosmos](account-overview.md)


