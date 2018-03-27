---
title: 'Azure Cosmos DB: Jak wykonywać zapytania za pomocą interfejsu MongoDB API? | Microsoft Docs'
description: Dowiedz się, jak za pomocą interfejsu MongoDB API wykonywać zapytania w usłudze Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/16/2018
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 7c51a2a1cace89305b971d5fb0f56c360cbf93cb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Samouczek: Wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu MongoDB API

Interfejs [API dla bazy danych MongoDB](mongodb-introduction.md) w usłudze Azure Cosmos DB obsługuje [zapytania powłoki MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytania o dane za pomocą bazy danych MongoDB

## <a name="sample-document"></a>Przykładowy dokument

Zapytania w tym artykule korzystają z następującego przykładowego dokumentu.

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
## <a id="examplequery1"></a> Przykładowe zapytanie 1 

Bazując na powyższym przykładowym dokumencie dotyczącym rodziny, następujące zapytanie zwraca dokumenty, dla których pole id ma wartość `WakefieldFamily`.

**Zapytanie**
    
    db.families.find({ id: “WakefieldFamily”})

**Wyniki**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
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
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a> Przykładowe zapytanie 2 

Następne zapytanie zwraca wszystkie dzieci w rodzinie. 

**Zapytanie**
    
    db.families.find( { id: “WakefieldFamily” }, { children: true } )

**Wyniki**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
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
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a> Przykładowe zapytanie 3 

Następne zapytanie zwraca wszystkie zarejestrowane rodziny. 

**Zapytanie**
    
    db.families.find( { "isRegistered" : true })
**Wyniki** Nie zostanie zwrócony żaden dokument. 

## <a id="examplequery4"></a> Przykładowe zapytanie 4

Następne zapytanie zwraca wszystkie rodziny, które nie zostały zarejestrowane. 

**Zapytanie**
    
    db.families.find( { "isRegistered" : false })
**Wyniki**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a> Przykładowe zapytanie 5

Następne zapytanie zwraca wszystkie rodziny, które nie są zarejestrowane i dla których stan to NY. 

**Zapytanie**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Wyniki**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a> Przykładowe zapytanie 6

Następne zapytanie zwraca wszystkie rodziny, w których dzieci chodzą do 8 klasy.

**Zapytanie**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Wyniki**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a> Przykładowe zapytanie 7

Następne zapytanie zwraca wszystkie rodziny, w których rozmiar tablicy z dziećmi to 3.

**Zapytanie**
  
      db.Family.find( {children: { $size:3} } )

**Wyniki**

Nie zostaną zwrócone żadne wyniki, ponieważ nigdzie nie ma więcej niż 2 dzieci. To zapytanie powiedzie się i zwróci pełny dokument tylko wtedy, gdy parametr będzie równy 2.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu bazy danych MongoDB 

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-sql-api.md)

