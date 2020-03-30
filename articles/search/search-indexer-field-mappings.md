---
title: Mapowania pól w indeksatorach
titleSuffix: Azure Cognitive Search
description: Skonfiguruj mapowania pól w indeksatorze, aby uwzględniać różnice w nazwach pól i reprezentacjach danych.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275156"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapowania pól i przekształcenia przy użyciu indeksatorów usługi Azure Cognitive Search

Podczas korzystania z indeksatorów usługi Azure Cognitive Search czasami okaże się, że dane wejściowe nie do końca pasują do schematu indeksu docelowego. W takich przypadkach można użyć **mapowania pól,** aby zmienić kształt danych podczas procesu indeksowania.

Niektóre sytuacje, w których mapowania pól są przydatne:

* Źródło danych ma pole `_id`o nazwie , ale usługa Azure Cognitive Search nie zezwala na nazwy pól rozpoczynające się od podkreślenia. Mapowanie pól umożliwia skuteczną zmianę nazwy pola.
* Chcesz wypełnić kilka pól w indeksie z tych samych danych źródła danych. Na przykład można zastosować różne analizatory do tych pól.
* Chcesz wypełnić pole indeksu danymi z więcej niż jednego źródła danych, a źródła danych używają różnych nazw pól.
* Musisz base64 zakodować lub zdekodować dane. Mapowania pól obsługują kilka **funkcji mapowania,** w tym funkcje kodowania i dekodowania Base64.

> [!NOTE]
> Funkcja mapowania pól indeksatorów usługi Azure Cognitive Search umożliwia mapowanie pól danych do pól indeksowania z kilkoma opcjami konwersji danych. Bardziej złożone dane mogą wymagać wstępnego przetwarzania, aby przekształcić je w formularz, który jest łatwy do indeksowania.
>
> Usługa Microsoft Azure Data Factory to zaawansowane rozwiązanie oparte na chmurze do importowania i przekształcania danych. Można również napisać kod, aby przekształcić dane źródłowe przed indeksowania. W przykładach kodu zobacz [Model danych relacyjnych](search-example-adventureworks-modeling.md) i [Model wielopoziomowe aspekty](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Konfigurowanie mapowań pól

Mapowanie pola składa się z trzech części:

1. A `sourceFieldName`, który reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalny `targetFieldName`, który reprezentuje pole w indeksie wyszukiwania. Jeśli pominięto, używana jest taka sama nazwa jak w źródle danych.
3. Opcjonalny `mappingFunction`, który może przekształcać dane przy użyciu jednej z kilku wstępnie zdefiniowanych funkcji. Pełna lista funkcji znajduje się [poniżej](#mappingFunctions).

Mapowania pól są `fieldMappings` dodawane do tablicy definicji indeksatora.

## <a name="map-fields-using-the-rest-api"></a>Mapowanie pól przy użyciu interfejsu API REST

Mapowania pól można dodawać podczas tworzenia nowego indeksatora przy użyciu żądania interfejsu API [programu Utwórz indeksatora.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Mapowania pól istniejącego indeksatora można zarządzać za pomocą żądania interfejsu API [indeksatora aktualizacji.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Na przykład, oto jak zamapować pole źródłowe na pole docelowe o innej nazwie:

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

Do pola źródłowego można odwoływać się w wielu mapowaniach pól. W poniższym przykładzie pokazano, jak "rozwidlić" pole, kopiując to samo pole źródłowe do dwóch różnych pól indeksu:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Usługa Azure Cognitive Search używa porównania bez uwzględniania wielkości liter w celu rozpoznania nazw pól i funkcji w mapowaniach pól. Jest to wygodne (nie trzeba uzyskać wszystkie wielkości liter w prawo), ale oznacza to, że źródło danych lub indeks nie może mieć pola, które różnią się tylko w zależności od przypadku.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapowanie pól przy użyciu sdk .NET

Mapowania pól można zdefiniować w zestawie SDK .NET przy użyciu `SourceFieldName` `TargetFieldName`klasy [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) która ma właściwości i , oraz odwołanie opcjonalne. `MappingFunction`

Mapowania pól można określić podczas konstruowania indeksatora `Indexer.FieldMappings` lub później, ustawiając bezpośrednio właściwość.

Poniższy przykład języka C# ustawia mapowania pól podczas konstruowania indeksatora.

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

Funkcja mapowania pól przekształca zawartość pola, zanim będzie ono przechowywane w indeksie. Obecnie obsługiwane są następujące funkcje mapowania:

* [base64Encode](#base64EncodeFunction)
* [base64Dekkod](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringKolekcja](#jsonArrayToStringCollectionFunction)
* [Urlencode](#urlEncodeFunction)
* [kod urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode, funkcja

Wykonuje *kodowanie* base64 bezpieczne dla adresów URL ciągu wejściowego. Zakłada, że dane wejściowe jest UTF-8 zakodowane.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie kluczy dokumentu

Tylko znaki bezpieczne dla adresów URL mogą pojawiać się w kluczu dokumentu usługi Azure Cognitive Search (ponieważ klienci muszą mieć możliwość adresowania dokumentu za pomocą [interfejsu API wyszukiwania).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Jeśli pole źródłowe klucza zawiera znaki niebezpieczne dla `base64Encode` adresów URL, można użyć tej funkcji do konwersji w czasie indeksowania. Jednak klucz dokumentu (zarówno przed, jak i po konwersji) nie może być dłuższy niż 1024 znaki.

Podczas pobierania zakodowanego klucza w czasie wyszukiwania, `base64Decode` można następnie użyć funkcji, aby uzyskać oryginalną wartość klucza i użyć go do pobrania dokumentu źródłowego.

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

Jeśli właściwość parametrów dla funkcji mapowania nie zostanie dołączona, wartość domyślna jest wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Usługa Azure Cognitive Search obsługuje dwa różne kodowania Bazy 64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [base64 opcje kodowania,](#base64details) aby zdecydować, które parametry do użycia.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Odkodowanie, funkcja

Wykonuje dekodowanie base64 ciągu wejściowego. Przyjmuje się, że dane wejściowe są ciągiem kodowanym base64 z kodowanym adresem *URL.*

#### <a name="example---decode-blob-metadata-or-urls"></a>Przykład — dekodowanie metadanych obiektów blob lub adresów URL

Dane źródłowe mogą zawierać ciągi kodowane w bazie Base64, takie jak ciągi metadanych obiektów blob lub adresy URL sieci Web, które mają być przeszukiwane jako zwykły tekst. Za pomocą `base64Decode` tej funkcji można przekształcić zakodowane dane z powrotem w zwykłe ciągi podczas wypełniania indeksu wyszukiwania.

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

Jeśli właściwość parametrów nie zostanie dołączona, `{"useHttpServerUtilityUrlTokenEncode" : true}`wartość domyślna jest domyślna dla wartości .

Usługa Azure Cognitive Search obsługuje dwa różne kodowania Bazy 64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [opcje kodowania base64,](#base64details) aby zdecydować, które parametry mają być używane.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opcje kodowania base64

Usługa Azure Cognitive Search obsługuje kodowanie base64 bezpieczne dla adresów URL i normalne kodowanie base64. Ciąg, który jest base64 zakodowane podczas indeksowania powinny być dekodowane później z tych samych opcji kodowania, w przeciwnym razie wynik nie będzie zgodny z oryginałem.

Jeśli `useHttpServerUtilityUrlTokenEncode` parametry `useHttpServerUtilityUrlTokenDecode` lub do `true`kodowania i dekodowania `base64Encode` odpowiednio są ustawione na , a następnie zachowuje `base64Decode` się jak [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i zachowuje się jak [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Jeśli `base64Encode` jest używany do `useHttpServerUtilityUrlTokenEncode` tworzenia wartości klucza, musi być ustawiona na true. Tylko kodowanie base64 bezpieczne dla adresów URL może być używane dla wartości klucza. Zobacz [reguły nazewnictwa &#40;&#41;usługi Azure Cognitive Search,](https://docs.microsoft.com/rest/api/searchservice/naming-rules) aby uzyskać pełny zestaw ograniczeń dotyczących znaków w kluczowych wartościach.

Biblioteki .NET w usłudze Azure Cognitive Search zakładają pełną platformę .NET Framework, która zapewnia wbudowane kodowanie. I `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` opcje wykorzystać tę wbudowaną funkcję. Jeśli używasz .NET Core lub innej struktury, `false` zaleca się ustawienie tych opcji i wywołanie funkcji kodowania i dekodowania struktury bezpośrednio.

W poniższej tabeli porównano różne kodowania `00>00?00`base64 ciągu . Aby określić wymagane dodatkowe przetwarzanie (jeśli istnieje) dla funkcji base64, zastosuj `00>00?00` funkcję kodowania biblioteki `MDA-MDA_MDA`na ciągu i porównaj dane wyjściowe z oczekiwanym wyjściem .

| Kodowanie | Base64 kodowanie danych wyjściowych | Dodatkowe przetwarzanie po kodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowaniem biblioteki |
| --- | --- | --- | --- |
| Base64 z wyściółką | `MDA+MDA/MDA=` | Używanie znaków bezpiecznych dla adresów URL i usuwanie dopełnienie | Użyj standardowych znaków base64 i dodaj dopełnienie |
| Base64 bez dopełnienie | `MDA+MDA/MDA` | Używanie znaków bezpiecznych dla adresów URL | Używanie standardowych znaków base64 |
| Bazabezsy url64 z dopełniewą | `MDA-MDA_MDA=` | Usuń dopełnienie | Dodawanie dopełnienie |
| Bazabezsy url64 bez dopełnienie | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition, funkcja

Dzieli pole ciągu przy użyciu określonego ogranicznika i wybiera token w określonej pozycji w wynikowym podziale.

Ta funkcja używa następujących parametrów:

* `delimiter`: ciąg używany jako separator podczas dzielenia ciągu wejściowego.
* `position`: całkowita pozycja oparta na wartości zerowej tokenu do pobrania po podziale ciągu wejściowego.

Na przykład, jeśli `Jane Doe`dane `delimiter` wejściowe to , is `" "`(spacja) i `position` jest 0, wynik jest; `Jane` jeśli `position` jest 1, wynik `Doe`jest . Jeśli pozycja odwołuje się do tokenu, który nie istnieje, zwracany jest błąd.

#### <a name="example---extract-a-name"></a>Przykład - wyodrębnić nazwę

Źródło danych zawiera `PersonName` pole i chcesz go indeksować `FirstName` `LastName` jako dwa oddzielne i pola. Za pomocą tej funkcji można podzielić dane wejściowe przy użyciu znaku spacji jako ogranicznika.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringKoniktowanie, funkcja

Przekształca ciąg sformatowany jako tablica ciągów JSON w tablicę ciągów, która może służyć do wypełniania `Collection(Edm.String)` pola w indeksie.

Na przykład, jeśli ciąg `["red", "white", "blue"]`wejściowy jest , `Collection(Edm.String)` to pole docelowe `red` `white`typu `blue`zostanie wypełnione trzema wartościami , i . Dla wartości wejściowych, które nie mogą być analizowane jako tablice ciągów JSON, zwracany jest błąd.

#### <a name="example---populate-collection-from-relational-data"></a>Przykład — wypełnianie kolekcji z danych relacyjnych

Usługa Azure SQL Database nie ma wbudowanego typu `Collection(Edm.String)` danych, który naturalnie mapuje do pól w usłudze Azure Cognitive Search. Aby wypełnić pola kolekcji ciągów, można wstępnie przetworzyć dane źródłowe `jsonArrayToStringCollection` jako tablicę ciągu JSON, a następnie użyć funkcji mapowania.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Szczegółowy przykład przekształcania danych relacyjnych w pola zbierania indeksów można znaleźć w obszarze [Modelowanie danych relacyjnych](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode, funkcja

Ta funkcja może służyć do kodowania ciągu tak, aby był "url bezpieczne". W przypadku użycia z ciągiem, który zawiera znaki, które nie są dozwolone w adresie URL, ta funkcja konwertuje te "niebezpieczne" znaki na odpowiedniki jednostki znaków. Ta funkcja używa formatu kodowania UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie kluczy dokumentu

`urlEncode`funkcja może być używana jako `base64Encode` alternatywa dla funkcji, jeśli tylko znaki niebezpieczne adres URL mają być konwertowane, przy jednoczesnym zachowaniu innych znaków w stanie, w jakim jest.

Powiedzmy, że `<hello>` ciąg wejściowy jest `(Edm.String)` - wtedy pole docelowe typu zostanie wypełnione wartością`%3chello%3e`

Podczas pobierania zakodowanego klucza w czasie wyszukiwania, `urlDecode` można następnie użyć funkcji, aby uzyskać oryginalną wartość klucza i użyć go do pobrania dokumentu źródłowego.

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

 ### <a name="example---decode-blob-metadata"></a>Przykład - dekodowanie metadanych obiektów blob

 Niektórzy klienci usługi Azure storage automatycznie kodują metadane obiektów blob, jeśli zawierają znaki inne niż ASCII. Jednak jeśli chcesz, aby takie metadane można przeszukiwać (jako zwykły tekst), można użyć `urlDecode` funkcji, aby włączyć zakodowane dane z powrotem do regularnych ciągów podczas wypełniania indeksu wyszukiwania.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>stała FunkcjaLengthEncode
 
 Ta funkcja konwertuje ciąg o dowolnej długości na ciąg o stałej długości.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Przykład — klucze dokumentu mapy, które są zbyt długie
 
W przypadku wystąpienia błędów wskazujących na to, że klucz dokumentu jest dłuższy niż 1024 znaki, tę funkcję można zastosować w celu skrócenia długości klucza dokumentu.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
