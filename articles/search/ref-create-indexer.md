---
title: Utwórz indeksator (wersja interfejsu api REST usługi Azure Search = 2017-11-11-Preview)
description: W wersji zapoznawczej interfejsu API indeksatory zostały rozszerzone na potrzeby uwzględnia parametry outputFieldMapping służy do mapowania wzbogacenia dane wyjściowe do pola w indeksie usługi wyszukiwanie Azure.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Utwórz indeksator (wersja interfejsu api REST usługi Azure Search = 2017-11-11-Preview)

Ta dokumentacja interfejsu API jest wersja preview specyficzne dokumentacji, obejmujące ulepszenia kognitywnych wyszukiwania podczas indeksowania.

Jak [ogólnie dostępna](https://docs.microsoft.com/rest/api/searchservice/create-indexer) wersji, można utworzyć nowego indeksatora w ramach usługi Azure Search przy użyciu żądania HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**Klucz interfejsu api** musi mieć klucz administratora (w przeciwieństwie do klucza zapytania). Zapoznaj się z rozdziałem uwierzytelniania w [zabezpieczeń w usłudze Azure Search](search-security-overview.md) Aby dowiedzieć się więcej na temat kluczy. [Tworzenie usługi Azure Search w portalu](search-create-service-portal.md) wyjaśniono, jak uzyskać adres URL usługi i klucza właściwości używane w żądaniu.

Alternatywnie można użyć PUT i określ nazwę źródła danych w identyfikatorze URI. Źródło danych nie istnieje, zostanie utworzona.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**Wersja interfejsu api** jest wymagana. Bieżąca wersja to `2016-09-01`. Zobacz [wersji interfejsu API w usłudze Azure Search](search-api-versions.md) szczegółowe informacje.

Dla poszczególnych platform danych wskazówki dotyczące tworzenia indeksatorów, rozpoczynać [omówienie indeksatory](search-indexer-overview.md), który zawiera pełną listę [innych pokrewnych artykułach](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Maksymalna liczba indeksatorów dozwolone jest zależna od warstwy cenowej. Bezpłatnej usługi umożliwia indeksatory do 3. Standardowa usługa umożliwia indeksatory 50. Zobacz [ograniczenia usługi](search-limits-quotas-capacity.md) szczegółowe informacje.    

## <a name="request"></a>Żądanie  
 Treść żądania zawiera definicję indeksatora, który określa źródło danych i indeksu docelowego indeksowania, a także opcjonalnie harmonogram indeksowania i parametrów.  

 Składnia struktury ładunku żądania ma następującą składnię. Przykładowe żądanie znajduje się w dalszej części tego tematu.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Planowanie uruchamiania indeksatora  
Indeksator Opcjonalnie można określić harmonogramu. Jeśli harmonogram, indeksatora jest uruchamiany okresowo zgodnie z harmonogramem. Planista są wbudowane; Nie można używać zewnętrznych harmonogramu. **Harmonogram** ma następujące atrybuty: 

-   **Interwał**: wymagane. Uruchamia wartości czasu trwania, który określa interwał lub okres indeksatora. Najmniejszy dopuszczalny interwał wynosi 5 minut; najdłuższej to jeden dzień. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czasu trwania ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Wzór: `"P[nD][T[nH][nM]]".` przykłady: `PT15M` co 15 minut, `PT2H` co 2 godziny.  

-   **wartość startTime**: opcjonalne. UTC datetime podczas indeksatora powinny uruchomione.  

### <a name="indexer-parameters"></a>Parametry indeksatora  
 Indeksator Opcjonalnie można określić kilka parametrów, które mają wpływ na jego zachowania. Wszystkie parametry wymienione poniżej są opcjonalne.  

-   **maxFailedItems**: liczba elementów, które mogą nie zostać pomyślnie zindeksowane, zanim uruchomienie indeksatora zostanie uznane za błąd. Domyślna to 0. Zwraca informacje o elementach nie powiodło się [pobierania stanu indeksatora &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) operacji.  

-   **maxFailedItemsPerBatch**: liczba elementów, które mogą nie zostać pomyślnie zindeksowane w każdej z partii, zanim uruchomienie indeksatora zostanie uznane za błąd. Domyślna to 0.  

-   **batchSize:** określa liczbę elementów, które ze źródła danych do odczytu i indeksowane jako pojedyncza partia w celu zwiększenia wydajności. Wartość domyślna zależy od typu źródła danych: 1000 Azure SQL i bazy danych Azure rozwiązania Cosmos i 10 dla magazynu obiektów Blob Azure.

### <a name="field-mapping-parameters"></a>Mapowania pól parametrów

Definicje indeksatora zawiera pole powiązania dla mapowania pola źródłowego do docelowego pola w indeksie usługi wyszukiwanie Azure. Istnieją dwa typy skojarzenia, w zależności od tego, czy transferu zawartości jest zgodny bezpośrednich lub wzbogaconego ścieżka:

+ **fieldMappings** są opcjonalne i stosowane podczas nazwy pola źródłowego i docelowego nie są zgodne lub jeśli chcesz określić funkcję.
+ **outputFieldMappings** są wymagane, jeśli tworzysz [potoku wzbogacenia](cognitive-search-concept-intro.md). W potoku wzbogacenia pola danych wyjściowych jest konstrukcję zdefiniowane podczas procesu wzbogacenia. Na przykład w polu danych wyjściowych może być strukturą złożone utworzona podczas wzbogacenia z dwa oddzielne pola w dokumencie źródłowym. 

#### <a name="fieldmappings"></a>fieldMappings

W poniższym przykładzie, należy wziąć pod uwagę tabeli źródłowej z polem `_id`. Usługa Azure Search nie zezwala na nazwę pola, począwszy od znaku podkreślenia, można zmienić nazwy pola. Można to zrobić przy użyciu `fieldMappings` właściwości indeksatora w następujący sposób:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Można określić wiele mapowań pól:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Nazwy pól w pliku źródłowym i docelowym jest rozróżniana wielkość liter.

Aby dowiedzieć się więcej o scenariuszach, w którym mapowania pól są przydatne, zobacz [mapowań pól indeksatora wyszukiwania](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

W scenariuszach kognitywnych wyszukiwania, w których skillset jest powiązany z indeksatora, należy dodać `outputFieldMappings` do skojarzenia żadnych danych wyjściowych wzbogacenia krok, który zawiera materiały do pola wyszukiwania w indeksie.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>Funkcje mapowania pól

Mapowania pól mogą służyć do przekształcenia wartości pól źródła przy użyciu *pola mapowania funkcji*. Na przykład dowolny ciąg wartość może być zakodowany w formacie base64, może służyć do wypełnienia pola klucza dokumentu.

Aby uzyskać więcej informacji na temat kiedy i jak należy używać funkcji mapowania pól, zobacz [funkcje mapowania pól](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Przykłady treść żądania  
 Poniższy przykład tworzy indeksatorze, który kopiuje dane z tabeli przywoływanej przez `ordersds` źródła danych do `orders` indeksu zgodnie z harmonogramem, który rozpoczyna się 1 stycznia 2015 UTC i jest uruchamiana co godzinę. Każde wywołanie indeksatora zakończy się pomyślnie, jeśli nie więcej niż 5 elementów nie zostać pomyślnie zindeksowane w każdej z partii, a nie więcej niż 10 elementów nie zostać pomyślnie zindeksowane w sumie.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Odpowiedź  
 201 utworzono dla pomyślnego żądania.  

## <a name="see-also"></a>Zobacz także

+ [Omówienie kognitywnych wyszukiwania](cognitive-search-concept-intro.md)
+ [Szybki Start: Spróbuj kognitywnych wyszukiwania](cognitive-search-quickstart-blob.md)
+ [Sposób mapowania pól](cognitive-search-output-field-mapping.md)
