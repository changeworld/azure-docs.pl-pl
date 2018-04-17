---
title: Tworzenie aplikacji bazy danych rozwiązania Cosmos Azure przy użyciu interfejsów API bazy danych MongoDB | Dokumentacja firmy Microsoft
description: Samouczek, która tworzy bazy danych online za pomocą interfejsów API usługi Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB.
keywords: Przykłady bazy danych mongodb
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: anhoh
ms.openlocfilehash: 81eff479c94af938918e6a221d45184ca1a84aef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Tworzenie bazy danych Azure rozwiązania Cosmos: interfejs API dla aplikacji bazy danych MongoDB przy użyciu środowiska Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

W tym przykładzie przedstawiono sposób tworzenia bazy danych Azure rozwiązania Cosmos: interfejs API dla aplikacji konsoli bazy danych MongoDB przy użyciu środowiska Node.js.

Aby użyć tego przykładu, należy:

* [Utwórz](create-mongodb-dotnet.md#create-account) bazy danych Azure rozwiązania Cosmos: interfejsu API dla konta bazy danych MongoDB.
* Pobieranie Twojej bazy danych MongoDB [ciąg połączenia](connect-mongodb-account.md) informacji.

## <a name="create-the-app"></a>Tworzymy aplikację.

1. Utwórz *app.js* pliku skopiuj i Wklej kod poniżej.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcjonalne**: Jeśli używasz **sterownik bazy danych MongoDB Node.js 2.2**, Zamień poniższy fragment kodu:

    Oryginał:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Powinien zostać zamieniony:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Zmodyfikuj następujące zmienne w *app.js* plik na ustawienia konta (Znajdowanie użytkownika [ciąg połączenia](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > **Sterownik bazy danych MongoDB Node.js 3.0** wymaga kodowania znaków specjalnych w haśle DB rozwiązania Cosmos. Upewnij się, że do kodowania znaków "=" % 3
    >
    > Przykład: Hasło *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv ==* koduje o *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv 3D % 3D*
    >
    > **Sterownik bazy danych MongoDB Node.js 2.2** nie jest wymagane kodowanie znaków specjalnych w haśle DB rozwiązania Cosmos.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Otwórz swój ulubiony terminal, uruchom **npm zainstalować bazy danych mongodb — Zapisz**, następnie uruchom aplikację w usłudze **węzła app.js**

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Użyj MongoChef](mongodb-mongochef.md) Twojego Azure DB rozwiązania Cosmos: interfejsu API dla konta bazy danych MongoDB.
