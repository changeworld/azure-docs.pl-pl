---
title: Tworzenie aplikacji internetowej za pomocą interfejsu API usługi Azure Cosmos DB | Microsoft Docs
description: Samouczek dotyczący usługi Azure Cosmos DB, w którym opisano sposób tworzenia aplikacji internetowej bazy danych online za pomocą interfejsu API bazy danych MongoDB.
keywords: mongodb examples
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 76a8e19bacdbde938758bf41ed7f209521f513aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2018
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: nawiązywania połączenia z aplikacją MongoDB przy użyciu platformy .NET

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

W tym samouczku przedstawiono sposób tworzenia konta usługi Azure Cosmos DB za pomocą witryny Azure Portal oraz sposób tworzenia bazy danych i kolekcji do przechowywania danych za pomocą [interfejsu API bazy danych MongoDB](mongodb-introduction.md). 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB 
> * Aktualizowanie parametrów połączenia
> * Tworzenie aplikacji bazy danych MongoDB na maszynie wirtualnej 


## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Zacznijmy od utworzenia konta usługi Azure Cosmos DB w witrynie Azure Portal.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Masz już konto usługi Azure Cosmos DB? Jeśli tak, przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS)
> * Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

1. W witrynie Azure Portal na stronie **Azure Cosmos DB** wybierz interfejsu API dla konta bazy danych MongoDB. 
2. Na lewym pasku bloku konta kliknij pozycję **Szybki start**. 
3. Wybierz platformę (*sterownik .NET*, *sterownik Node.js*, *powłoka MongoDB*, *sterownik Java*, *sterownik Python* ). Jeśli sterownik lub narzędzie nie znajduje się na liście, nie przejmuj się — stale dokumentujemy kolejne fragmentu kodu połączenia. 
4. Po skopiowaniu i wklejeniu fragmentu kodu do aplikacji bazy danych MongoDB możesz już rozpocząć pracę.

## <a name="set-up-your-mongodb-app"></a>Konfigurowanie aplikacji bazy danych MongoDB

Możesz skorzystać z samouczka [Tworzenie aplikacji internetowej na platformie Azure, która łączy się z bazą danych MongoDB uruchomioną na maszynie wirtualnej](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md), wprowadzając minimalne modyfikacje, aby szybko skonfigurować aplikację MongoDB (lokalną lub opublikowaną w ramach aplikacji internetowej platformy Azure) nawiązującą połączenie z interfejsem API na potrzeby konta bazy danych MongoDB.  

1. Postępuj zgodnie z samouczkiem, wprowadzając jedną modyfikację.  Zastąp kod Dal.cs następującym kodem:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. Zmodyfikuj następujące zmienne w pliku Dal.cs dla wszystkich ustawień konta z poziomu strony Klucze w witrynie Azure Portal:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Możesz już użyć aplikacji!

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka. 

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB 
> * Aktualizowanie parametrów połączenia
> * Tworzenie aplikacji bazy danych MongoDB na maszynie wirtualnej

Możesz przejść do następnego samouczka i zaimportować dane MongoDB do usługi Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)

