---
title: Tworzenie bazy danych dokumentów przy użyciu języka Java Azure Cosmos DB
description: Przykładowy kod Java, którego można używać do nawiązywania połączeń z interfejsem API SQL usługi Azure Cosmos DB i wykonywania względem niego zapytań
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.custom: seo-java-august2019
ms.openlocfilehash: a7e2089b8dfff1f021c31d2b13591b54dcd9c4ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616984"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>Szybki start: Tworzenie aplikacji w języku Java za pomocą konta interfejsu API SQL usługi Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start przedstawiono sposób tworzenia zasobów konta [interfejsu API SQL](sql-api-introduction.md) usługi Azure Cosmos DB i zarządzanie nimi przy użyciu aplikacji Java. Na początku zostaną wykonane następujące czynności: utworzenie konta interfejsu API SQL usługi Azure Cosmos DB przy użyciu witryny Azure Portal, utworzenie aplikacji Java za pomocą [zestawu SDK Java usługi SQL](sql-api-sdk-async-java.md) oraz dodanie zasobów do konta usługi Cosmos DB przy użyciu aplikacji Java. Instrukcje podane w tym przewodniku Szybki start można wykonać w dowolnym systemie operacyjnym, w którym można uruchomić oprogramowanie Java. Po ukończeniu tego przewodnika Szybki Start zobaczysz, jak tworzyć i modyfikować bazy danych Cosmos, kontenery w interfejsie użytkownika lub programowo, zależnie od preferencji użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Ponadto: 

* [Zestaw Java Development Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne [Maven](https://maven.apache.org/)
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów należy utworzyć konto interfejsu API SQL za pomocą usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujmy aplikację interfejsu API SQL z serwisu GitHub, ustawmy parametry połączenia i uruchommy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 

* Inicjowanie klienta `AsyncDocumentClient`. [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos Database. Ten klient jest używany do konfigurowania i wykonywania żądań dotyczących usługi.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Tworzenie elementu [Database](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Tworzenie elementu [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Tworzenie dokumentów przy użyciu metody [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* Zapytania SQL w formacie JSON są wykonywane przy użyciu metody [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.


1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. W oknie terminala usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Java.

    ```bash
    mvn package
    ```

3. W oknie terminalu usługi git użyj następującego polecenia, aby uruchomić aplikację Java (zastąp element YOUR_COSMOS_DB_HOSTNAME wartością identyfikatora URI w cudzysłowach z portalu oraz zastąp element YOUR_COSMOS_DB_MASTER_KEY kluczem podstawowym w cudzysłowach z portalu)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Okno terminala wyświetli powiadomienie o utworzeniu bazy danych FamilyDB. 
    
4. Naciśnij klawisz, aby utworzyć bazę danych, a następnie inny klawisz, aby utworzyć kolekcję. 

    Przełącz się z powrotem do Eksploratora danych w przeglądarce, aby sprawdzić, czy znajduje się w nim teraz baza danych FamilyDB i kolekcja FamilyCollection.

5. Przełącz się do okna konsoli i naciśnij klawisz w celu utworzenia pierwszego dokumentu, a następnie inny klawisz w celu utworzenia drugiego dokumentu. Następnie przełącz się z powrotem do Eksploratora danych, aby wyświetlić te dokumenty. 

6. Naciśnij klawisz, aby uruchomić zapytanie i wyświetlić dane wyjściowe w oknie konsoli. 

7. Aplikacja nie usuwa utworzonych zasobów. Przełącz się do portalu, aby [wyczyścić zasoby](#clean-up-resources).  z poziomu konta, aby nie spowodować naliczenia opłat.

    ![Dane wyjściowe konsoli](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, bazy danych dokumentów i kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji w celu programistycznego wykonania tych czynności. Teraz możesz zaimportować dodatkowe dane do kontenera usługi Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
