---
title: Mapowania pól dla automatycznego indeksowania, przy użyciu indeksatorów — usługa Azure Search
description: Skonfiguruj mapowania pól indeksator usługi Azure Search na różnice nazw pól i reprezentacji danych.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147791"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapowania pól i przekształcenia przy użyciu indeksatorów usługi Azure Search

Przy użyciu indeksatorów usługi Azure Search, możesz czasami znaleźć to, czy dane wejściowe nie dość zgodny ze schematem indeksu docelowego. W takich przypadkach można użyć **mapowania pól** przekształcenie danych podczas procesu indeksowania.

Sytuacje, gdzie mapowania pól są przydatne:

* Źródło danych zawiera pole o nazwie `_id`, ale usługa Azure Search nie zezwala na nazwy pól, które rozpoczynają się od podkreślenia. Mapowanie pól umożliwia efektywne zmienić nazwę pola.
* Chcesz wypełnić kilka pól w indeksie z tego samego źródła danych. Na przykład możesz zastosować różne analizatory do tych pól.
* Chcesz wypełnić pole indeksu przy użyciu danych z więcej niż jednego źródła danych i źródeł danych używać nazw inne pole.
* Potrzebujesz do formatu Base64 kodowania lub dekodowania danych. Mapowania pól obsługuje kilka **mapowania funkcji**, takie jak funkcje dla Base64, kodowania i dekodowania.

> [!NOTE]
> Funkcja mapowania pól w indeksatorach usługi Azure Search zapewnia prosty sposób mapowania pól danych na pola indeksu, za pomocą kilku opcji do konwersji danych. Bardziej złożonych danych może wymagać przetwarzania wstępnego, aby przekształcić je do formularza, który jest łatwy do indeksu.
>
> Microsoft Azure Data Factory to zaawansowane rozwiązanie oparte na chmurze do importowania i przekształcania danych. Można także napisać kod, aby przetwarzają dane źródłowe przed indeksowania. Aby uzyskać przykłady kodu, zobacz [dane relacyjne modelu](search-example-adventureworks-modeling.md) i [modelu wielopoziomowe aspekty](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Konfigurowanie mapowania pól

Mapowanie pól składa się z trzech części:

1. Element `sourceFieldName`, który reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalny `targetFieldName`, która reprezentuje pola w indeksie wyszukiwania. W przypadku pominięcia jest używana taką samą nazwę jak źródła danych.
3. Opcjonalny `mappingFunction`, które można transformować dane przy użyciu jednej z kilku predefiniowanymi funkcjami. Pełna lista funkcji jest [poniżej](#mappingFunctions).

Mapowania pól są dodawane do `fieldMappings` tablicy definicja indeksatora.

## <a name="map-fields-using-the-rest-api"></a>Mapowanie pól za pomocą interfejsu API REST

Można dodać mapowania pól, podczas tworzenia nowego przy użyciu indeksatora [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) żądania interfejsu API. Możesz zarządzać mapowań pól użycia istniejącego indeksatora [indeksatora aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-indexer) żądania interfejsu API.

Na przykład poniżej przedstawiono sposób mapowania pól źródłowej na pole docelowe z inną nazwą:

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

Wiele mapowań pól można odwoływać się do pola źródłowego. Poniższy przykład pokazuje, jak pola, kopiowanie tego samego pola źródłowego do dwóch pól inny indeks "rozwidlenie":

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Usługa Azure Search używa porównania bez uwzględniania wielkości liter do rozpoznawania nazw pól i funkcji w mapowania pól. Jest to wygodne (nie trzeba uzyskać odpowiednie wielkości liter), ale oznacza to, że źródła danych lub indeks nie może mieć pola, które różnią się tylko wielkością liter.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapowanie pól przy użyciu zestawu .NET SDK

Należy zdefiniować mapowania pól w przy użyciu zestawu .NET SDK [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) klasy, która ma właściwości `SourceFieldName` i `TargetFieldName`oraz opcjonalny `MappingFunction` odwołania.

Mapowania pól można określić podczas tworzenia indeksatora lub później, ustawiając bezpośrednio `Indexer.FieldMappings` właściwości.

Następujące C# przykładzie ustawiono mapowania pól, podczas tworzenia indeksatora.

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

Funkcja mapowania pola przekształca zawartość pola, zanim są przechowywane w indeksie. Obecnie obsługiwane są następujące funkcje mapowania:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode — funkcja

Wykonuje *bezpieczny adres URL* kodowanie Base64 ciągu wejściowego. Przyjęto założenie, że dane wejściowe są zakodowane w formacie UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie klucza dokumentu

Znaki tylko bezpieczny adres URL może znajdować się w kluczu dokumentu usługi Azure Search (ponieważ klienci muszą mieć możliwość rozwiązać używając dokumentu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Jeśli pole źródłowe związane z kluczem zawiera znaki niebezpieczne adresu URL, możesz użyć `base64Encode` funkcję, aby przekonwertować go na indeksowanie czasu.

Po pobraniu zakodowany klucz w czasie wyszukiwania można użyć `base64Decode` funkcji, aby uzyskać oryginalną wartość klucza i używać go do pobrania dokumentu źródłowego.

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

Jeśli nie zawiera właściwości parametrów dla funkcji mapowania, jego wartość domyślna to wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Usługa Azure Search obsługuje dwa różne kodowania Base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [opcji kodowania base64](#base64details) zdecydować, której parametry do użycia.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode — funkcja

Wykonuje Base64 dekodowanie ciągu wejściowego. Dane wejściowe zakłada się, że *bezpieczny adres URL* ciągu zakodowanego algorytmem Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Przykład — dekodowanie metadane obiektu blob lub adresy URL

Źródło danych może zawierać ciągi zakodowane w formacie Base64, takich jak ciągi metadanych obiektu blob lub adresu URL sieci web, które chcą ułatwić wyszukiwanie w postaci zwykłego tekstu. Możesz użyć `base64Decode` funkcję, aby przekształcić dane zakodowane ponownie zwykłe ciągi podczas wypełniania indeksu wyszukiwania.

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

Jeśli nie dodasz Właściwość parameters, jego wartość domyślna to wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Usługa Azure Search obsługuje dwa różne kodowania Base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [opcji kodowania base64](#base64details) zdecydować, której parametry do użycia.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opcje kodowania Base64

Usługa Azure Search obsługuje dwa różne kodowania Base64: **Adres URL HttpServerUtility token**, i **kodowanie Base64 bezpieczny adres URL bez uzupełnień**. Ciąg, który jest kodowany algorytmem Base64 podczas indeksowania później powinien zostać odczytany przy użyciu tych samych opcji kodowania, w przeciwnym razie wynik nie będzie zgodne z oryginalnym.

Jeśli `useHttpServerUtilityUrlTokenEncode` lub `useHttpServerUtilityUrlTokenDecode` parametry w celu kodowania i dekodowania odpowiednio są ustawione na `true`, następnie `base64Encode` zachowuje się jak [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i `base64Decode` zachowuje się jak [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Jeśli nie używasz pełny program .NET Framework (oznacza to, że używasz platformy .NET Core lub innej struktury) do tworzenia wartości klucza w celu emulacji zachowania usługi Azure Search, następnie należy ustawić `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` do `false`. W zależności od używanej biblioteki base64 kodowania i dekodowania funkcje mogą różnić się od nich używane przez usługę Azure Search.

W poniższej tabeli porównano kodowania base64 różnych ciągu `00>00?00`. Aby ustalić wymagane dodatkowego przetwarzania (jeśli istnieje) dla funkcji base64, zastosuj biblioteki kodowanie funkcji w ciągu `00>00?00` i porównywanie danych wyjściowych z oczekiwanych danych wyjściowych `MDA-MDA_MDA`.

| Kodowanie | Base64 — kodowanie danych wyjściowych | Dodatkowe przetwarzanie po zakodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowanie biblioteki |
| --- | --- | --- | --- |
| Base64, za pomocą wypełnienia | `MDA+MDA/MDA=` | Użyj znaków bezpieczny adres URL i Usuń dopełnienie | Użyj standardowych base64 znaków i Dodaj dopełnienie |
| Base64 bez wypełnienia | `MDA+MDA/MDA` | Używaj znaków bezpieczny adres URL | Użyj standardowych base64 znaków |
| Bezpieczny adres URL base64, za pomocą wypełnienia | `MDA-MDA_MDA=` | Usuń dopełnienie | Dodaj dopełnienie |
| Bezpieczny adres URL base64 bez wypełnienia | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition — funkcja

Dzieli się polem ciągu przy użyciu określonego ogranicznika i odbiera token w określonej pozycji w wynikowej podziału.

Ta funkcja wykorzystuje następujące parametry:

* `delimiter`: ciąg wykorzystywany jako separator, w przypadku dzielenia ciągu wejściowego.
* `position`: liczba całkowita liczona od zera pozycja token do pobrania po podzieleniu ciągu wejściowego.

Na przykład, jeśli dane wejściowe są `Jane Doe`, `delimiter` jest `" "`(miejsca) i `position` wynosi 0, wynik jest `Jane`; Jeśli `position` wynosi 1, wynik jest `Doe`. Jeśli pozycja odwołuje się do tokenu, który nie istnieje, zwracany jest błąd.

#### <a name="example---extract-a-name"></a>Przykład — Wyodrębnij nazwę

Źródło danych zawiera `PersonName` pól i chcesz je indeksować jako dwa osobne `FirstName` i `LastName` pola. Funkcja ta jest przydatna do dzielenia danych wejściowych przy użyciu znaku spacji jako ogranicznika.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection — funkcja

Przekształca ciąg w formacie JSON tablicę ciągów w tablicy ciągów, który może służyć do wypełniania `Collection(Edm.String)` pola w indeksie.

Na przykład, jeśli ciąg wejściowy jest `["red", "white", "blue"]`, następnie pola docelowego typu `Collection(Edm.String)` zostaną wypełnione wartościami trzy `red`, `white`, i `blue`. Wprowadzenie wartości, które nie może być analizowana jako tablice w formacie JSON ciąg zwracany jest błąd.

#### <a name="example---populate-collection-from-relational-data"></a>Przykład — wypełnianie kolekcji z danymi relacyjnymi

Usługa Azure SQL Database nie ma typu danych wbudowane, mapująca się naturalnie do `Collection(Edm.String)` pól w usłudze Azure Search. Aby wypełnić pola kolekcji ciągów, można wstępnie przetworzyć dane źródłowe w postaci tablicy ciągów JSON, a następnie użyć `jsonArrayToStringCollection` mapowania funkcji.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Aby uzyskać szczegółowy przykład przekształca opartego na danych relacyjnych w indeks kolekcji pól, zobacz [dane relacyjne modelu](search-example-adventureworks-modeling.md).
