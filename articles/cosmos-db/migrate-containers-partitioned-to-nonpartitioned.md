---
title: Migrowanie kontenerów usługi Azure Cosmos nieobjętych partycjonowanymi do kontenerów podzielonych na partycje
description: Dowiedz się, jak przeprowadzić migrację wszystkich istniejących kontenerów nie podzielonych na partycje do kontenerów podzielonych na partycje.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623352"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrowanie kontenerów nieobjętych partycjami do kontenerów podzielonych na partycje

Usługa Azure Cosmos DB obsługuje tworzenie kontenerów bez klucza partycji. Obecnie można tworzyć kontenery nieobjęte partycjami przy użyciu interfejsu wiersza polecenia platformy Azure i platformy Azure Cosmos DB SDK (.Net, Java, NodeJs), które mają wersję mniejszą lub równą 2.x. Nie można utworzyć kontenerów nieobeskrybowanych przy użyciu witryny Azure Portal. Jednak takie kontenery nie podzielonych na partycje nie są elastyczne i mają stałą pojemność magazynu 20 GB i limit przepływności 10K RU/s.

Kontenery nieobjęte partycjami są starsze i należy przeprowadzić migrację istniejących kontenerów nie podzielonych na partycje do kontenerów podzielonych na partycje, aby skalować magazyn i przepływność. Usługa Azure Cosmos DB udostępnia mechanizm zdefiniowany przez system do migracji kontenerów nie podzielonych na partycje do kontenerów podzielonych na partycje. W tym dokumencie wyjaśniono, jak wszystkie istniejące kontenery nieobeszczone na partycje są automatycznie migrowane do kontenerów podzielonych na partycje. Funkcja automatycznej migracji można korzystać tylko wtedy, gdy używasz wersji SDK w wersji V3 we wszystkich językach.

> [!NOTE]
> Obecnie nie można migrować kont interfejsu API usługi Azure Cosmos DB i Gremlin przy użyciu kroków opisanych w tym dokumencie.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrowanie kontenera przy użyciu klucza partycji zdefiniowanego przez system

Aby obsługiwać migrację, usługa Azure Cosmos DB udostępnia klucz partycji zdefiniowany przez system o nazwie `/_partitionkey` we wszystkich kontenerach, które nie mają klucza partycji. Nie można zmienić definicji klucza partycji po migracji kontenerów. Na przykład definicja kontenera, który jest migrowany do kontenera podzielonego na partycje będzie w następujący sposób:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Po migracji kontenera można utworzyć dokumenty, wypełniając `_partitionKey` właściwość wraz z innymi właściwościami dokumentu. Właściwość `_partitionKey` reprezentuje klucz partycji dokumentów.

Wybór odpowiedniego klucza partycji jest ważne, aby optymalnie wykorzystać aprowizowaną przepływność. Aby uzyskać więcej informacji, zobacz jak wybrać artykuł [klucz partycji.](partitioning-overview.md)

> [!NOTE]
> Klucz partycji zdefiniowany przez system można korzystać tylko wtedy, gdy używasz najnowszej/V3 wersji SDK we wszystkich językach.

Poniższy przykład przedstawia przykładowy kod do utworzenia dokumentu z kluczem partycji zdefiniowanym przez system i odczytu tego dokumentu:

**Reprezentacja JSON dokumentu**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Aby uzyskać pełny przykład, zobacz [.Net przykłady][1] GitHub repozytorium.
                      
## <a name="migrate-the-documents"></a>Migrowanie dokumentów

Podczas gdy definicja kontenera jest wzbogacona o właściwość klucza partycji, dokumenty w kontenerze nie są automatycznie migrowane. Oznacza to, że `/_partitionKey` ścieżka właściwości klucza partycji systemowej nie jest automatycznie dodawana do istniejących dokumentów. Należy ponownie podzielić istniejące dokumenty, czytając dokumenty, które zostały utworzone bez klucza partycji i przepisać je z powrotem z `_partitionKey` właściwością w dokumentach.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Uzyskiwanie dostępu do dokumentów, które nie mają klucza partycji

Aplikacje mogą uzyskać dostęp do istniejących dokumentów, które nie mają klucza partycji przy użyciu specjalnej właściwości systemu o nazwie "PartitionKey.None", jest to wartość niezmigrowanych dokumentów. Tej właściwości można użyć we wszystkich operacjach CRUD i kwerendy. W poniższym przykładzie pokazano przykład do odczytu pojedynczego dokumentu z NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Aby uzyskać pełny przykład sposobu ponownego partycjonowania dokumentów, zobacz [.Net przykłady][1] repozytorium GitHub. 

## <a name="compatibility-with-sdks"></a>Kompatybilność z SDK

Starsza wersja zestawów SDK usługi Azure Cosmos DB, takich jak V2.x.x i V1.x.x, nie obsługuje właściwości klucza partycji zdefiniowanej przez system. Tak po przeczytaniu definicji kontenera ze starszego SDK, nie zawiera żadnych definicji klucza partycji i te kontenery będą zachowywać się dokładnie tak jak poprzednio. Aplikacje, które są zbudowane ze starszą wersją zestawy SDK nadal pracować z nie podzielonych na partycje, jak jest bez żadnych zmian. 

Jeśli zmigrowany kontener jest zużywany przez najnowszą/V3 wersję SDK i rozpocznie się wypełnianie klucza partycji zdefiniowanego przez system w nowych dokumentach, nie można już uzyskać dostępu (odczytu, aktualizacji, usunięcia, kwerendy) takich dokumentów ze starszych zestawów SDK.

## <a name="known-issues"></a>Znane problemy

**Wykonywanie zapytań o liczbę elementów, które zostały wstawione bez klucza partycji przy użyciu V3 SDK może wiązać się z wyższym zużyciem przepływności**

W przypadku kwerendy z zestawu SDK v3 dla elementów, które są wstawiane przy użyciu zestawu `PartitionKey.None` V2 SDK lub elementów wstawionych przy użyciu zestawu SDK V3 z parametrem, kwerenda count może zużywać więcej RU/s, jeśli `PartitionKey.None` parametr jest dostarczany w Plikach danych. Zaleca się, aby nie `PartitionKey.None` podawać parametru, jeśli żadne inne elementy nie są wstawiane z kluczem partycji.

Jeśli nowe elementy są wstawiane z różnymi wartościami dla klucza partycji, wykonywanie zapytań o takie liczby elementów przez przekazanie odpowiedniego klucza w `FeedOptions` nie będzie miało żadnych problemów. Po wstawieniu nowych dokumentów z kluczem partycji, jeśli trzeba zbadać tylko liczbę dokumentów bez wartości klucza partycji, kwerenda ta może ponownie ponieść wyższe RU/s podobne do zwykłych kolekcji podzielonych na partycje.

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration