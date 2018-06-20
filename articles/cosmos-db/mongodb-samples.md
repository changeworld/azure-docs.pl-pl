---
title: Tworzenie aplikacji usługi Azure Cosmos DB przy użyciu interfejsów API bazy danych MongoDB | Microsoft Docs
description: Samouczek, w którym przedstawiono tworzenie bazy danych online za pomocą interfejsów API dla bazy danych MongoDB w usłudze Azure Cosmos DB.
keywords: mongodb examples
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: bd31656404f11c9676b321e2e40454c33f61e3f5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795197"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Tworzenie aplikacji usługi Azure Cosmos DB z interfejsem API bazy danych MongoDB przy użyciu środowiska Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

W tym przykładzie przedstawiono sposób tworzenia aplikacji konsolowej usługi Azure Cosmos DB z interfejsem API bazy danych MongoDB przy użyciu środowiska Node.js.

Aby użyć tego przykładu, musisz:

* [Utworzyć](create-mongodb-dotnet.md#create-account) konto usługi Azure Cosmos DB z interfejsem API bazy danych MongoDB.
* Pobrać informacje o [parametrach połączenia](connect-mongodb-account.md) bazy danych MongoDB.

## <a name="create-the-app"></a>Tworzymy aplikację.

1. Utwórz plik *app.js*, a następnie skopiuj i wklej poniższy kod.

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
    
    **Opcjonalnie**: jeśli używasz **sterownika MongoDB Node.js 2.2**, zamień następujący fragment kodu:

    Oryginalny kod:

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
    
    Należy zamienić na:

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
    
2. Zmodyfikuj następujące zmienne w pliku *app.js* zgodnie z ustawieniami konta (dowiedz się, jak znaleźć [parametry połączenia](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > **Sterownik MongoDB Node.js 3.0** wymaga kodowania znaków specjalnych w haśle usługi Cosmos DB. Upewnij się, że znaki „=” są kodowane jako %3D
    >
    > Przykład: hasło *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* jest kodowane jako *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > **Sterownik MongoDB Node.js 2.2** nie wymaga kodowania znaków specjalnych w haśle usługi Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Otwórz swój ulubiony terminal, uruchom polecenie **npm install mongodb --save**, następnie uruchom aplikację za pomocą polecenia **node app.js**

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [korzystać z programu MongoChef](mongodb-mongochef.md) za pomocą konta usługi Azure Cosmos DB z interfejsem API bazy danych MongoDB.
