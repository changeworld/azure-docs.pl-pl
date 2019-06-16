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
ms.openlocfilehash: d3e0d876550b0c3baf89f3f13e0458fc97e11351
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025216"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapowania pól w indeksatorach usługi Azure Search
Przy użyciu indeksatorów usługi Azure Search, może co pewien czas zaczniesz w sytuacjach, w którym dane wejściowe nie dość zgodny ze schematem indeksu docelowego. W takich przypadkach można użyć **mapowania pól** przekształcić dane w żądany kształt.

Sytuacje, gdzie mapowania pól są przydatne:

* Źródło danych zawiera pole `_id`, ale usługa Azure Search nie zezwala na nazwy pól rozpoczynające się od podkreślenia. Mapowanie pól umożliwia "Zmień nazwę" polem.
* Chcesz wypełnić kilka pól w indeksie przy użyciu tych samych danych źródła danych, na przykład, aby można było zastosować różne analizatory do tych pól. Mapowania pól umożliwiają "rozwidlenie" pole źródła danych.
* Potrzebujesz do formatu Base64 kodowania lub dekodowania danych. Mapowania pól obsługuje kilka **mapowania funkcji**, takie jak funkcje dla Base64, kodowania i dekodowania.   

## <a name="setting-up-field-mappings"></a>Konfigurowanie mapowania pól
Można dodać mapowania pól, podczas tworzenia nowego przy użyciu indeksatora [tworzenie indeksatora](https://msdn.microsoft.com/library/azure/dn946899.aspx) interfejsu API. Możesz zarządzać mapowań pól przy użyciu indeksatora w indeksowania [indeksatora aktualizacji](https://msdn.microsoft.com/library/azure/dn946892.aspx) interfejsu API.

Mapowanie pól składa się z trzech części:

1. Element `sourceFieldName`, który reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalny `targetFieldName`, która reprezentuje pola w indeksie wyszukiwania. W przypadku pominięcia jest używana taką samą nazwę jak źródła danych.
3. Opcjonalny `mappingFunction`, które można transformować dane przy użyciu jednej z kilku predefiniowanymi funkcjami. Pełna lista funkcji jest [poniżej](#mappingFunctions).

Mapowania pól są dodawane do `fieldMappings` tablicy na definicja indeksatora.

Na przykład Oto jak można uwzględnienia różnic między nazwami pól:

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

Indeksator może mieć wiele mapowań pól. Na przykład Oto jak możesz mogą "rozwidlenie" pola:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkcje mapowania pól
Te funkcje są obecnie obsługiwane:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Wykonuje *bezpieczny adres URL* kodowanie Base64 ciągu wejściowego. Przyjęto założenie, że dane wejściowe są zakodowane w formacie UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Przykładowe przypadek użycia — wyszukiwanie klucza dokumentu
Znaki tylko bezpieczny adres URL może znajdować się w kluczu dokumentu usługi Azure Search (ponieważ klienci muszą mieć możliwość rozwiązać używając dokumentu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document), na przykład). Jeśli dane zawierają adres URL niebezpiecznych znaków i chcesz z niej korzystać, aby wypełnić pola klucza w indeksie wyszukiwania, należy użyć tej funkcji. Gdy klucz jest zakodowany, możesz użyć base64 — dekodowanie można pobrać oryginalnej wartości. Aby uzyskać więcej informacji, zobacz [base64 kodowania i dekodowania](#base64details) sekcji.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Przykładowy przypadek użycia — pobieranie oryginalnego klucza
Masz indeksatora obiektów blob, który indeksów obiektów blob za pomocą metadanych ścieżka obiektu blob jako klucz dokumentu. Po pobraniu klucza zakodowanego dokumentu, chcesz dekodowania ścieżkę i pobrać obiekt blob.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Jeśli nie ma potrzeby wyszukania dokumentów według kluczy, a także nie należy do zdekodowania zakodowany zawartości, można po prostu pozostawisz `parameters` mapowanie funkcji, które są domyślnie `useHttpServerUtilityUrlTokenEncode` do `true`. W przeciwnym razie zobacz [szczegóły base64](#base64details) sekcji, aby zdecydować, które ustawienia do użycia.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Wykonuje Base64 dekodowanie ciągu wejściowego. Dane wejściowe przyjmuje się, że *bezpieczny adres URL* ciągu zakodowanego algorytmem Base64.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Wartości niestandardowe metadane obiektu blob musi być zakodowany w formacie ASCII. Kodowanie Base64 służy do reprezentowania dowolne ciągi UTF-8 w niestandardowych metadanych obiektu blob. Jednak aby wyszukiwanie istotnych, służy tę funkcję, aby przekształcać dane zakodowane powrót do ciągów "regularne" podczas wypełniania indeksu wyszukiwania.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Jeśli nie określisz dowolny `parameters`, następnie wartość domyślną `useHttpServerUtilityUrlTokenDecode` jest `true`. Zobacz [szczegóły base64](#base64details) sekcji, aby zdecydować, które ustawienia do użycia.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Szczegóły base64, kodowania i dekodowania
Usługa Azure Search obsługuje dwa kodowania base64: Adres URL HttpServerUtility token i bezpieczny adres URL bez uzupełnień kodowania base64. Należy użyć z tym samym kodowaniem jako funkcji mapowania, aby kodowanie klucz dokumentu, aby się, Koduj wartość ma być zdekodowany, indeksator lub dekodowania pola zakodowany przez indeksator.

Jeśli `useHttpServerUtilityUrlTokenEncode` lub `useHttpServerUtilityUrlTokenDecode` parametry w celu kodowania i dekodowania odpowiednio są ustawione na `true`, następnie `base64Encode` zachowuje się jak [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i `base64Decode` zachowuje się jak [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Jeśli nie używasz pełny program .NET Framework (czyli używasz platformy .NET Core lub inne środowisko programistyczne) do tworzenia wartości klucza w celu emulacji zachowania usługi Azure Search, następnie należy ustawić `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` do `false`. W zależności od biblioteki, możesz użyć, base64 kodowania i dekodowania narzędzie funkcje mogą różnić się od usługi Azure Search.

W poniższej tabeli porównano kodowania base64 różnych ciągu `00>00?00`. Aby ustalić wymagane dodatkowego przetwarzania (jeśli istnieje) dla funkcji base64, zastosuj biblioteki kodowanie funkcji w ciągu `00>00?00` i porównywanie danych wyjściowych z oczekiwanych danych wyjściowych `MDA-MDA_MDA`.

| Kodowanie | Base64 — kodowanie danych wyjściowych | Dodatkowe przetwarzanie po zakodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowanie biblioteki |
| --- | --- | --- | --- |
| Base64, za pomocą wypełnienia | `MDA+MDA/MDA=` | Użyj znaków bezpieczny adres URL i Usuń dopełnienie | Użyj standardowych base64 znaków i Dodaj dopełnienie |
| Base64 bez wypełnienia | `MDA+MDA/MDA` | Używaj znaków bezpieczny adres URL | Użyj standardowych base64 znaków |
| Bezpieczny adres URL base64, za pomocą wypełnienia | `MDA-MDA_MDA=` | Usuń dopełnienie | Dodaj dopełnienie |
| Bezpieczny adres URL base64 bez wypełnienia | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Dzieli się polem ciągu przy użyciu określonego ogranicznika i odbiera token w określonej pozycji w wynikowej podziału.

Na przykład, jeśli dane wejściowe są `Jane Doe`, `delimiter` jest `" "`(miejsca) i `position` wynosi 0, wynik jest `Jane`; Jeśli `position` wynosi 1, wynik jest `Doe`. Jeśli pozycja odwołuje się do tokenu, który nie istnieje, zwracany jest błąd.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Źródło danych zawiera `PersonName` pól i chcesz je indeksować jako dwa osobne `FirstName` i `LastName` pola. Funkcja ta jest przydatna do dzielenia danych wejściowych przy użyciu znaku spacji jako ogranicznika.

### <a name="parameters"></a>Parametry
* `delimiter`: ciąg wykorzystywany jako separator, w przypadku dzielenia ciągu wejściowego.
* `position`: liczba całkowita liczona od zera pozycja token do pobrania po podzieleniu ciągu wejściowego.    

### <a name="example"></a>Przykład
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Przekształca ciąg w formacie JSON tablicę ciągów w tablicy ciągów, który może służyć do wypełniania `Collection(Edm.String)` pola w indeksie.

Na przykład, jeśli ciąg wejściowy jest `["red", "white", "blue"]`, następnie pola docelowego typu `Collection(Edm.String)` zostaną wypełnione wartościami trzy `red`, `white`, i `blue`. Wprowadzenie wartości, które nie może być analizowana jako tablice w formacie JSON ciąg zwracany jest błąd.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Usługa Azure SQL database nie ma typu danych wbudowane, mapująca się naturalnie do `Collection(Edm.String)` pól w usłudze Azure Search. Aby wypełnić pola kolekcji ciągów, formatu danych źródłowych w postaci tablicy ciągów JSON, a następnie użyć tej funkcji.

### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Pomóż nam ulepszyć usługę Azure Search
Jeśli masz sugestie funkcji lub pomysły dotyczące ulepszeń, skontaktuj się z nami na naszych [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
