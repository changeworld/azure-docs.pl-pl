---
title: Zarządzanie zasadami indeksowania w Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasadami indeksowania w Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: thweiss
ms.openlocfilehash: 6e935b88c474d28c06b8cdd76d36f5ba64c942f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093206"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami indeksowania w Azure Cosmos DB

W Azure Cosmos DB dane są indeksowane zgodnie z [zasadami indeksowania](index-policy.md) , które są zdefiniowane dla każdego kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów wymuszają indeksy zakresu dla dowolnego ciągu lub liczby oraz indeksów przestrzennych dla dowolnego obiektu GEOJSON typu punkt. Te zasady można zastąpić:

- z Azure Portal
- Korzystanie z interfejsu wiersza polecenia platformy Azure
- Korzystanie z jednego z zestawów SDK

[Aktualizacja zasad indeksowania](index-policy.md#modifying-the-indexing-policy) wyzwala transformację indeksu. Postęp tego przekształcenia może być również śledzony z zestawów SDK.

> [!NOTE]
> W ramach uaktualnienia zestawu SDK i portalu zmieniamy zasady indeksu w celu dostosowania ich do nowych kontenerów, które zostały wdrożone w ramach nowego układu. W tym nowym układzie wszystkie typy danych pierwotnych są indeksowane jako zakres z pełną dokładnością (-1). W związku z tym typy indeksów i precyzji nie są już udostępniane użytkownikowi. W przyszłości użytkownicy będą musieli po prostu dodać ścieżki do sekcji includedPaths i zignorować indexKinds i precyzję. Ta zmiana nie ma wpływu na wydajność i można kontynuować aktualizowanie zasad indeksowania przy użyciu tej samej składni. Aby zaktualizować zasady indeksu, można nadal używać wszystkich przykładów w istniejącej dokumentacji.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kontenery usługi Azure Cosmos przechowują swoje zasady indeksowania jako dokument JSON, który Azure Portal umożliwia bezpośrednie edytowanie.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz kontener, w którym chcesz korzystać.

1. Kliknij pozycję **skaluj & ustawienia**.

1. Modyfikowanie dokumentu JSON zasad indeksowania (Zobacz przykłady [poniżej](#indexing-policy-examples))

1. Po zakończeniu kliknij przycisk **Zapisz** .

![Zarządzanie indeksowaniem przy użyciu witryny Azure Portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Polecenie [AZ cosmosdb Collection Update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) w interfejsie wiersza polecenia platformy Azure umożliwia zastąpienie definicji JSON zasad indeksowania kontenera:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Korzystanie z zestawu .NET SDK V2

`IndexingMode` `IndexingPolicy` `ExcludedPaths` `IncludedPaths` [](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) [](create-sql-api-dotnet.md) Obiekt z zestawu .NET SDK V2 (zobacz ten przewodnik Szybki Start dotyczący użycia) uwidacznia właściwość, która umożliwia zmianę i dodanie lub usunięcie i. `DocumentCollection`

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Aby śledzić postęp transformacji indeksu, należy przekazać `RequestOptions` obiekt, który `PopulateQuotaInfo` ustawia właściwość na `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

`getIndexingPolicy()` `setIndexingPolicy()` [](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) [](create-sql-api-java.md) Obiekt z zestawu Java SDK (zobacz ten przewodnik Szybki Start dotyczący użycia) i metody. `DocumentCollection` Obiekt `IndexingPolicy` , który manipuluje, umożliwia zmianę trybu indeksowania i Dodawanie lub usuwanie dołączonych i wykluczonych ścieżek.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Aby śledzić postęp transformacji indeksu w kontenerze, należy przekazać `RequestOptions` obiekt, który żąda informacji o przydziale, a następnie pobrać wartość `x-ms-documentdb-collection-index-transformation-progress` z nagłówka odpowiedzi.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node. js

`indexingMode` `indexingPolicy` `excludedPaths` `includedPaths` [](https://www.npmjs.com/package/@azure/cosmos) [](create-sql-api-nodejs.md) Interfejs z zestawu SDK środowiska Node. js (zobacz ten przewodnik Szybki Start dotyczący użycia) uwidacznia właściwość, która umożliwia zmianę i dodanie lub usunięcie i. `ContainerDefinition`

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Aby śledzić postęp transformacji indeksu w `RequestOptions` kontenerze, należy przekazać obiekt, który `populateQuotaInfo` ustawia właściwość na `true`, a następnie pobrać wartość z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Korzystanie z zestawu SDK języka Python

W przypadku korzystania z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) (zobacz [ten przewodnik Szybki Start](create-sql-api-python.md) dotyczący użycia) Konfiguracja kontenera jest zarządzana jako słownik. Z tego słownika można uzyskać dostęp do zasad indeksowania i wszystkich jej atrybutów.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [
    {"path": "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Przykłady zasad indeksowania

Poniżej przedstawiono kilka przykładów zasad indeksowania pokazywanych w ich formacie JSON, które są widoczne na Azure Portal. Te same parametry można ustawić za pomocą interfejsu wiersza polecenia platformy Azure lub dowolnego zestawu SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Wycofaj zasady, aby selektywnie wykluczać niektóre ścieżki właściwości
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zasady zgody na wybiórcze uwzględnienie niektórych ścieżek właściwości
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Uwaga: Zwykle zaleca się użycie zasad indeksowania **rezygnacji** , aby Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Używanie indeksu przestrzennego tylko dla określonej ścieżki właściwości
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Wykluczanie wszystkich ścieżek właściwości, ale utrzymywanie aktywności indeksowania

Tych zasad można używać w sytuacjach, gdy [Funkcja czasu wygaśnięcia (TTL)](time-to-live.md) jest aktywna, ale nie jest wymagany żaden dodatkowy indeks (aby użyć Azure Cosmos dB jako czystego magazynu klucz-wartość).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Brak indeksowania
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Przykłady złożonych zasad indeksowania

Oprócz dołączania lub wykluczania ścieżek dla poszczególnych właściwości można również określić indeks złożony. Jeśli chcesz wykonać zapytanie `ORDER BY` z klauzulą dla wielu właściwości, wymagany jest [indeks złożony](index-policy.md#composite-indexes) dla tych właściwości.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, opis wieku):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Ten złożony indeks będzie mógł obsługiwać następujące dwa zapytania:

#1 kwerendy:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

#2 kwerendy:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, wiek ASC) i (nazwa ASC, opis wieku):

W ramach tych samych zasad indeksowania można zdefiniować wiele różnych indeksów złożonych. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, wiek ASC):

Określenie zamówienia jest opcjonalne. Jeśli nie zostanie określony, kolejność jest rosnąca.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](index-policy.md)
