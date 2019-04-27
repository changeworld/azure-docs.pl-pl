---
title: Jak wykonywać zapytania przy użyciu języka SQL w usłudze Azure Cosmos DB?
description: Dowiedz się, jak wykonywać zapytania za pomocą usługi SQL w usłudze Azure Cosmos DB
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 05/10/2017
ms.reviewer: sngun
ms.openlocfilehash: bc9835876e8b87213ddbae65e43222467e751ea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612810"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Samouczek: wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API SQL

[Interfejs API SQL](documentdb-introduction.md) usługi Azure Cosmos DB obsługuje wykonywanie zapytań względem dokumentów przy użyciu języka SQL. Ten artykuł zawiera przykładowy dokument i dwa przykładowe zapytania SQL oraz ich wyniki.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytań o dane za pomocą usługi SQL

## <a name="sample-document"></a>Przykładowy dokument

Zapytania SQL w tym artykule korzystają z następującego przykładowego dokumentu.

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
        "gender": "female", "grade": 1,
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
## <a name="where-can-i-run-sql-queries"></a>Gdzie można uruchomić zapytania SQL?

Zapytania można uruchamiać przy użyciu Eksploratora danych w witrynie Azure Portal, za pomocą [interfejsu API REST i zestawów SDK](sql-api-sdk-dotnet.md), a nawet [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo), który uruchamia zapytania na istniejącym zestawie danych przykładowych.

Aby uzyskać więcej informacji na temat zapytań SQL zobacz:
* [Zapytanie SQL i składnia SQL](how-to-sql-query.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że masz konto usługi Azure Cosmos DB i kolekcję. Nie spełniasz tych warunków? Skorzystaj z [pięciominutowego samouczka Szybki start](create-mongodb-nodejs.md).

## <a name="example-query-1"></a>Przykładowe zapytanie 1

Bazując na powyższym przykładowym dokumencie dotyczącym rodziny, następujące zapytanie SQL zwraca dokumenty, dla których pole id ma wartość `WakefieldFamily`. Ponieważ jest to instrukcja `SELECT *`, dane wyjściowe zapytania są kompletnym dokumentem JSON:

**Zapytanie**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Results**

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
        "gender": "female", "grade": 1,
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

## <a name="example-query-2"></a>Przykładowe zapytanie 2

Następne zapytanie zwraca imiona wszystkich dzieci w rodzinie o identyfikatorze zgodnym z `WakefieldFamily`, uporządkowane według klasy.

**Zapytanie**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Results**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu języka SQL  

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-sql-api.md)

