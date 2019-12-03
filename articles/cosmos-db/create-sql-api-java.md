---
title: Szybki Start — używanie języka Java do tworzenia bazy danych dokumentów przy użyciu Azure Cosmos DB
description: Ten przewodnik Szybki Start przedstawia przykładowy kod Java, którego można użyć do nawiązania połączenia z interfejsem API SQL Azure Cosmos DB i wykonywania na nich zapytań
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a4a8990b3da534acb39ff87c9f7665fb3b08ef06
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708162"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki Start: Tworzenie aplikacji Java do zarządzania Azure Cosmos DB danych interfejsu API SQL


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start pokazano, jak za pomocą aplikacji języka Java utworzyć bazę danych dokumentów i zarządzać nią z poziomu Azure Cosmos DB konta interfejsu API SQL. Najpierw utwórz konto Azure Cosmos DB interfejsu API SQL przy użyciu Azure Portal, Utwórz aplikację Java przy użyciu zestawu SQL Java SDK, a następnie Dodaj zasoby do konta Cosmos DB przy użyciu aplikacji Java. Instrukcje podane w tym przewodniku Szybki start można wykonać w dowolnym systemie operacyjnym, w którym można uruchomić oprogramowanie Java. Po ukończeniu tego przewodnika Szybki Start zobaczysz, jak tworzyć i modyfikować bazy danych Cosmos DB, kontenery w interfejsie użytkownika lub programowo, zależnie od preferencji użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Dodatkowo: 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 

* Inicjowanie klienta `CosmosClient`. `CosmosClient` zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos Database. Ten klient jest używany do konfigurowania i wykonywania żądań dotyczących usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* CosmosDatabase.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* CosmosContainer.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Tworzenie elementu przy użyciu metody `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Odczyty punktów są wykonywane przy użyciu metody `getItem` i `read`

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Zapytania SQL w formacie JSON są wykonywane przy użyciu metody `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.


1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-cosmos-java-getting-started
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
    
4. Aplikacja tworzy bazę danych o nazwie `AzureSampleFamilyDB`
5. Aplikacja tworzy kontener o nazwie `FamilyContainer`
6. Aplikacja przeprowadzi odczyty punktów przy użyciu identyfikatorów obiektów i wartości klucza partycji (czyli lastName w naszym przykładzie). 
7. Aplikacja będzie wysyłać zapytania o elementy, aby pobrać wszystkie rodziny o nazwisku ("Andersen", "Wakefield", "Johnsonem")

7. Aplikacja nie usuwa utworzonych zasobów. Przełącz się do portalu, aby [wyczyścić zasoby](#clean-up-resources).  z poziomu konta, aby nie spowodować naliczenia opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, bazy danych dokumentów i kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji w celu programistycznego wykonania tych czynności. Teraz możesz zaimportować dodatkowe dane do kontenera usługi Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
