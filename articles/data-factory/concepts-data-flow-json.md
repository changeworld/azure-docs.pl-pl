---
title: Używanie kodu JSON w mapowaniu przepływu danych w Azure Data Factory
description: Azure Data Factory mapowanie przepływu danych ma wbudowane funkcje obsługi dokumentów JSON z hierarchiami
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 605564ed541c23a9060879706fb25f91e97a8eac
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326568"
---
# <a name="mapping-data-flow-json-handling"></a>Mapowanie obsługi JSON przepływu danych

## <a name="creating-json-structures-in-derived-column"></a>Tworzenie struktur JSON w kolumnie pochodnej

Do przepływu danych można dodać kolumnę złożoną za pośrednictwem konstruktora wyrażeń kolumn pochodnych. W transformację kolumn pochodnych Dodaj nową kolumnę, a następnie otwórz konstruktora wyrażeń, klikając niebieską ramkę. Aby utworzyć kolumnę złożoną, można wprowadzić strukturę JSON ręcznie lub użyć środowiska użytkownika do interaktywnego dodawania podkolumn.

### <a name="using-the-expression-builder-ux"></a>Korzystanie z środowiska programu Expression Builder

W okienku po stronie schematu danych wyjściowych Umieść kursor nad kolumną i kliknij ikonę znaku plus. Wybierz pozycję **Dodaj podkolumnę** , aby utworzyć kolumnę typu złożonego.

Dodaj podkolumnę(media/data-flow/addsubcolumn.png "Dodawanie") ![podkolumny]

W ten sam sposób można dodać dodatkowe kolumny i podkolumny. W przypadku każdego niezłożonej pola wyrażenie może być dodane w edytorze wyrażeń z prawej strony.

Kolumna ![złożona kolumny złożonej](media/data-flow/complexcolumn.png "")

### <a name="entering-the-json-structure-manually"></a>Ręczne wprowadzanie struktury JSON

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

Używanie zestawu danych JSON jako źródła w przepływie danych pozwala na ustawienie pięciu dodatkowych ustawień. Te ustawienia można znaleźć w obszarze **Ustawienia JSON** zgodnie z opisem na karcie **Opcje źródła** .  

![](media/data-flow/json-settings.png "Ustawienia") json dla ustawienia JSON

### <a name="default"></a>Domyślne

Domyślnie dane JSON są odczytywane w następującym formacie.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Pojedynczy dokument

W przypadku wybrania **jednego dokumentu** mapowanie przepływów danych odczytuje jeden dokument JSON z każdego pliku. 

``` json
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

Jeśli wybrano **nazwy kolumn bez cudzysłowów** , mapowanie przepływów danych odczytuje kolumny JSON, które nie są ujęte w cudzysłowy. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Ma Komentarze

Zaznacz pole **ma Komentarze** , jeśli dane JSON mają komentarz C++ C lub style.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Pojedyncze cudzysłowy

Wybierz opcję **pojedyncze cudzysłowy** , jeśli pola i wartości JSON używają apostrofów zamiast podwójnych cudzysłowów.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Odwrócony ukośnik odwrotny

Wybierz opcję **pojedyncze cudzysłowy** , jeśli ukośniki odwrotne są używane do ucieczki znaków w danych JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funkcje wyższego rzędu

Funkcja wyższej kolejności to funkcja, która przyjmuje w jednej lub kilku funkcjach jako argument. Poniżej znajduje się lista funkcji wyższego rzędu obsługiwanych w mapowaniu przepływów danych, które umożliwiają wykonywanie operacji na tablicach.

### <a name="filter"></a>filtru
Filtruje elementy z tablicy, które nie spełniają podanego predykatu. Filtr oczekuje odwołania do jednego elementu w funkcji predykatu jako #item.

#### <a name="examples"></a>Przykłady
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w funkcji Expression jako #item.

#### <a name="examples"></a>Przykłady
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>zmniejszenie
Gromadzi elementy w tablicy. Funkcja ograniczania oczekuje odwołania do akumulowana i jednego elementu w pierwszej funkcji wyrażenia jako #acc i #item i oczekuje wartości, jako #result do użycia w drugiej funkcji wyrażenia.

#### <a name="examples"></a>Przykłady
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>Porządku
Sortuje tablicę przy użyciu podanej funkcji predykatu. Funkcja Sort oczekuje odwołania do dwóch kolejnych elementów w funkcji Expression jako #item1 i #item2.

#### <a name="examples"></a>Przykłady
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>wyświetlana
Zwraca wartość true, jeśli dowolny element w podanej tablicy ma wartość true w podanym predykacie. Zawiera oczekiwane odwołanie do jednego elementu w funkcji predykatu jako #item.

#### <a name="examples"></a>Przykłady
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Następne kroki

* [Używanie transformacji kolumn pochodnych do kompilowania struktur hierarchicznych](data-flow-derived-column.md)
