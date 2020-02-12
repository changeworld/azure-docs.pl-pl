---
title: 'Szybki Start: Używanie środowiska Node. js do wykonywania zapytań z Azure Cosmos DB konta interfejsu API SQL'
description: Jak używać środowiska Node. js do tworzenia aplikacji, która łączy się z Azure Cosmos DB konta interfejsu API SQL i wysyła zapytania do danych.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134486"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Szybki Start: Używanie środowiska Node. js do nawiązywania połączenia i wykonywania zapytań dotyczących danych z Azure Cosmos DB konta interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejsu API SQL z Azure Portal i zarządzasz nim, a następnie korzystasz z aplikacji node. js sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`klucza.
- [Node. js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-a-database"></a>Tworzenie bazy danych 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujmy aplikację Node. js z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją.

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

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych Azure Cosmos są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

Jeśli znasz poprzednią wersję zestawu SDK JavaScript języka SQL, możesz użyć, aby wyświetlić *kolekcje* terminów i *dokument*. Ponieważ Azure Cosmos DB obsługuje [wiele modeli interfejsów API](introduction.md), [wersja 2.0 + zestawu JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) używa *kontenera*warunków ogólnych, który może być kolekcją, wykresem lub tabelą i *elementem* do opisywania zawartości kontenera.

Wszystkie poniższe fragmenty kodu pochodzą z pliku *app.js*.

* Obiekt `CosmosClient` jest zainicjowany.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Utwórz nową bazę danych usługi Azure Cosmos.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Nowy kontener (kolekcja) jest tworzony w ramach bazy danych.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Tworzenie elementu (dokumentu).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Zapytanie SQL w formacie JSON jest wykonywane w bazie danych rodziny. Zapytanie zwraca wszystkie elementy podrzędne rodziny "Anderson". 

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

Teraz wróć do Azure Portal, aby uzyskać szczegóły parametrów połączenia konta usługi Azure Cosmos. Skopiuj parametry połączenia do aplikacji, aby można było połączyć się z bazą danych.

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **klucze** w lewym okienku nawigacji, a następnie wybierz pozycję klucze **odczytu i zapisu**. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować identyfikator URI i klucz podstawowy do pliku *config. js* w następnym kroku.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-dotnet/keys.png)

2. W obszarze Otwórz plik *config. js* . 

3. Skopiuj wartość identyfikatora URI z portalu (przy użyciu przycisku kopiowania) i ustaw ją jako wartość klucza punktu końcowego w *pliku config. js*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Następnie skopiuj wartość klucza podstawowego z portalu i ustaw jej wartość `config.key` w *pliku config. js*. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Uruchom polecenie `npm install` w terminalu, aby zainstalować wymagane moduły npm.

2. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

Teraz możesz wrócić do Eksplorator danych, modyfikować i pracy z nowymi danymi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji node. js. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)


