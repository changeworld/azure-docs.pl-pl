---
title: Wprowadzenie do zapytań SQL w usłudze Azure Cosmos DB
description: Dowiedz się, jak używać zapytań SQL do wykonywania zapytań o dane z usługi Azure Cosmos DB. Możesz przekazać przykładowe dane do kontenera w usłudze Azure Cosmos DB i zbadać je.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873339"
---
# <a name="getting-started-with-sql-queries"></a>Wprowadzenie do zapytań SQL

Konta interfejsu API usługi Azure Cosmos DB SQL obsługują kwerendy elementów przy użyciu języka sql (Structured Query Language) jako języka zapytań JSON. Cele projektowe języka zapytań usługi Azure Cosmos DB mają na celu:

* Obsługa języka SQL, jednego z najbardziej znanych i popularnych języków zapytań, zamiast wymyślania nowego języka zapytań. SQL zapewnia formalny model programowania dla zapytań rozszerzonych za pośrednictwem elementów JSON.  

* Użyj modelu programowania JavaScript jako podstawy dla języka zapytań. System typu JavaScript, ocena wyrażenia i wywołanie funkcji są katalogami głównych interfejsu API SQL. Te katalogi główne zapewniają naturalny model programowania dla funkcji, takich jak projekcje relacyjne, hierarchiczna nawigacja między elementami JSON, sprzężenia samosieci, zapytania przestrzenne i wywołanie funkcji zdefiniowanych przez użytkownika (UDF) napisanych w całości w języku JavaScript.

## <a name="upload-sample-data"></a>Przekazywanie przykładowych danych

Na koncie usługi SQL API Cosmos `Families`DB utwórz kontener o nazwie . Utwórz dwa proste elementy JSON w kontenerze. Można uruchomić większość przykładowych zapytań w docs kwerendy usługi Azure Cosmos DB przy użyciu tego zestawu danych.

### <a name="create-json-items"></a>Tworzenie elementów JSON

Poniższy kod tworzy dwa proste elementy JSON o rodzinach. Proste elementy JSON dla rodzin Andersen i Wakefield obejmują rodziców, dzieci i ich zwierzęta domowe, adres i informacje rejestracyjne. Pierwszy element ma ciągi, liczby, wartości logiczne, tablice i właściwości zagnieżdżone.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Drugi element używa `givenName` `familyName` i zamiast `firstName` `lastName`i .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Kwerenda elementów JSON

Wypróbuj kilka zapytań dotyczących danych JSON, aby zrozumieć niektóre z kluczowych aspektów języka zapytań SQL usługi Azure Cosmos DB.

Poniższa kwerenda zwraca `id` elementy, `AndersenFamily`w których pole jest zgodne . Ponieważ jest to `SELECT *` kwerenda, dane wyjściowe kwerendy jest kompletnym elementem JSON. Aby uzyskać więcej informacji na temat składni SELECT, zobacz [instrukcja SELECT](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki kwerendy są następujące: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Następujące zapytanie formatuje dane wyjściowe JSON do innego kształtu. Kwerenda projektuje nowy `Family` obiekt JSON `Name` z `City`dwoma zaznaczonymi polami i , gdy miasto adresu jest takie samo jak stan. "NY, NY" pasuje do tej sprawy.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Wyniki kwerendy są następujące:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Poniższa kwerenda zwraca wszystkie imiona dzieci `id` w `WakefieldFamily`rodzinie, których dopasowania , uporządkowane według miasta.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Wyniki są następujące:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Uwagi

W poprzednich przykładach pokazano kilka aspektów języka zapytań usługi Cosmos DB:  

* Ponieważ sql api działa na wartości JSON, zajmuje się jednostkami w kształcie drzewa zamiast wierszy i kolumn. Można odwołać się do węzłów drzewa na `Node1.Node2.Node3…..Nodem`dowolnej dowolnej głębokości, jak `<table>.<column>` , podobnie jak odwołanie dwuczęściowe w ANSI SQL.

* Ponieważ język kwerendy działa z danymi bez schematu, system typów musi być powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynik kwerendy jest prawidłową wartością JSON, ale nie jest gwarantowana być schematu stałego.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. System typów i wyrażenia są ograniczone do czynienia tylko z typami JSON. Aby uzyskać więcej informacji, zobacz [specyfikację JSON](https://www.json.org/).  

* Kontener usługi Cosmos jest kolekcją wolnego od schematu elementów JSON. Relacje wewnątrz i między elementami kontenera są niejawnie przechwytywane przez hermetyzacja, a nie przez klucz podstawowy i relacje klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrzunijnych omówionych w dalszej części tego artykułu.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula SELECT](sql-query-select.md)
