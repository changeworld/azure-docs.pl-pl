---
title: 'Szybki start: Tworzenie aplikacji sieci Web przy użyciu interfejsu API platformy Azure dla Mongo DB i Java SDK — Azure Cosmos DB'
description: Przykładowy kod języka Java, którego można używać do nawiązywania połączeń i wykonywania zapytań przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 7129819b6cec068420b0b265d878078622721812
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965388"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Szybki start: Tworzenie aplikacji konsolowej przy użyciu języka Java i interfejsu API MongoDB w Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

W tym przewodniku szybki start użyjesz interfejsu API Azure Cosmos DB dla Mongo DB i Java SDK, aby utworzyć aplikację internetową konsoli. Azure Cosmos DB umożliwia szybkie tworzenie i wysyłanie zapytań do baz danych dokumentów, kluczy/wartości i grafów, a wszystkie korzyści wynikające z dystrybucji globalnej i możliwości skalowania poziomego są dostępne w ramach rdzenia Cosmos DB.

Ten przewodnik Szybki start opisuje sposób tworzenia konta usługi Cosmos przy użyciu [interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md). Następnie utworzysz i wdrożysz aplikację konsoli przy użyciu [sterownika Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu muszą być spełnione następujące wymagania wstępne:
* [Zainstaluj program JDK dla platformy Azure i Azure Stack JDK w wersji 8](https://aka.ms/azure-jdks)
* Narzędzie Maven (uruchom polecenie `apt-get install maven`, jeśli narzędzie Maven nie jest zainstalowane)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

Nadaj nazwę **db** nowej bazie danych i nazwę **coll** nowej kolekcji.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminalu usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Następnie otwórz kod w ulubionym edytorze. 

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku Program.java.

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

1. Na koncie wybierz pozycję **Szybki Start**, wybierz pozycję **Java**, a następnie skopiuj parametry połączenia do Schowka.

2. Otwórz plik `Program.java` i zastąp argument konstruktora MongoClientURI za pomocą parametrów połączenia. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. Uruchom polecenie `mvn package` w terminalu, aby zainstalować wymagane moduły npm

2. Uruchom polecenie `mvn exec:java -D exec.mainClass=GetStarted.Program` w terminalu, aby uruchomić aplikację Java.

Teraz możesz używać narzędzia [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) do modyfikowania nowych danych, wykonywania zapytań względem nich i pracowania z nimi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Cosmos, tworzenia kolekcji i uruchamiania aplikacji konsolowej. Teraz możesz zaimportować dodatkowe dane do swojej bazy danych usługi Cosmos.

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
