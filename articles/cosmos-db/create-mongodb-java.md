---
title: 'Azure Cosmos DB: Tworzenie aplikacji konsolowej przy użyciu języka Java i interfejsu API usługi MongoDB | Microsoft Docs'
description: Przykładowy kod Java, którego można używać do nawiązywania połączeń z interfejsem API MongoDB usługi Azure Cosmos DB i wykonywania względem niego zapytań
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 9716bc9a4728a48732cacef7c2568a640ba39ee6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Tworzenie aplikacji konsolowej interfejsu API usługi MongoDB przy użyciu języka Java i witryny Azure Portal

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

Ten przewodnik Szybki start przedstawia sposób tworzenia [interfejsu API MongoDB](mongodb-introduction.md) konta usługi Azure Cosmos DB, bazy danych dokumentów i kolekcji przy użyciu witryny Azure Portal. Następnie utworzysz i wdrożysz aplikację konsolową w oparciu o [sterownik Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu muszą być spełnione następujące wymagania wstępne:
* Zestaw JDK 1.7 lub nowszy (uruchom polecenie `apt-get install default-jdk`, jeśli zestaw JDK nie jest zainstalowany)
* Narzędzie Maven (uruchom polecenie `apt-get install maven`, jeśli narzędzie Maven nie jest zainstalowane)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

Nadaj nazwę **db** nowej bazie danych i nazwę **coll** nowej kolekcji.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API usługi MongoDB z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.  

2. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Następnie otwórz kod w ulubionym edytorze. 

## <a name="review-the-code"></a>Przeglądanie kodu

Dokonajmy szybkiego przeglądu działań wykonywanych w aplikacji. Otwórz plik `Program.java` i zobacz, że następujące wiersze kodu tworzą zasoby usługi Azure Cosmos DB. 

* Inicjowanie klienta DocumentClient.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Tworzenie nowej bazy danych i kolekcji.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Wstawianie dokumentów za pomocą metody `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Wykonywanie zapytań za pomocą metody `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. Z poziomu konta wybierz pozycję **Szybki start** i Java, a następnie skopiuj parametry połączenia do Schowka

2. Otwórz plik `Program.java` i zastąp argument konstruktora MongoClientURI za pomocą parametrów połączenia. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. Uruchom polecenie `mvn package` w terminalu, aby zainstalować wymagane moduły npm

2. Uruchom polecenie `mvn exec:java -D exec.mainClass=GetStarted.Program` w terminalu, aby uruchomić aplikację Java.

Teraz możesz używać narzędzia [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) do modyfikowania nowych danych, wykonywania zapytań względem nich i pracowania z nimi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kolekcji za pomocą Eksploratora danych i uruchamiania aplikacji konsolowej. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane usługi MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)


