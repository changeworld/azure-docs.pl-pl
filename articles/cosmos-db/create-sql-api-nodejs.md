---
title: Szybki start — używanie pliku Node.js do wykonywania zapytań z konta interfejsu API SQL usługi Azure Cosmos DB
description: Jak użyć Node.js do utworzenia aplikacji, która łączy się z kontem interfejsu API SQL usługi Azure Cosmos DB i danymi zapytań.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240313"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Szybki start: łączenie danych interfejsu API SQL usługi Azure Cosmos DB za pomocą aplikacji Node.js

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start można utworzyć konto interfejsu API SQL usługi Azure Cosmos DB i zarządzać nim z witryny Azure portal i przy użyciu aplikacji Node.js sklonowanej z usługi GitHub. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="walkthrough-video"></a>Klip wideo

Obejrzyj ten klip wideo, aby uzyskać pełny instruktaż zawartości w tym artykule.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora bazy danych usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Tworzenie konta usługi Azure Cosmos

W tym celu przewodnika Szybki start można użyć [opcji wypróbuj usługę Azure Cosmos DB,](https://azure.microsoft.com/try/cosmosdb/) aby utworzyć konto usługi Azure Cosmos.

1. Przejdź do [strony try Usługi Azure Cosmos DB dla bezpłatnej](https://azure.microsoft.com/try/cosmosdb/) strony.

1. Wybierz konto interfejsu API **SQL** i wybierz pozycję **Utwórz**. Zaloguj się przy użyciu konta Microsoft.

1. Po pomyślnym zalogowaniu twoje konto usługi Azure Cosmos powinno być gotowe. Wybierz **pozycję Otwórz w witrynie Azure portal,** aby otworzyć nowo utworzone konto.

Opcja "Wypróbuj usługę Azure Cosmos DB za darmo" nie wymaga subskrypcji platformy Azure i oferuje konto usługi Azure Cosmos przez ograniczony okres 30 dni. Jeśli chcesz używać konta usługi Azure Cosmos przez dłuższy okres, należy zamiast [tego utworzyć konto](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) w ramach subskrypcji platformy Azure.

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz można użyć narzędzia Eksploratora danych w witrynie Azure portal do tworzenia bazy danych i kontenera.

1. Wybierz nowy kontener **Eksploratora** > **New Container**danych .

   Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo, aby go zobaczyć.

   ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodaj kontener** wprowadź ustawienia nowego kontenera.

   | Ustawienie           | Sugerowana wartość | Opis                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Identyfikator bazy danych**   | Zadania           | Wprowadź _Zadania_ jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **przepływność bazy danych aprowizuj,** umożliwia udostępnianie przepływności aprowizowanej do bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w oszczędzaniu kosztów. |
   | **Przepływność**    | 400             | Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.                                                                                                                                                                                                                                                    |
   | **Identyfikator kontenera**  | Items           | Wprowadź _elementy_ jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.                                                                                                                                                                                                                                                               |
   | **Klucz partycji** | /category       | Przykład opisany w tym artykule używa _/category_ jako klucz partycji.                                                                                                                                                                                                                                                                                                           |

   Oprócz poprzednich ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe zasady klucza podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).

   Kliknij przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujmy aplikację Node.js z gitHub, ustaw ciąg połączenia i uruchommy go.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych usługi Azure Cosmos są tworzone w kodzie, można przejrzeć następujące fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string).

Jeśli znasz poprzednią wersję sdk JĘZYKA JAVAScript SQL, możesz być używany do wyświetlania _zbiorów_ terminów i _dokumentu_. Ponieważ usługa Azure Cosmos DB obsługuje [wiele modeli interfejsu API,](introduction.md) [wersja 2.0+ zestawu JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) używa _ogólnego kontenera_terminów , które mogą być kolekcją, wykresem lub tabelą i _elementem_ do opisania zawartości kontenera.

Cosmos DB JavaScript SDK@azure/cosmosnazywa się " " i może być zainstalowany z npm ...

```bash
npm install @azure/cosmos
```

Wszystkie poniższe fragmenty kodu pochodzą z pliku _app.js_.

- Jest `CosmosClient` importowany z `@azure/cosmos` pakietu npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Nowy `CosmosClient` obiekt jest inicjowany.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Wybierz bazę danych "Zadania".

  ```javascript
  const database = client.database(databaseId);
  ```

- Wybierz kontener/kolekcję "Przedmioty".

  ```javascript
  const container = database.container(containerId);
  ```

- Zaznacz wszystkie elementy w kontenerze "Elementy".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Utwórz nowy element

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Aktualizowanie elementu

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Usuwanie elementu

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> Zarówno w metodach "update" i "delete", element musi być `container.item()`wybrany z bazy danych przez wywołanie . Dwa parametry przekazywane w są identyfikator elementu i klucz partycji elementu. W takim przypadku klucz parition jest wartością pola "kategoria".

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure portal, aby uzyskać szczegóły ciągu połączenia konta usługi Azure Cosmos. Skopiuj ciąg połączenia do aplikacji, aby mógł połączyć się z bazą danych.

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Klucze** z lewej strony nawigacji, a następnie wybierz pozycję **Odczytuj i zapisuj klucze**. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować identyfikator URI i klucz podstawowy do pliku _app.js_ w następnym kroku.

   ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-dotnet/keys.png)

2. W polu Otwórz plik _config.js._

3. Skopiuj wartość identyfikatora URI z portalu (za pomocą przycisku kopiowania) i spraw, aby była to wartość klucza punktu końcowego w _pliku config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Następnie skopiuj wartość klucza podstawowego `config.key` z portalu i zrób z niej wartość w _config.js_. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Uruchom `npm install` w terminalu,@azure/cosmosaby zainstalować " " pakiet npm

2. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

3. Dwa elementy utworzone wcześniej w tym przewodniku Szybki start są wymienione. Tworzony jest nowy element. Flaga "isComplete" na tym elemencie jest aktualizowana do "true", a następnie na koniec element jest usuwany.

Możesz kontynuować eksperymentowanie z tą przykładową aplikacją lub wrócić do Eksploratora danych, modyfikować i pracować z danymi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB, utworzyć kontener przy użyciu Eksploratora danych i uruchomić aplikację Node.js. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [importowanie danych do usługi Azure Cosmos db](import-data.md)
