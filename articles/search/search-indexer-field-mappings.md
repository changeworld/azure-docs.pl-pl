---
title: Mapowania pól w indeksatory usługi Azure Search
description: Konfigurowanie usługi Azure Search mapowań pól indeksatora na różnice w nazwy pól i reprezentacji danych
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: 041866cd1c290bc576577771abcae31db747095e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796856"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapowania pól w indeksatory usługi Azure Search
Podczas korzystania z usługi Azure Search indeksatorów, czasami znajduje się samodzielnie w sytuacji, gdy dane wejściowe dość nie pasuje do schematu indeksu docelowego. W takich przypadkach można użyć **mapowań pól** do przekształcenia danych do żądanego kształtu.

Niektóre gdzie mapowania pól są przydatne w sytuacjach:

* Źródło danych ma pole `_id`, ale usługi Azure Search nie zezwala na nazwy pola, począwszy od znaku podkreślenia. Mapowanie pól umożliwia "rename" pola.
* Należy wypełnić kilka pól w indeksie z tego samego źródła danych, na przykład, ponieważ chcesz zastosować różne analizatorów do tych pól. Pole — mapowanie umożliwiają "rozwidlania" pola źródła danych.
* W formacie Base64 kodowania lub dekodowania danych. Pole — mapowanie obsługuje kilka **mapowania funkcji**, włączając funkcje dla Base64, kodowania i dekodowania.   

## <a name="setting-up-field-mappings"></a>Konfigurowanie mapowania pól
Można dodać mapowania pól, podczas tworzenia nowego przy użyciu indeksatora [Utwórz indeksator](https://msdn.microsoft.com/library/azure/dn946899.aspx) interfejsu API. Możesz zarządzać mapowań pól przy użyciu indeksowania indeksatora [indeksatora aktualizacji](https://msdn.microsoft.com/library/azure/dn946892.aspx) interfejsu API.

Mapowanie pól składa się z trzech części:

1. A `sourceFieldName`, który reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalny `targetFieldName`, który reprezentuje pole w indeksie wyszukiwania. Pominięcie taką samą nazwę jak źródło danych jest używany.
3. Opcjonalny `mappingFunction`, które można przekształcać dane przy użyciu jednej z kilku wstępnie zdefiniowane funkcje. Pełną listę funkcji jest [poniżej](#mappingFunctions).

Mapowania pól są dodawane do `fieldMappings` tablicy w definicji indeksatora.

Na przykład Oto jak można uwzględnić różnice w nazwach pól:

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

Indeksator może mieć wiele mapowań pól. Na przykład, w tym jak użytkownik może "rozwidlania" pola:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Usługa Azure Search używa porównania bez uwzględniania wielkości liter do rozpoznania nazwy pól i funkcji w mapowania pól. Jest to wygodny (nie trzeba uzyskać prawo o wielkości liter), ale oznacza to, że źródła danych lub indeks nie może mieć pól, które różnią się tylko wielkością liter.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkcje mapowania pól
Obecnie obsługiwane są następujące funkcje:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Wykonuje *bezpieczny adres URL* ciągu wejściowego kodowania Base64. Zakłada, że dane wejściowe zakodowane w formacie UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Przypadek użycia próbki — wyszukiwanie klucza dokumentu
Znaki tylko bezpieczny adres URL może występować w kluczu dokumentu usługi Azure Search (ponieważ klienci muszą mieć możliwość adresów w dokumencie przy użyciu [wyszukiwania API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), na przykład). Jeśli dane zawierają znaki niezabezpieczony adres URL i ma być używany do wypełnienia pola klucza w indeksie wyszukiwania, użyj tej funkcji. Po klucz jest zakodowany, możesz użyć base64 dekodowania pobrać oryginalnej wartości. Aby uzyskać więcej informacji, zobacz [base64 kodowania i dekodowania](#base64details) sekcji.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Przykładowe przypadek użycia — pobrać oryginalnego klucza
Masz indeksatora obiektów blob, który indeksy obiektów blob z metadanymi ścieżki obiektu blob jako klucz dokumentu. Po pobraniu klucz dokumentu zakodowany, chcesz dekodowania ścieżkę i pobieranie obiektu blob.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Jeśli nie ma potrzeby wyszukania dokumentów według kluczy i również nie muszą zdekodować zakodowanego zawartości, można po prostu pozostawić `parameters` mapowanie funkcji, które domyślnie `useHttpServerUtilityUrlTokenEncode` do `true`. W przeciwnym razie zobacz [szczegóły base64](#base64details) sekcji, aby zdecydować, które ustawienia do użycia.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Wykonuje Base64 dekodowania ciągu wejściowego. Dane wejściowe przyjmuje się, że *bezpieczny adres URL* ciąg kodowany w formacie Base64.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Wartości niestandardowe metadane obiektu blob musi być zakodowany w formacie ASCII. Za pomocą kodowania Base64 do reprezentowania dowolne ciągi UTF-8 w niestandardowych metadanych obiektu blob. Jednak aby wyszukiwania łatwy do rozpoznania, korzystając tej funkcji można cofnąć dane zakodowane do ciągów "regularne" podczas wypełniania indeksu wyszukiwania.

#### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Jeśli nie określisz żadnego `parameters`, następnie wartość domyślną `useHttpServerUtilityUrlTokenDecode` jest `true`. Zobacz [szczegóły base64](#base64details) sekcji, aby zdecydować, które ustawienia do użycia.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Szczegóły base64 kodowania i dekodowania
Usługa wyszukiwanie Azure obsługuje dwa kodowania base64: adres URL HttpServerUtility kodowania base64 tokenu i bezpieczny adres URL bez dopełnienia. Należy użyć tego samego kodowanie jako funkcje mapowania, aby zakodować klucz dokumentu znaleźć się, Koduj wartość ma być zdekodowany przez indeksatora lub dekodowania pola kodowane przez indeksatora.

Jeśli używasz programu .NET Framework, można ustawić `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` do `true`, kodowania i dekodowania odpowiednio. Następnie `base64Encode` zachowuje się jak [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i `base64Decode` zachowuje się jak [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Jeśli nie używasz programu .NET Framework, a następnie należy ustawić `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` do `false`. W zależności od biblioteki używasz, base64 kodowania i dekodowania narzędzie funkcje mogą różnić się od usługi Azure Search.

W poniższej tabeli porównano kodowania base64 różnych ciągu `00>00?00`. Aby ustalić wymagane dodatkowego przetwarzania (jeśli istnieją) dla funkcji base64, zastosuj biblioteki kodowania funkcji na ciąg `00>00?00` i porównywanie danych wyjściowych z oczekiwane dane wyjściowe `MDA-MDA_MDA`.

| Encoding | Dane wyjściowe kodowania Base64 | Dodatkowe przetwarzanie po kodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowania biblioteki |
| --- | --- | --- | --- |
| Base64 z dopełnienia | `MDA+MDA/MDA=` | Znaki bezpieczny adres URL, a następnie usuń dopełnienia | Znaki standardowe base64, a następnie dodaj dopełnienia |
| Base64 bez dopełnienia | `MDA+MDA/MDA` | Znaki bezpiecznego adresu URL | Użyj standardowych base64 znaków |
| Bezpieczny adres URL base64 z dopełnienia | `MDA-MDA_MDA=` | Usuń dopełnienia | Dodaj dopełnienia |
| Bezpieczny adres URL base64 bez dopełnienia | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Dzieli polem ciągu przy użyciu określonego ogranicznika i odbiera token w określonej pozycji w wynikowej podziału.

Na przykład, jeśli dane wejściowe są `Jane Doe`, `delimiter` jest `" "`(miejsca) i `position` wynosi 0, wynik `Jane`; Jeśli `position` 1, wynikiem jest `Doe`. Jeśli pozycja odwołuje się do tokenu, który nie istnieje, zwracany jest błąd.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Źródło danych zawiera `PersonName` pola i chcesz indeksu ją jako dwa osobne `FirstName` i `LastName` pól. Funkcja ta jest przydatna do dzielenia przy użyciu znaku spacji, jak ogranicznik danych wejściowych.

### <a name="parameters"></a>Parametry
* `delimiter`: ciąg wykorzystywany jako separator w przypadku dzielenia ciągu wejściowego.
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
Przekształca ciąg w formacie JSON tablicę ciągów w tablicy ciągów, który może służyć do wypełnienia `Collection(Edm.String)` pola w indeksie.

Na przykład, jeśli ciąg wejściowy jest `["red", "white", "blue"]`, następnie pola docelowego typu `Collection(Edm.String)` zostaną wypełnione wartościami trzy `red`, `white`, i `blue`. Wartości wejściowe, które nie może być analizowana jako tablice ciągu JSON zwracany jest błąd.

### <a name="sample-use-case"></a>Przypadek użycia próbki
Baza danych SQL Azure nie ma typu danych wbudowanych, mapująca się naturalnie do `Collection(Edm.String)` pól w usłudze Azure Search. Aby wypełnić pola kolekcji ciągów, format danych źródłowych w postaci tablicy ciągów JSON i użyć tej funkcji.

### <a name="example"></a>Przykład
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszenia należy dotrzeć do nas w naszej [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
