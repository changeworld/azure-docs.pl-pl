---
title: 'Quickstart: Use Node.js to query from Azure Cosmos DB SQL API account'
description: How to use Node.js to create an app that connects to Azure Cosmos DB SQL API account and queries data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 44cdd4307be56d864afb45d619958cc59a3fa978
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220521"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js to connect and query data from Azure Cosmos DB SQL API account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

This quickstart demonstrates how to use a Node.js app to connect to the [SQL API](sql-api-introduction.md) account in Azure Cosmos DB. You can then use Azure Cosmos DB SQL queries to query and manage data. The Node.js app you build in this article uses the [SQL JavaScript SDK](sql-api-sdk-node.md). W tym przewodniku Szybki start jest używana wersja 2.0 [zestawu SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Dodatkowo:
    * [Node.js](https://nodejs.org/en/) w wersji 6.0.0 lub nowszej
    * [Usługa Git](https://git-scm.com/)

## <a name="create-a-database"></a>Tworzenie bazy danych 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Add a container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Now let's clone a Node.js app from GitHub, set the connection string, and run it.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. If you're interested in learning how the Azure Cosmos database resources are created in the code, you can review the following snippets. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). 

Osoby znające poprzednią wersję zestawu SDK języka JavaScript mogą być przyzwyczajone do terminów „kolekcja” i „dokument”. Ponieważ usługa Azure Cosmos DB obsługuje [wiele modeli interfejsu API](https://docs.microsoft.com/azure/cosmos-db/introduction), w wersji 2.0 i nowszych wersjach zestawu SDK języka JavaScript są używane ogólne terminy „kontener” (może oznaczać kolekcję, graf lub tabelę) i „element” (opisuje zawartość kontenera).

Wszystkie poniższe fragmenty kodu pochodzą z pliku **app.js**.

* The `CosmosClient` object is initialized.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Create a new Azure Cosmos database.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* A new container (collection) is created within the database.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Tworzenie elementu (dokumentu).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* A SQL query over JSON is performed on the family database. The query returns all the children of the "Anderson" family. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Now go back to the Azure portal to get the connection string details of your Azure Cosmos account. Copy the connection string into the app so that it can connect to your database.

1. In the [Azure portal](https://portal.azure.com/), in your Azure Cosmos account, in the left navigation click **Keys**, and then click **Read-write Keys**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz identyfikator URI i klucz podstawowy do pliku `config.js`.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-dotnet/keys.png)

2. Otwórz plik `config.js`. 

3. Skopiuj wartość identyfikatora URI z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza endpoint w pliku `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Następnie skopiuj wartość klucza podstawowego z portalu i przypisz ją do klucza `config.key` w pliku `config.js`. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Uruchom polecenie `npm install` w terminalu, aby zainstalować wymagane moduły npm

2. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

You can now go back to Data Explorer, modify, and work with this new data.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

In this quickstart, you've learned how to create an Azure Cosmos account, create a container using the data explorer, and run an app. You can now import additional data to your Azure Cosmos database. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)


