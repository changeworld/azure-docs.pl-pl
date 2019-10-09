---
title: Azure Data Factory mapowanie pojęć JSON przepływu danych
description: Data Factory mapowanie przepływu danych ma wbudowane funkcje obsługi dokumentów JSON z hierarchiami
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 37db3e153e8dfcbc1120fcb1f6d2f77187edc78e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029669"
---
# <a name="mapping-data-flow-json-handling"></a>Mapowanie obsługi JSON przepływu danych



## <a name="creating-json-structures-in-expression-editor"></a>Tworzenie struktur JSON w edytorze wyrażeń
### <a name="derived-column-transformation"></a>Transformacja kolumn pochodnych
Dodawanie złożonej kolumny do przepływu danych jest łatwiejsze za pomocą edytora wyrażeń kolumn pochodnych. Po dodaniu nowej kolumny i otwarciu edytora dostępne są dwie opcje: wprowadź ręcznie strukturę JSON lub użyj interfejsu użytkownika, aby interaktywnie dodać podkolumny.

#### <a name="interactive-ui-json-design"></a>Interaktywny projekt JSON interfejsu użytkownika
W okienku po stronie danych wyjściowych można dodawać nowe podkolumny, używając menu `+`: ![Dodaj]podkolumnę(media/data-flow/addsubcolumn.png "Dodaj") podkolumnę

Z tego miejsca można dodawać nowe kolumny i podkolumny w taki sam sposób. W przypadku każdego niezłożonej pola wyrażenie może być dodane w edytorze wyrażeń z prawej strony.

Kolumna ![złożona kolumny złożonej](media/data-flow/complexcolumn.png "")

#### <a name="manual-json-design"></a>Ręczny projekt JSON
Aby ręcznie dodać strukturę JSON, Dodaj nową kolumnę i wprowadź wyrażenie w edytorze. Wyrażenie jest zgodne z następującym formatem ogólnym:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Jeśli to wyrażenie zostało wprowadzone dla kolumny o nazwie "complexColumn", zostanie ona zapisywana w ujścia jako następujący kod JSON:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Przykładowy skrypt ręczny dla pełnej definicji hierarchicznej
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Opcje formatu źródła
### <a name="default"></a>Domyślne
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Pojedynczy dokument
* Opcja jeden
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Opcja dwie
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Nazwy kolumn bez cytowania
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Ma Komentarze
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Pojedyncze cudzysłowy
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Odwrócony ukośnik odwrotny
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funkcje wyższego rzędu
## <a name="filter"></a>filtru
Filtruje elementy z tablicy, które nie spełniają podanego predykatu. Filtr oczekuje odwołania do jednego elementu w funkcji predykatu jako #item.

### <a name="examples"></a>Przykłady
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w funkcji Expression jako #item.

### <a name="examples"></a>Przykłady
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>zmniejszenie
Gromadzi elementy w tablicy. Funkcja ograniczania oczekuje odwołania do akumulowana i jednego elementu w pierwszej funkcji wyrażenia jako #acc i #item i oczekuje wartości, jako #result do użycia w drugiej funkcji wyrażenia.

### <a name="examples"></a>Przykłady
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>Porządku
Sortuje tablicę przy użyciu podanej funkcji predykatu. Funkcja Sort oczekuje odwołania do dwóch kolejnych elementów w funkcji Expression jako #item1 i #item2.

### <a name="examples"></a>Przykłady
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>wyświetlana
Zwraca wartość true, jeśli dowolny element w podanej tablicy ma wartość true w podanym predykacie. Zawiera oczekiwane odwołanie do jednego elementu w funkcji predykatu jako #item.

### <a name="examples"></a>Przykłady
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Następne kroki

* [Używanie transformacji kolumn pochodnych do kompilowania struktur hierarchicznych](data-flow-derived-column.md)
