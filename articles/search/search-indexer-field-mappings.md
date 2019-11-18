---
title: Mapowania pól w indeksatorach
titleSuffix: Azure Cognitive Search
description: Skonfiguruj mapowania pól w indeksatorze, aby uwzględnić różnice w nazwach pól i reprezentacjach danych.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72623787cdb27c568fe2b4ec075010674a3996ef
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123993"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapowania pól i przekształcenia przy użyciu indeksatorów usługi Azure Wyszukiwanie poznawcze

W przypadku korzystania z usługi Azure Wyszukiwanie poznawcze indeksatorów czasami okaże się, że dane wejściowe nie są całkowicie zgodne ze schematem docelowego indeksu. W takich przypadkach można użyć **mapowań pól** , aby zmienić kształt danych podczas procesu indeksowania.

Niektóre sytuacje, w których mapowania pól są użyteczne:

* Źródło danych ma pole o nazwie `_id`, ale usługa Azure Wyszukiwanie poznawcze nie zezwala na używanie nazw pól, które zaczynają się od znaku podkreślenia. Mapowanie pól pozwala efektywnie zmienić nazwę pola.
* Chcesz wypełnić kilka pól w indeksie z tych samych danych źródła danych. Na przykład możesz chcieć zastosować różne analizatory do tych pól.
* Chcesz wypełnić pole indeksu danymi z więcej niż jednego źródła danych, a źródła danych używają różnych nazw pól.
* Musisz zakodować lub zdekodować dane w formacie base64. Mapowania pól obsługują kilka **funkcji mapowania**, w tym funkcje kodowania i dekodowania Base64.

> [!NOTE]
> Funkcja mapowania pól dla indeksatorów platformy Azure Wyszukiwanie poznawcze zapewnia prosty sposób mapowania pól danych do pól indeksu, z kilkoma opcjami konwersji danych. Bardziej złożone dane mogą wymagać wstępnego przetwarzania, aby kształtować go w postaci łatwej do indeksowania.
>
> Microsoft Azure Data Factory to zaawansowane rozwiązanie oparte na chmurze służące do importowania i przekształcania danych. Możesz również napisać kod, aby przekształcić dane źródłowe przed indeksowanie. Aby zapoznać się z przykładami kodu, zobacz artykuł [Modelowanie danych relacyjnych](search-example-adventureworks-modeling.md) i [model wielopoziomowych aspektów](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Skonfiguruj mapowania pól

Mapowanie pola składa się z trzech części:

1. `sourceFieldName`, która reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalna `targetFieldName`, która reprezentuje pole w indeksie wyszukiwania. W przypadku pominięcia zostanie użyta taka sama nazwa jak w źródle danych.
3. Opcjonalna `mappingFunction`, która może przekształcić dane przy użyciu jednej z kilku wstępnie zdefiniowanych funkcji. Pełna lista funkcji znajduje się [poniżej](#mappingFunctions).

Mapowania pól są dodawane do tablicy `fieldMappings` definicji indeksatora.

## <a name="map-fields-using-the-rest-api"></a>Mapowanie pól przy użyciu interfejsu API REST

Można dodać mapowania pól podczas tworzenia nowego indeksatora przy użyciu żądania interfejsu API [tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . Można zarządzać mapowaniami pól istniejącego indeksatora przy użyciu żądania API [Update indeksator](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Na przykład poniżej przedstawiono sposób mapowania pola źródłowego do pola docelowego o innej nazwie:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Do pola źródłowego można odwoływać się w wielu mapowaniach pól. Poniższy przykład pokazuje, jak "rozwidlenie" pola, kopiując te same pola źródła do dwóch różnych pól indeksu:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Usługa Azure Wyszukiwanie poznawcze używa porównania bez uwzględniania wielkości liter, aby rozpoznać nazwy pól i funkcji w mapowaniu pól. Jest to wygodne (nie trzeba uzyskać całej wielkości liter), ale oznacza to, że źródło danych lub indeks nie mogą mieć pól, które różnią się tylko wielkością liter.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapowanie pól przy użyciu zestawu .NET SDK

Mapowania pól można definiować w zestawie .NET SDK przy użyciu klasy [fieldmapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , która ma właściwości `SourceFieldName` i `TargetFieldName`i opcjonalne odwołanie `MappingFunction`.

Można określić mapowania pól podczas konstruowania indeksatora lub później przez bezpośrednie ustawienie właściwości `Indexer.FieldMappings`.

Poniższy C# przykład ustawia mapowania pól podczas konstruowania indeksatora.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkcje mapowania pól

Funkcja mapowania pól przekształca zawartość pola, zanim zostanie on zapisany w indeksie. Następujące funkcje mapowania są obecnie obsługiwane:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode, funkcja

Wykonuje bezpieczne kodowanie Base64 dla *adresu URL* w ciągu wejściowym. Przyjęto założenie, że dane wejściowe są kodowane w formacie UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie klucza dokumentu

W kluczu dokumentu usługi Azure Wyszukiwanie poznawcze mogą występować tylko bezpieczne znaki w adresie URL (ponieważ klienci muszą mieć możliwość adresowania dokumentu przy użyciu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Jeśli pole źródłowe klucza zawiera adresy URL-niebezpieczne, można użyć funkcji `base64Encode`, aby skonwertować ją podczas indeksowania.

Gdy pobierasz zakodowany klucz w czasie wyszukiwania, możesz użyć funkcji `base64Decode`, aby uzyskać oryginalną wartość klucza, a następnie użyć jej do pobrania dokumentu źródłowego.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Jeśli nie dołączysz właściwości Parameters dla funkcji mapowania, domyślnie wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa różne kodowania base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [Opcje kodowania base64](#base64details) , aby określić parametry do użycia.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode, funkcja

Wykonuje dekodowanie Base64 ciągu wejściowego. Przyjmuje się, że wartość wejściowa jest *bezpiecznym* ciągiem z kodowaniem Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Przykład — dekodowanie metadanych lub adresów URL obiektu BLOB

Dane źródłowe mogą zawierać ciągi kodowane algorytmem Base64, takie jak ciągi metadanych obiektów blob lub adresy URL sieci Web, które mają być przeszukiwane jako zwykły tekst. Można użyć funkcji `base64Decode`, aby przekształcić zakodowane dane z powrotem do zwykłych ciągów podczas wypełniania indeksu wyszukiwania.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Jeśli nie dołączysz właściwości Parameters, domyślnie wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa różne kodowania base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [Opcje kodowania base64](#base64details) , aby określić parametry do użycia.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opcje kodowania base64

Usługa Azure Wyszukiwanie poznawcze obsługuje kodowanie przy użyciu bezpiecznego adresu URL i standardowego kodowania base64. Ciąg szyfrowany algorytmem Base64 podczas indeksowania powinien zostać zdekodowany później przy użyciu tych samych opcji kodowania lub w przeciwnym razie wynik nie będzie zgodny z oryginałem.

Jeśli parametry `useHttpServerUtilityUrlTokenEncode` lub `useHttpServerUtilityUrlTokenDecode` do kodowania i dekodowania odpowiednio są ustawione na `true`, `base64Encode` zachowuje się jak [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i `base64Decode` zachowuje się jak [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Jeśli `base64Encode` jest używany do tworzenia wartości kluczy, `useHttpServerUtilityUrlTokenEncode` musi być ustawiona na wartość true. Dla wartości kluczy można używać tylko bezpiecznego kodowania base64 z adresem URL. Zobacz [reguły &#40;nazewnictwa Azure&#41; wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice/naming-rules) , aby uzyskać pełny zestaw ograniczeń dotyczących znaków w wartościach klucza.

W bibliotekach platformy .NET na platformie Azure Wyszukiwanie poznawcze założono pełny .NET Framework, który zapewnia wbudowane kodowanie. `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` opcje wykorzystują ten wbudowany functionaity. W przypadku korzystania z platformy .NET Core lub innej platformy zalecamy ustawienie tych opcji w celu `false` i wywołania funkcji kodowania i dekodowania struktury przez program.

W poniższej tabeli porównano różne kodowania base64 `00>00?00`. Aby określić wymagane dodatkowe przetwarzanie (jeśli istnieje) dla funkcji Base64, zastosuj funkcję kodowania biblioteki w ciągu `00>00?00` i Porównaj dane wyjściowe z oczekiwanym `MDA-MDA_MDA`wyjściowym.

| Kodowanie | Dane wyjściowe kodowania base64 | Dodatkowe przetwarzanie po kodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowaniem biblioteki |
| --- | --- | --- | --- |
| Base64 z uzupełnieniem | `MDA+MDA/MDA=` | Użyj znaków bezpiecznych dla adresu URL i Usuń uzupełnienie | Użyj standardowych znaków base64 i Dodaj uzupełnienie |
| Bezuzupełnienie Base64 | `MDA+MDA/MDA` | Użyj znaków bezpiecznych dla adresu URL | Używaj standardowych znaków Base64 |
| Bezpieczny algorytm Base64 z użyciem adresu URL | `MDA-MDA_MDA=` | Usuń uzupełnienie | Dodaj uzupełnienie |
| Bezpieczny dla adresu URL algorytm Base64 bez dopełnienia | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition, funkcja

Dzieli pole ciągu przy użyciu określonego ogranicznika i wybiera token w określonym położeniu w wyniku podziału.

Ta funkcja używa następujących parametrów:

* `delimiter`: ciąg, który ma być używany jako separator podczas dzielenia ciągu wejściowego.
* `position`: podzielną liczbę całkowitą od zera pozycji tokenu do wybrania po podpisaniu ciągu wejściowego.

Na przykład, jeśli dane wejściowe są `Jane Doe`, `delimiter` jest `" "`(miejsce), a `position` to 0, wynik jest `Jane`; Jeśli `position` wynosi 1, wynik jest `Doe`. Jeśli pozycja odwołuje się do nieistniejącego tokenu, zwracany jest błąd.

#### <a name="example---extract-a-name"></a>Przykład — Wyodrębnij nazwę

Źródło danych zawiera pole `PersonName` i ma być indeksowane jako dwa osobne `FirstName` i `LastName` pola. Za pomocą tej funkcji można podzielić dane wejściowe za pomocą znaku spacji jako ogranicznika.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection, funkcja

Transformuje ciąg sformatowany jako tablicę JSON ciągów do tablicy ciągów, która może służyć do wypełniania pola `Collection(Edm.String)` w indeksie.

Na przykład jeśli ciąg wejściowy jest `["red", "white", "blue"]`, pole docelowe typu `Collection(Edm.String)` zostanie wypełnione trzema wartościami `red`, `white`i `blue`. W przypadku wartości wejściowych, których nie można analizować jako tablic ciągów JSON, zwracany jest błąd.

#### <a name="example---populate-collection-from-relational-data"></a>Przykład — wypełnienie kolekcji z danych relacyjnych

Azure SQL Database nie ma wbudowanego typu danych, który w sposób naturalny mapuje do `Collection(Edm.String)` pól w usłudze Azure Wyszukiwanie poznawcze. Aby wypełnić pola kolekcji ciągów, można wstępnie przetworzyć dane źródłowe jako tablicę ciągów JSON, a następnie użyć funkcji mapowania `jsonArrayToStringCollection`.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Aby zapoznać się ze szczegółowym przykładem, który przekształca dane relacyjne w pola kolekcji indeksów, zobacz [Modelowanie danych relacyjnych](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode, funkcja

Ta funkcja może służyć do kodowania ciągu w taki sposób, że jest to "bezpieczny adres URL". W przypadku użycia z ciągiem zawierającym znaki, które nie są dozwolone w adresie URL, ta funkcja przekonwertuje te "niebezpieczne" znaków na odpowiedniki jednostek znakowych. Ta funkcja używa formatu kodowania UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie klucza dokumentu

funkcji `urlEncode` można użyć jako alternatywy dla funkcji `base64Encode`, jeśli tylko niebezpieczne znaki adresów URL mają być konwertowane, zachowując inne znaki.

Załóżmy, że ciąg wejściowy jest `<hello>`-a pole docelowe typu `(Edm.String)` zostanie wypełnione wartością `%3chello%3e`

Gdy pobierasz zakodowany klucz w czasie wyszukiwania, możesz użyć funkcji `urlDecode`, aby uzyskać oryginalną wartość klucza, a następnie użyć jej do pobrania dokumentu źródłowego.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode, funkcja

 Ta funkcja konwertuje ciąg zakodowany w adresie URL na zdekodowany ciąg przy użyciu formatu kodowania UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Przykład — dekodowanie metadanych obiektu BLOB

 Niektórzy klienci usługi Azure Storage automatycznie zakodują metadane obiektu BLOB, jeśli zawiera znaki inne niż ASCII. Jeśli jednak chcesz przeszukiwać takie metadane (jako zwykły tekst), możesz użyć funkcji `urlDecode`, aby przekształcić zakodowane dane z powrotem do zwykłych ciągów podczas wypełniania indeksu wyszukiwania.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```