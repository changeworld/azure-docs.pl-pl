---
title: Migrowanie niepartycjonowana kontenery usługi Azure Cosmos DB do kontenerów podzielonym na partycje
description: Dowiedz się, jak przeprowadzić migrację istniejących niepartycjonowana kontenerach w kontenerach podzielonym na partycje.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241926"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrowanie niepartycjonowana kontenerów do kontenerów podzielonym na partycje

Usługa Azure Cosmos DB obsługuje tworzenie kontenerów bez klucza partycji. Obecnie można tworzyć niepartycjonowana kontenery przy użyciu wiersza polecenia platformy Azure i Azure Cosmos DB zestawów SDK (.Net, Java, NodeJs) mające wersja niższa niż lub równe 2.x. Nie można utworzyć niepartycjonowana kontenerów przy użyciu witryny Azure portal. Jednak takie niepartycjonowana kontenery nie są elastyczne i wyeliminowaniu pojemności 10 GB i przepływnością limit wynoszący 10 K jednostek RU/s.

Niepartycjonowana kontenerów: czym są starszej wersji i należy zmigrować istniejące kontenery niepartycjonowana partycjonowane kontenery skalowanie magazynu i przepływności. Usługa Azure Cosmos DB udostępnia mechanizm zdefiniowane przez system migrację kontenerów niepartycjonowana do kontenerów podzielonym na partycje. W tym dokumencie wyjaśniono, jak wszystkie kontenery istniejących niepartycjonowana są migrowane automatycznie do kontenerów podzielonym na partycje. Korzystać z zalet funkcji automatycznej migracji, tylko wtedy, gdy jest używana wersja V3 zestawów SDK we wszystkich językach.

> [!NOTE] 
> Obecnie nie można migrować konta usługi Azure Cosmos DB bazy danych MongoDB i interfejs API Gremlin wykonując kroki opisane w tym dokumencie. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrowanie kontenera przy użyciu klucza partycji zdefiniowane przez system

Aby umożliwić obsługę migracji, usługi Azure Cosmos DB definiuje klucz partycji zdefiniowane przez system, o nazwie `/_partitionkey` na wszystkie kontenery, które nie mają klucza partycji. Nie można zmienić definicji klucza partycji, po przeprowadzeniu migracji są kontenery. Na przykład definicji kontenera, który został poddany migracji kontener podzielony na partycje będą w następujący sposób: 

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
 
Po przeprowadzeniu migracji kontenera można tworzyć dokumenty, definiując `_partitionKey` właściwości oraz inne właściwości dokumentu. `_partitionKey` Właściwość reprezentuje klucz partycji dokumentów. 

Wybór klucza partycji prawo ważne jest, aby optymalnie wykorzystywać aprowizowanej przepływności. Aby uzyskać więcej informacji, zobacz [wybór klucza partycji](partitioning-overview.md) artykułu. 

> [!NOTE]
> Korzystać z zalet systemu zdefiniowanym kluczem partycji, tylko wtedy, gdy jest używana wersja najnowsza/V3 zestawów SDK we wszystkich językach.

Przykładowy kod do utworzenia dokumentu przy użyciu klucza partycji zdefiniowanego systemu i przeczytać ten dokument można znaleźć w poniższym przykładzie:

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Aby uzyskać pełny przykład, zobacz [przykłady dla platformy .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repozytorium GitHub. 
                      
## <a name="migrate-the-documents"></a>Migrowanie dokumentów

Gdy definicja kontenera jest rozszerzona na właściwość klucza partycji, dokumentów w ramach kontenera nie są automatycznie migrowane. Co oznacza, że właściwość klucza partycji systemu `/_partitionKey` ścieżka nie jest automatycznie dodawane do istniejących dokumentów. Musisz partycje istniejących dokumentów, zapoznając się z dokumentami, które zostały utworzone bez klucza partycji, a następnie ponownie je z powrotem zapisuje `_partitionKey` właściwości w dokumentach. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Dostęp do dokumentów, które nie mają klucza partycji

Aplikacje mogą uzyskiwać dostęp do istniejących dokumentów, które nie mają klucza partycji przy użyciu właściwości specjalnych systemu o nazwie "CosmosContainerSettings.NonePartitionKeyValue", jest to wartość, dokumentów nie zostały zmigrowane. Ta właściwość służy w przypadku wszystkich operacji CRUD i zapytań. Poniższy przykład pokazuje przykład odczytywanie pojedynczego dokumentu NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Aby uzyskać pełny przykład o tym, jak na partycje, dokumentów, zobacz [przykłady dla platformy .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repozytorium GitHub. 

## <a name="compatibility-with-sdks"></a>Zgodność z zestawami SDK

Starsza wersja usługi Azure Cosmos DB SDK, takie jak V2.x.x i V1.x.x nie obsługują właściwości klucza partycji zdefiniowane przez system. Tak gdy definicja kontenera odczytywać starszy zestaw SDK, nie zawiera żadnych definicji klucza partycji i te kontenery będą zachowywać się dokładnie tak jak wcześniej. Aplikacje, które są tworzone za pomocą starszej wersji zestawów SDK nadal pracować niepartycjonowana się bez wprowadzania żadnych zmian. 

Jeśli migrowane kontenera jest wykorzystywana przez najnowszą/V3 wersję zestawu SDK i rozpoczęciem wypełnianie klucza partycji zdefiniowane przez system, w ramach nowych dokumentów, nie może uzyskać dostępu (Odczyt, update, delete, zapytania) z tych dokumentów z starszych zestawów SDK już.

## <a name="next-steps"></a>Kolejne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
* [Praca z konta usługi Azure Cosmos](account-overview.md)