---
title: Wprowadzenie do zapytań SQL w usłudze Azure Cosmos DB
description: Wprowadzenie do zapytań SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342618"
---
# <a name="getting-started-with-sql-queries"></a>Wprowadzenie do zapytań SQL

Konta usługi Azure Cosmos DB — interfejs API SQL obsługuje elementy tworzenie zapytań przy użyciu języka SQL (Structured Query) jako język zapytań JSON. Cele projektu języka zapytań usługi Azure Cosmos DB są:

* Obsługuje język SQL, jednym z najbardziej znanych i popularnych języków zapytanie, zamiast inventing nowego języka zapytań. SQL zapewnia model programowania formalne zaawansowane zapytania przez w formacie JSON.  

* Użyj modelu programowania języka JavaScript jako podstawa dla języka zapytań. System typów języka JavaScript, Obliczanie wyrażenia i wywołania funkcji są korzenie interfejsu API SQL. Te katalogi główne Obejmij naturalnych model programowania funkcji takich jak relacyjne projekcji, nawigacja hierarchiczna w formacie JSON samosprzężenia, zapytań przestrzennych i wywołania funkcji zdefiniowanych przez użytkownika (UDF), napisanych w całości w języku JavaScript.

## <a name="upload-sample-data"></a>Przekazywanie przykładowych danych

Na koncie usługi SQL API Cosmos DB, tworzenia kontenera o nazwie `Families`. Utwórz dwa proste elementy JSON w kontenerze. Może uruchamiać większość przykładowych zapytań w dokumentacji zapytań usługi Azure Cosmos DB przy użyciu tego zestawu danych.

### <a name="create-json-items"></a>Tworzenie elementów JSON

Poniższy kod tworzy dwa proste w formacie JSON dotyczących rodziny. Proste elementów JSON dla rodziny Andersen i Wakefield obejmują elementy nadrzędne, elementy podrzędne i ich zwierząt domowych, adresu i informacji o rejestracji. Pierwszy element zawiera ciągi, liczby, wartości logicznych, tablic i zagnieżdżonych właściwości.


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

Drugi element używa `givenName` i `familyName` zamiast `firstName` i `lastName`.

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

### <a name="query-the-json-items"></a>Zapytania w formacie JSON

Wypróbuj kilka zapytań dotyczących danych JSON, aby poznać niektóre z kluczowych aspektów języka zapytania SQL usługi Azure Cosmos DB.

Następujące zapytanie zwraca elementy gdzie `id` pola dopasowania `AndersenFamily`. Ponieważ jest ono `SELECT *` zapytanie, wyniki kwerendy jest kompletny element JSON. Aby uzyskać więcej informacji na temat składni wybierz zobacz [instrukcji SELECT](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki zapytania są: 

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

Następujące zapytanie formatuje dane wyjściowe JSON do innego kształtu. Zapytanie projektów nowych JSON `Family` obiektu z dwóch wybranych pól, `Name` i `City`, gdy adres, Miasto jest taka sama jak stan. "NY, NY" pasuje do tej sprawy.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Wyniki zapytania są:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Następujące zapytanie zwraca imiona wszystkich dzieci w rodzinie którego `id` odpowiada `WakefieldFamily`, uporządkowanych według miast.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Wyniki są:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Uwagi

W poprzednich przykładach pokazano kilka aspektów języka zapytań usługi Cosmos DB:  

* Ponieważ interfejsu API SQL działa na wartości JSON, zajmuje się on w kształcie drzewo jednostek zamiast wierszy i kolumn. Możesz zapoznać się z węzłami drzewa, na dowolnym poziomie dowolnego, takie jak `Node1.Node2.Node3…..Nodem`, podobnie jak odwołanie legalną dwuczęściową `<table>.<column>` ANSI SQL.

* Ponieważ język zapytań działa z danymi ze schematów, system typu musi być powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynik zapytania jest prawidłową wartością JSON, ale nie jest gwarantowana stałego schematu.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. System typów i wyrażenia są ograniczone do czynienia tylko z typami JSON. Aby uzyskać więcej informacji, zobacz [specyfikacji formatu JSON](https://www.json.org/).  

* Kontener usługi Cosmos DB to kolekcja elementów JSON bez schematu. Relacje wewnątrz i pomiędzy elementami kontenera są przechwytywane niejawnie przez zawierania, nie za pomocą klucza podstawowego i relacje klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrz elementu omówione w dalszej części tego artykułu.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzula SELECT](sql-query-select.md)
