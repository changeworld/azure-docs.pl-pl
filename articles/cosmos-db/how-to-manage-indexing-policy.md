---
title: Zarządzanie zasadami indeksowania w Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasadami indeksowania w Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: ede4266457aaa76bdd9f1141df5c2981bb722326
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915910"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami indeksowania w Azure Cosmos DB

W Azure Cosmos DB dane są indeksowane zgodnie z [zasadami indeksowania](index-policy.md) , które są zdefiniowane dla każdego kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów wymuszają indeksy zakresu dla dowolnego ciągu lub liczby. Te zasady można zastąpić własnymi zasadami indeksowania niestandardowego.

## <a name="indexing-policy-examples"></a>Przykłady zasad indeksowania

Poniżej przedstawiono kilka przykładów zasad indeksowania pokazywanych w ich formacie JSON, które są widoczne na Azure Portal. Te same parametry można ustawić za pomocą interfejsu wiersza polecenia platformy Azure lub dowolnego zestawu SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Wycofaj zasady, aby selektywnie wykluczać niektóre ścieżki właściwości

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
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

Te zasady indeksowania są równoważne z tymi, które są ustawiane ```kind```ręcznie ```dataType```, i ```precision``` do wartości domyślnych. Te właściwości nie są już wymagane do jawnego ustawiania i można je pominąć z poziomu zasad indeksowania w całości (jak pokazano w powyższym przykładzie).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
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

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Te zasady indeksowania są równoważne z tymi, które są ustawiane ```kind```ręcznie ```dataType```, i ```precision``` do wartości domyślnych. Te właściwości nie są już wymagane do jawnego ustawiania i można je pominąć z poziomu zasad indeksowania w całości (jak pokazano w powyższym przykładzie).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
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
                "path": "/path/to/root/of/multiple/included/properties/*",
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
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Zwykle zaleca się użycie zasad indeksowania **rezygnacji** , aby Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Używanie indeksu przestrzennego tylko dla określonej ścieżki właściwości

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Przykłady złożonych zasad indeksowania

Oprócz dołączania lub wykluczania ścieżek dla poszczególnych właściwości można również określić indeks złożony. Jeśli chcesz wykonać zapytanie `ORDER BY` z klauzulą dla wielu właściwości, wymagany jest [indeks złożony](index-policy.md#composite-indexes) dla tych właściwości. Ponadto indeksy złożone będą mieć korzyść wydajności dla zapytań, które mają filtr i mają klauzulę ORDER BY dla różnych właściwości.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, opis wieku):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
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

Powyższy indeks złożony w polu Nazwa i wiek są wymagane dla #1 zapytań i #2 zapytań:

#1 kwerendy:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

#2 kwerendy:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Ten złożony indeks będzie korzystać z #3 zapytań i #4 zapytań i optymalizowania filtrów:

#3 kwerendy:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 kwerendy:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, wiek ASC) i (nazwa ASC, opis wieku):

W ramach tych samych zasad indeksowania można zdefiniować wiele różnych indeksów złożonych.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
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

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Wykluczanie wszystkich ścieżek właściwości, ale utrzymywanie aktywności indeksowania

Tych zasad można używać w sytuacjach, gdy [Funkcja czasu wygaśnięcia (TTL)](time-to-live.md) jest aktywna, ale nie jest wymagany żaden dodatkowy indeks (aby użyć Azure Cosmos dB jako czystego magazynu klucz-wartość).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Brak indeksowania

Te zasady spowodują wyłączenie indeksowania. Jeśli `indexingMode` jest ustawiona na `none`, nie można ustawić czasu wygaśnięcia dla kontenera.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aktualizowanie zasad indeksowania

W Azure Cosmos DB zasady indeksowania można aktualizować przy użyciu dowolnej z poniższych metod:

- z Azure Portal
- Korzystanie z interfejsu wiersza polecenia platformy Azure
- Korzystanie z jednego z zestawów SDK

[Aktualizacja zasad indeksowania](index-policy.md#modifying-the-indexing-policy) wyzwala transformację indeksu. Postęp tego przekształcenia może być również śledzony z zestawów SDK.

> [!NOTE]
> Podczas aktualizowania zasad indeksowania zapisy do Azure Cosmos DB będą nieprzerwane. Podczas ponownego indeksowania zapytania mogą zwracać częściowe wyniki w miarę aktualizowania indeksu.

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

Pobierz szczegóły kontenera

```csharp
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
```

Ustawianie spójności trybu indeksowania

```csharp
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
```

Dodaj dołączoną ścieżkę

```csharp
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/age/*" });
```

Dodaj wykluczoną ścieżkę

```csharp
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
```

Dodawanie indeksu przestrzennego

```csharp
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
```

Dodawanie indeksu złożonego

```csharp
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
```

Aktualizowanie kontenera ze zmianami

```csharp
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

Pobierz szczegóły kontenera

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

Ustawianie spójności trybu indeksowania

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

Dodaj dołączoną ścieżkę

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

Dodaj wykluczoną ścieżkę

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

Dodawanie indeksu przestrzennego

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

Dodawanie indeksu złożonego

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

Aktualizowanie kontenera ze zmianami

```java
 client.replaceCollection(container, null);
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

Pobierz szczegóły kontenera

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Ustawianie spójności trybu indeksowania

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Dodaj dołączoną ścieżkę obejmującą indeks przestrzenny

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Dodaj wykluczoną ścieżkę

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Aktualizowanie kontenera ze zmianami

```javascript
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

Pobierz szczegóły kontenera

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Ustawianie spójności trybu indeksowania

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definiowanie zasad indeksowania z dołączoną ścieżką i indeksem przestrzennym

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definiowanie zasad indeksowania przy użyciu ścieżki wykluczonej

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Dodawanie indeksu złożonego

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Aktualizowanie kontenera ze zmianami

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](index-policy.md)
