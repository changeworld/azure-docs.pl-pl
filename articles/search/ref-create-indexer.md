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
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Utwórz indeksator (wersja interfejsu api REST usługi Azure Search = 2017-11-11-Preview)

Ta dokumentacja interfejsu API jest wersja preview specyficzne dokumentacji, dodawanie [kognitywnych wyszukiwania](cognitive-search-concept-intro.md) elementów do tworzenia interfejsu API indeksatora. Jak [ogólnie dostępna](https://docs.microsoft.com/rest/api/searchservice/create-indexer) wersji, można utworzyć nowego indeksatora w ramach usługi Azure Search przy użyciu żądania HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**Klucz interfejsu api** musi mieć klucz administratora (w przeciwieństwie do klucza zapytania). Zapoznaj się z rozdziałem uwierzytelniania w [zabezpieczeń w usłudze Azure Search](search-security-overview.md) Aby dowiedzieć się więcej na temat kluczy. [Tworzenie usługi Azure Search w portalu](search-create-service-portal.md) wyjaśniono, jak uzyskać adres URL usługi i klucza właściwości używane w żądaniu.

Alternatywnie można użyć PUT i określ nazwę źródła danych w identyfikatorze URI. Źródło danych nie istnieje, zostanie utworzona.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**Wersja interfejsu api** jest wymagana. Bieżącej wersji dostępnych jest zwykle `api-version=2017-11-11`, ale należy wersję zapoznawczą kognitywnych wyszukiwania: `api-version=2017-11-11-Preview`.  Zobacz [wersji interfejsu API w usłudze Azure Search](search-api-versions.md) szczegółowe informacje.

Dla poszczególnych platform danych wskazówki dotyczące tworzenia indeksatorów, rozpoczynać [omówienie indeksatory](search-indexer-overview.md), który zawiera pełną listę [innych pokrewnych artykułach](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Maksymalna liczba indeksatorów dozwolone jest zależna od warstwy cenowej. Bezpłatnej usługi umożliwia indeksatory do 3. Standardowa usługa umożliwia indeksatory 50. Standardowe usługi wysokiej nie obsługują indeksatorów w ogóle. Zobacz [ograniczenia usługi](search-limits-quotas-capacity.md) szczegółowe informacje.    

## <a name="request"></a>Żądanie  

A [źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), i [skillset](ref-create-skillset.md) są częścią [indeksatora](search-indexer-overview.md) definicji, ale każda jest niezależna składnik, który może być używana w różnych kombinacji. Na przykład można użyć tego samego źródła danych z wielu indeksatory lub ten sam indeks z indeksatorów wielu lub wiele indeksatorów zapisywania do jednego indeksu.

 Treść żądania zawiera definicję indeksatora, z następujących części.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Składnia żądania

Dla struktury ładunku żądania ma następującą składnię. Przykładowe żądanie znajduje się w dalszej części tego artykułu.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

A [definicji źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) często obejmuje właściwości, które indeksatora umożliwia wykorzystanie właściwości platformy źródła. Tak źródła danych, które są przekazywane do indeksatora określa dostępność niektórych właściwości i parametrów, typu zawartości filtrowanie danych na obiekty BLOB platformy Azure lub limit czasu zapytania bazy danych SQL Azure. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[Schematu indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) definiuje kolekcji pól zawierających przypisań wyszukiwanie, filtrowanie, pobieranie i innych, które określają sposób używania pola. Podczas indeksowania, indeksatora przeszukuje źródła danych, opcjonalnie pęknięcia dokumentów i wyodrębnia dane serializuje wyniki w formacie JSON i indeksuje ładunku na podstawie schematu zdefiniowane dla indeksu.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[Kognitywnych wyszukiwania (wersja zapoznawcza)](cognitive-search-concept-intro.md) odwołuje się do języka naturalnego i możliwości w usłudze Azure Search, zastosowane podczas wprowadzanie danych, aby wyodrębnić jednostek, fraz klucza, języka, informacje z obrazów i tak dalej przetwarzania obrazu. Przekształcenia stosowane do zawartości są za pośrednictwem *umiejętności*, które są łączone w pojedynczy [ *skillset*](ref-create-skillset.md), po jednym dla każdego indeksatora. Podobnie jak w przypadku źródeł danych i indeksów skillset jest niezależna składnik, który można dołączyć do indeksatora. Możesz ponownie użyć skillset z innymi indeksatorami, ale każdego indeksatora w czasie można używać tylko jednego skillset.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"harmonogram"  
Indeksator Opcjonalnie można określić harmonogramu. Bez harmonogramu, indeksatora jest uruchamiany natychmiast po wysłaniu żądania: nawiązywanie połączenia, przeszukiwania i indeksowania źródła danych. Dla niektórych scenariuszy, w tym długotrwałe zadania indeksowania, są używane do harmonogramów [rozszerzyć okna przetwarzania](search-howto-reindex.md#scale-out-indexing) poza maksymalnie 24-godzinnym. Jeśli harmonogram, indeksatora jest uruchamiany okresowo zgodnie z harmonogramem. Planista korzysta z wbudowanej w; Nie można używać zewnętrznych harmonogramu. A **harmonogram** ma następujące atrybuty: 

-   **Interwał**: wymagane. Uruchamia wartości czasu trwania, który określa interwał lub okres indeksatora. Najmniejszy dopuszczalny interwał to pięć minut; najdłuższej to jeden dzień. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czasu trwania ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Wzór: `"P[nD][T[nH][nM]]".` przykłady: `PT15M` co 15 minut, `PT2H` co 2 godziny.  

-   **wartość startTime**: opcjonalne. UTC datetime podczas indeksatora powinny uruchomione.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"Parametry"

Indeksator opcjonalnie może zająć parametry konfiguracji, które modyfikują zachowanie w czasie wykonywania. Parametry konfiguracji są rozdzielane przecinkami na żądanie indeksatora. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Parametry ogólne dla wszystkich indeksatorów

| Parametr | Dozwolone wartości i typ   | Sposób użycia  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Liczba całkowita<br/>Domyślnie jest specyficzna dla źródła (1000 dla usługi Azure SQL Database i Azure rozwiązania Cosmos DB, 10 dla usługi Azure Blob Storage) | Określa liczbę elementów, które ze źródła danych do odczytu i indeksowane jako pojedyncza partia w celu zwiększenia wydajności. |
| `"maxFailedItems"` | Liczba całkowita<br/>Wartość domyślna to 0 | Liczba błędów na tolerować zanim uruchomienia indeksatora zostanie uznane za błąd. Wartość -1, jeśli nie chcesz, aby wszelkie błędy, aby zatrzymać proces indeksowania. Można pobrać informacji o elementach nie powiodło się przy użyciu [pobierania stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Liczba całkowita<br/>Wartość domyślna to 0 | Liczba błędów na tolerować w każdej z partii, zanim uruchomienia indeksatora zostanie uznane za błąd. Wartość -1, jeśli nie chcesz, aby wszelkie błędy, aby zatrzymać proces indeksowania. |

#### <a name="blob-configuration-parameters"></a>Parametry konfiguracji obiektów blob

Kilka parametrów są na wyłączność dla określonego indeksatora, takich jak [indeksowanie obiektów blob platformy Azure](search-howto-indexing-azure-blob-storage.md).

| Parametr | Dozwolone wartości i typ   | Sposób użycia  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Ciąg<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | Aby uzyskać [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), należy ustawić `text` zwiększania wydajności indeksowania plików w postaci zwykłego tekstu w magazynie obiektów blob. <br/>Dla [obiekty BLOB CSV](search-howto-index-csv-blobs.md), należy ustawić `delimitedText` kiedy obiekty BLOB są zwykłe pliki CSV. <br/>Dla [obiekty BLOB JSON](search-howto-index-json-blobs.md), należy ustawić `json` do wyodrębniania strukturę zawartości lub do `jsonArray` (wersja zapoznawcza), aby wyodrębnić poszczególne elementy tablicy jako oddzielnych dokumentów w usłudze Azure Search. |
| `"excludedFileNameExtensions"` | Ciąg<br/>rozdzielana przecinkami lista | Aby uzyskać [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), Ignoruj wszystkie typy plików, na liście. Na przykład można wykluczyć "PNG, PNG, plik MP4" można pominąć te pliki podczas indeksowania. | 
| `"indexedFileNameExtensions"` | Ciąg<br/>rozdzielana przecinkami lista | Aby uzyskać [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), wybiera obiekty BLOB, jeśli rozszerzenie pliku znajduje się na liście. Na przykład można skoncentrować indeksowania na określonej aplikacji pliki "docx, pptx, .msg" specjalnie uwzględnienie tych typów plików. | 
| `"failOnUnsupportedContentType"` | true <br/>Wartość FAŁSZ (ustawienie domyślne) | Aby uzyskać [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), należy ustawić `false` czy chcesz kontynuować indeksowanie podczas napotkano nieobsługiwany typ zawartości, a nie znasz z wyprzedzeniem wszystkie typy zawartości (rozszerzenia pliku). |
| `"failOnUnprocessableDocument"` | true <br/>Wartość FAŁSZ (ustawienie domyślne)| Dla [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), należy ustawić `false` Jeśli chcesz kontynuować indeksowanie, jeśli dokument nie powiedzie się indeksowania. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>Wartość FAŁSZ (ustawienie domyślne)| Dla [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), ustawić tę właściwość na `true` nadal indeksowania magazynu metadanych dla zawartości obiektu blob, który jest zbyt duży, aby przetworzyć.  Zbyt duże obiekty BLOB są traktowane jako błędy domyślnie. Limity rozmiaru obiektu blob, zobacz [ograniczenia usługi](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Ciąg<br/>rozdzielana przecinkami lista| Aby uzyskać [CSV obiektów blob (wersja zapoznawcza)](search-howto-index-csv-blobs.md), określa rozdzielana przecinkami lista nagłówków kolumn, przydatne w przypadku mapowania pól źródłowego na docelowy pól w indeksie. |
| `"delimitedTextDelimiter"` | Ciąg<br/>Zdefiniowane przez użytkownika | Dla [CSV obiektów blob (wersja zapoznawcza)](search-howto-index-csv-blobs.md), określa ogranicznik końca wiersza dla plików CSV, w której każdy wiersz rozpoczęcie tworzenia nowego dokumentu (na przykład, ""|"`).  |
| `"firstLineContainsHeaders"` | wartość true (ustawienie domyślne) <br/>false | Aby uzyskać [CSV obiektów blob (wersja zapoznawcza)](search-howto-index-csv-blobs.md), wskazuje, że w pierwszym wierszu (niepustych) każdy obiekt blob zawiera nagłówki.|
| `"documentRoot"`  | Ciąg<br/>Zdefiniowane przez użytkownika | Dla [tablice notacji JSON (wersja zapoznawcza)](search-howto-index-json-blobs.md#nested-json-arrays), mając strukturalnych lub częściowo ustrukturyzowanych dokument, można określić ścieżkę do tablicy za pomocą tej właściwości. |
| `"dataToExtract"` | Ciąg<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (ustawienie domyślne) | Aby uzyskać [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md):<br/>Ustaw `"storageMetadata"` do indeksu po prostu [właściwości standardowych obiektów blob i metadanych określonych przez użytkownika](../storage/blobs/storage-properties-metadata.md). <br/>Ustaw `"allMetadata"` można wyodrębnić metadanych pochodzącymi z podsystemem magazynu obiektów blob platformy Azure i [metadane specyficzne dla typu zawartości](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (na przykład metadane unikatowa tylko pliki PNG) są indeksowane. <br/>Ustaw `"contentAndMetadata"` wyodrębnić wszystkie metadane i zawartość tekstową z każdego obiektu blob. <br/><br/>Dla [analizy obrazu w wyszukiwaniu kognitywnych (wersja zapoznawcza)](cognitive-search-concept-image-scenarios.md), gdy `"imageAction"` ustawiono `"generateNormalizedImages"`, `"dataToExtract"` ustawienie informuje indeksatora danych do wyodrębniania zawartości obrazu. Stosuje się do osadzonego obrazu zawartość. PDF lub innej aplikacji lub pliki obrazów, np. jpg i PNG w obiektach blob Azure.  |
| `"imageAction"` |  Ciąg<br/>`"none"`<br/>`"generateNormalizedImages"` | Dla [obiektów blob Azure](search-howto-indexing-azure-blob-storage.md), należy ustawić`"none"` zignorowanie osadzonych obrazów i plików obrazów w zestawie danych. Jest to wartość domyślna. <br/><br/>Dla [analizy obrazu w wyszukiwaniu kognitywnych](cognitive-search-concept-image-scenarios.md), należy ustawić`"generateNormalizedImages"` wyodrębnienie tekstu z obrazów (na przykład wyraz "stop" od ruchu znak) i osadzić go jako część zawartości pola. Podczas analizy obrazu indeksatora tworzy tablicę znormalizowane obrazów jako część łamania dokumentu i osadza wygenerowanych informacjach w polu zawartości. Ta akcja wymaga, aby `"dataToExtract"` ma ustawioną wartość `"contentAndMetadata"`. Znormalizowane obrazu odwołuje się do dodatkowego przetwarzania wynikowa uniform obrazu wyjściowego, o rozmiarze i obracać wspieranie spójne renderowanie, gdy obejmują obrazy w wynikach wyszukiwania visual (na przykład fotografie sam rozmiar na wykresie sterować w [Pokaz JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Informacje te są generowane dla każdego obrazu, gdy używasz |


#### <a name="other-configuration-parameters"></a>Inne parametry konfiguracji

Następujące parametry są specyficzne dla bazy danych SQL Azure.

| Parametr | Dozwolone wartości i typ   | Sposób użycia  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Ciąg<br/>"hh: mm:"<br/>"00: 05:00"| Aby uzyskać [bazy danych SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), ustaw ten parametr, aby zwiększyć limit czasu ponad wartość domyślną 5 minut.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Definicje indeksatora zawiera pole powiązania dla mapowania pola źródłowego do docelowego pola w indeksie usługi wyszukiwanie Azure. Istnieją dwa typy skojarzenia, w zależności od tego, czy transferu zawartości jest zgodny bezpośrednich lub wzbogaconego ścieżka:

+ **fieldMappings** są opcjonalne i stosowane podczas nazwy pola źródłowego i docelowego nie są zgodne lub jeśli chcesz określić funkcję.
+ **outputFieldMappings** są wymagane, jeśli tworzysz [potoku wzbogacenia](cognitive-search-concept-intro.md). W potoku wzbogacenia pola danych wyjściowych jest konstrukcję zdefiniowane podczas procesu wzbogacenia. Na przykład w polu danych wyjściowych może być strukturą złożone utworzona podczas wzbogacenia z dwa oddzielne pola w dokumencie źródłowym. 

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

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

W [kognitywnych wyszukiwania](cognitive-search-concept-intro.md) scenariusze, w których skillset jest powiązany z indeksatora, należy dodać `outputFieldMappings` do skojarzenia żadnych danych wyjściowych wzbogacenia krok, który zawiera materiały do pola wyszukiwania w indeksie.

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

### <a name="field-mapping-functions"></a>Funkcje mapowania pól

Mapowania pól mogą służyć do przekształcenia wartości pól źródła przy użyciu *pola mapowania funkcji*. Na przykład dowolny ciąg wartość może być zakodowany w formacie base64, może służyć do wypełnienia pola klucza dokumentu.

Aby uzyskać więcej informacji na temat kiedy i jak należy używać funkcji mapowania pól, zobacz [funkcje mapowania pól](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Przykładowe żądanie  
 W pierwszym przykładzie jest tworzony indeksatorze, który kopiuje dane z tabeli przywoływanej przez `ordersds` źródła danych do `orders` indeksu zgodnie z harmonogramem, który rozpoczyna się 1 stycznia 2015 UTC i jest uruchamiana co godzinę. Każde wywołanie indeksatora zakończy się pomyślnie, jeśli nie więcej niż 5 elementów nie zostać pomyślnie zindeksowane w każdej z partii, a nie więcej niż 10 elementów nie zostać pomyślnie zindeksowane w sumie.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

W drugim przykładzie pokazano operacji wyszukiwania kognitywnych, wskazano w odniesieniu do skillset i [outputFieldMappings](#output-fieldmappings). [Skillsets](ref-create-skillset.md) zasobów wysokiego poziomu, określane oddzielnie. W tym przykładzie jest formą skróconą definicji indeksatora w [samouczek search kognitywnych](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Odpowiedź  
 201 utworzono dla pomyślnego żądania.  

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Omówienie kognitywnych wyszukiwania](cognitive-search-concept-intro.md)
+ [Szybki Start: Spróbuj kognitywnych wyszukiwania](cognitive-search-quickstart-blob.md)
+ [Sposób mapowania pól](cognitive-search-output-field-mapping.md)
