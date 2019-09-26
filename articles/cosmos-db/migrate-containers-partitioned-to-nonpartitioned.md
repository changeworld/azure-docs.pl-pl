---
title: Migrowanie kontenerów usługi Azure Cosmos bez partycjonowania do kontenerów partycjonowanych
description: Dowiedz się, jak migrować wszystkie istniejące kontenery niepartycjonowane do kontenerów partycjonowanych.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 77d70aaa9c1ae5a111a47e08f259c0ce95fd7c92
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300111"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrowanie kontenerów bez partycjonowania do kontenerów partycjonowanych

Azure Cosmos DB obsługuje tworzenie kontenerów bez klucza partycji. Obecnie można tworzyć kontenery bez partycjonowania przy użyciu interfejsu wiersza polecenia platformy Azure i zestawów SDK Azure Cosmos DB (.NET, Java, NodeJs), które mają wersję mniejszą lub równą 2. x. Nie można utworzyć kontenerów bez partycjonowania przy użyciu Azure Portal. Jednak takie kontenery niepartycjonowane nie są elastyczne i mają stałą pojemność magazynu wynoszącą 10 GB i limit przepływności (10 000 jednostek RU/s).

Kontenery bez partycjonowania są starsze i należy migrować istniejące kontenery niepodzielone na partycje w celu skalowania magazynu i przepływności. Azure Cosmos DB zapewnia mechanizm zdefiniowany przez system w celu migrowania kontenerów niepodzielonych na partycje do kontenerów podzielonych na partycje. W tym dokumencie wyjaśniono, w jaki sposób wszystkie istniejące kontenery niepodzielone na partycje są migrowane do kontenerów z podziałem na partycje. Możesz skorzystać z funkcji automigracji tylko wtedy, gdy używasz wersji v3 zestawów SDK we wszystkich językach.

> [!NOTE]
> Obecnie nie można migrować kont interfejsu API Azure Cosmos DB MongoDB i Gremlin za pomocą kroków opisanych w tym dokumencie.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrowanie kontenera przy użyciu klucza partycji zdefiniowanej przez system

W celu obsługi migracji Azure Cosmos DB udostępnia klucz `/_partitionkey` partycji zdefiniowany przez system na wszystkich kontenerach, które nie mają klucza partycji. Nie można zmienić definicji klucza partycji po migracji kontenerów. Na przykład definicja kontenera migrowanego do kontenera partycjonowanego będzie następująca:

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

Po migracji kontenera można utworzyć dokumenty, wypełniając `_partitionKey` Właściwość wraz z innymi właściwościami dokumentu. `_partitionKey` Właściwość reprezentuje klucz partycji dokumentów.

Wybór odpowiedniego klucza partycji jest ważny w celu optymalnego wykorzystania alokowanej przepływności. Aby uzyskać więcej informacji, zobacz artykuł [jak wybrać klucz partycji](partitioning-overview.md) .

> [!NOTE]
> Można korzystać z klucza partycji zdefiniowanego przez system tylko wtedy, gdy jest używana najnowsza wersja programu/v3 zestawów SDK we wszystkich językach.

Poniższy przykład przedstawia przykładowy kod służący do tworzenia dokumentu z kluczem partycji zdefiniowanym przez system i odczytywania tego dokumentu:

**Reprezentacja dokumentu JSON**

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

Aby zapoznać się z kompletnym przykładem, zobacz repozytorium [.NET Samples](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) w witrynie GitHub.
                      
## <a name="migrate-the-documents"></a>Migrowanie dokumentów

Mimo że definicja kontenera jest rozszerzona za pomocą właściwości klucza partycji, dokumenty w kontenerze nie są migrowane. Oznacza to, że ścieżka właściwości `/_partitionKey` klucza partycji systemowej nie jest automatycznie dodawana do istniejących dokumentów. Należy ponownie podzielić na partycje istniejące dokumenty, odczytując dokumenty, które zostały utworzone bez klucza partycji i ponownie zapisując je z `_partitionKey` właściwością w dokumentach.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Dostęp do dokumentów, które nie mają klucza partycji

Aplikacje mogą uzyskiwać dostęp do istniejących dokumentów, które nie mają klucza partycji przy użyciu specjalnej właściwości systemu o nazwie "CosmosContainerSettings. NonePartitionKeyValue", która jest wartością niezmigrowanych dokumentów. Tej właściwości można używać we wszystkich operacjach CRUD i zapytania. Poniższy przykład przedstawia przykład odczytu pojedynczego dokumentu z NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Aby zapoznać się z kompletnym przykładem na temat ponownego partycjonowania dokumentów, zobacz repozytorium [.NET Samples](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) w witrynie GitHub. 

## <a name="compatibility-with-sdks"></a>Zgodność z zestawami SDK

Starsza wersja zestawów SDK Azure Cosmos DB, takich jak v2. x. x i v1. x. x, nie obsługuje właściwości klucza partycji zdefiniowanej przez system. Dlatego w przypadku odczytywania definicji kontenera ze starszego zestawu SDK nie zawiera ona żadnej definicji klucza partycji i te kontenery będą działać dokładnie tak jak wcześniej. Aplikacje skompilowane przy użyciu starszej wersji zestawów SDK nadal pracują z niepodzielonym na partycje bez żadnych zmian. 

Jeśli migrowany kontener jest używany przez najnowszą wersję zestawu SDK/v3 i rozpoczęto zapełnianie zdefiniowanego przez system klucza partycji w ramach nowych dokumentów, nie można już uzyskać dostępu do tych dokumentów ze starszych zestawów SDK.

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Obsługa przepływności na kontenerach i bazach danych](set-throughput.md)
* [Współpraca z kontem usługi Azure Cosmos](account-overview.md)