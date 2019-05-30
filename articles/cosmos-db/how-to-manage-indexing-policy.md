---
title: Zarządzanie zasadami indeksowania w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasadami indeksowania w usłudze Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 05fd369cfebba03c814507f82755fa6cb6a89400
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386803"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami indeksowania w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB, zindeksowaniu danych po [zasady indeksowania](index-policy.md) które są definiowane dla każdego kontenera. Domyślne zasady dla nowo utworzonego kontenerów indeksowania wymusza zakresem indeksów dla dowolnego ciągu lub liczby i indeksów przestrzennych dla dowolnego obiektu GeoJSON typu punktu. Te zasady można przesłonić:

- W witrynie Azure portal
- Przy użyciu wiersza polecenia platformy Azure
- przy użyciu jednego z zestawów SDK

[Aktualizacji zasad indeksowania](index-policy.md#modifying-the-indexing-policy) wyzwala przekształcania indeksu. Również można śledzić postęp tej transformacji z zestawów SDK.

> [!NOTE]
> W ramach uaktualniania zestawu SDK i Portal są obecnie przekształcane zasad indeksu dopasowanie z nowym układem indeksu, w których firma Microsoft ma dostępna dla nowych kontenerów. Za pomocą tego nowego układu wszystkie pierwotne typy danych są indeksowane, jako zakres o pełnej dokładności (-1). W związku z tym rodzaje indeksu i dokładności nie są widoczne dla użytkownika już. W przyszłości użytkownicy będą musieli po prostu Dodaj do sekcji includedPaths ścieżki i ignoruje indexKinds i dokładność. Ta zmiana nie ma wpływu na wydajność i można kontynuować aktualizacji zasad indeksowania, przy użyciu tej samej składni. Można nadal używać wszystkie przykłady w naszej dokumentacji istniejących można zaktualizować zasad indeksu.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kontenery usługi Azure Cosmos przechowywać swoje zasady indeksowania jako dokument JSON umożliwiająca witryny Azure portal, można edytować bezpośrednio.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

1. Otwórz **Eksplorator danych** okienka i wybierz kontener, w którym chcesz pracować.

1. Kliknij pozycję **skalowanie i ustawienia**.

1. Modyfikowanie indeksowania dokumentów JSON zasad (Zobacz przykłady [poniżej](#indexing-policy-examples))

1. Kliknij przycisk **Zapisz** po zakończeniu.

![Zarządzanie indeksowaniem przy użyciu witryny Azure Portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

[Az cosmosdb kolekcji aktualizacja](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) polecenie z wiersza polecenia platformy Azure pozwala zastąpić definicję formatu JSON kontenera zasad indeksowania:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Użyj zestawu SDK platformy .NET w wersji 2

`DocumentCollection` Obiektu z [zestawu SDK platformy .NET w wersji 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (zobacz [ten przewodnik Szybki Start](create-sql-api-dotnet.md) dotyczące jego użycia) udostępnia `IndexingPolicy` właściwość, która pozwala na zmianę `IndexingMode` i dodać lub usunąć `IncludedPaths` i `ExcludedPaths`.

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

Aby śledzić postępy przekształcania indeksu, należy przekazać `RequestOptions` obiekt, który ustawia `PopulateQuotaInfo` właściwość `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

`DocumentCollection` Obiektu z [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (zobacz [ten przewodnik Szybki Start](create-sql-api-java.md) dotyczące jego użycia) udostępnia `getIndexingPolicy()` i `setIndexingPolicy()` metody. `IndexingPolicy` Obiektu, ich modyfikowania pozwala zmienić tryb indeksowania i dodać lub usunąć ścieżki dołączone i wykluczone.

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

Aby śledzić postępy przekształcania indeksu w kontenerze, należy przekazać `RequestOptions` obiektu, który żąda informacji o limicie przydziału wypełniona, a następnie pobrać wartość z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

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

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node.js

`ContainerDefinition` Interfejs z [zestawu SDK środowiska Node.js](https://www.npmjs.com/package/@azure/cosmos) (zobacz [ten przewodnik Szybki Start](create-sql-api-nodejs.md) dotyczące jego użycia) udostępnia `indexingPolicy` właściwość, która pozwala na zmianę `indexingMode` i dodać lub usunąć `includedPaths` i `excludedPaths`.

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

Aby śledzić postępy przekształcania indeksu w kontenerze, należy przekazać `RequestOptions` obiekt, który ustawia `populateQuotaInfo` właściwości `true`, następnie pobierają wartości z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Korzystanie z języka Python SDK

Korzystając z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) (zobacz [ten przewodnik Szybki Start](create-sql-api-python.md) dotyczące jego użycia), konfiguracja kontenera jest zarządzany w formie słownika. Z tego słownika użytkownik może uzyskiwać dostęp do zasad indeksowania i jego atrybuty.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Przykłady operacji dotyczących zasad indeksowania

Poniżej przedstawiono kilka przykładów zasad indeksowania wyświetlane w formacie JSON, czyli o tym, jak są one widoczne w witrynie Azure portal. Te same parametry można ustawić za pomocą wiersza polecenia platformy Azure lub dowolnego zestawu SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Zasady rezygnacji, aby selektywnie wykluczyć niektóre ścieżki właściwości
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zasady uczestnictwo uwzględnić tylko niektóre ścieżki właściwości
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

Uwaga: Ogólnie zaleca się używanie **rezygnacji** indeksowanie zasad, aby umożliwić usłudze Azure Cosmos DB aktywnie indeksowania nowej właściwości, które mogą zostać dodane do modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Przy użyciu indeksu przestrzennego na tylko do określonych właściwości ścieżki
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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Wykluczenie ścieżek wszystkich właściwości, ale zachowuje indeksowanie jest aktywny

Tej zasady należy używać w sytuacjach, w której [funkcji czasu wygaśnięcia (TTL)](time-to-live.md) jest aktywna, ale nie pomocniczy indeks jest wymagane (do używania usługi Azure Cosmos DB jako czysty parach klucz wartość).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nie indeksowania
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Złożone Przykłady zasad indeksowania

Oprócz uwzględniając lub wykluczając ścieżki dla poszczególnych właściwości, można również określić indeksie złożonym. Jeśli chcesz wykonywać zapytania, które zawiera `ORDER BY` klauzula dla wielu właściwości [indeksu złożonego](index-policy.md#composite-indexes) na tych właściwości jest wymagana.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indeks złożone zdefiniowane dla (nazwa asc, wiek, desc):
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

Ten indeks złożony będzie mogła obsługiwać następujące dwa zapytania:

Zapytanie #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Zapytanie #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indeks złożony zdefiniowane dla (nazwa asc, wieku asc) i (nazwa usługi asc, wiek desc):

Można zdefiniować wiele różnych indeksów złożonego w ramach tych samych zasad indeksowania. 
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indeks złożone zdefiniowane dla (nazwa asc, wieku asc):

Jest to opcjonalne określić kolejność. Jeśli nie zostanie określony, kolejność jest rosnąca.
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

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](index-policy.md)
