---
title: Samouczek środowiska Node.js dotyczący interfejsu API SQL dla usługi Azure Cosmos DB | Microsoft Docs
description: Samouczek środowiska Node.js, w którym przedstawiono, jak nawiązać połączenie z bazą danych Azure Cosmos DB i wykonywać względem niej zapytania za pomocą interfejsu API SQL
services: cosmos-db
author: deborahc
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 61d9d33ae7e8e2104e61bbd24862a709dd6d78ec
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636496"
---
# <a name="tutorial-build-a-nodejs-console-app-with-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Samouczek: tworzenie aplikacji konsolowej Node.js przy użyciu zestawu SDK dla języka JavaScript na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

W tym samouczku pokazano, jak utworzyć aplikację konsolową Node.js na potrzeby tworzenia zasobów usługi Azure Cosmos DB i wykonywania zapytań względem nich.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
> * Instalowanie aplikacji
> * Tworzenie bazy danych
> * Tworzenie kontenera
> * Dodawanie elementów do kontenera
> * Wykonywanie operacji CRUD względem elementów, kontenera i bazy danych

Nie masz czasu na utworzenie aplikacji? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Zobacz sekcję [Pobieranie kompletnego rozwiązania](#GetSolution) tego artykułu, aby uzyskać krótkie instrukcje.

## <a name="prerequisites"></a>Wymagania wstępne 

Upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Środowisko [Node.js](https://nodejs.org/) w wersji 6.0.0 lub nowszej.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie aplikacji Node.js](#SetupNode). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie aplikacji Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Krok 2. Konfigurowanie aplikacji Node.js

1. Otwórz swój ulubiony terminal.
2. Zlokalizuj folder lub katalog, w którym chcesz zapisać aplikację Node.js.
3. Utwórz dwa puste pliki JavaScript za pomocą następujących poleceń:

   * W systemie Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * W systemie Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Zainstaluj moduł @azure/cosmos za pomocą menedżera npm. Użyj następującego polecenia:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Krok 3. Ustawianie konfiguracji aplikacji

1. Otwórz plik ```config.js``` w ulubionym edytorze tekstu.

1. Skopiuj i wklej poniższy fragment kodu oraz ustaw identyfikator URI punktu końcowego i klucz podstawowy usługi Azure Cosmos DB jako wartości właściwości ```config.endpoint``` i ```config.primaryKey```. Obie te konfiguracje można znaleźć w witrynie [Azure Portal](https://portal.azure.com).

   ![Zrzut ekranu przedstawiający pobieranie kluczy z witryny Azure Portal][keys]

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Skopiuj i wklej dane ```database```, ```container``` i ```items``` do obiektu ```config``` poniżej miejsca, w którym zostały ustawione właściwości ```config.endpoint``` i ```config.primaryKey```. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć [narzędzia migracji danych](import-data.md) usługi Azure Cosmos DB zamiast definiować tutaj dane.

   ```nodejs
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   Osoby znające poprzednią wersję zestawu SDK języka JavaScript mogą być przyzwyczajone do terminów „kolekcja” i „dokument”. Ponieważ usługa Azure Cosmos DB obsługuje [wielu modeli interfejsu API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), w wersji 2.0 + zestawu SDK dla języka JavaScript używane są ogólne terminy „kontener” i „element”. Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. 

1. Na koniec wyeksportuj obiekt ```config```, aby można było odwoływać się do niego w pliku ```app.js```.

   ```nodejs
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a> Krok 4. Łączenie się z kontem usługi Azure Cosmos DB

1. Otwórz pusty plik ```app.js``` w edytorze tekstu. Skopiuj i wklej kod poniżej, aby zaimportować moduł ```@azure/cosmos``` i nowo utworzony moduł ```config```.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');
   ```

1. Skopiuj i wklej kod, aby użyć wcześniej zapisanych właściwości ```config.endpoint``` i ```config.primaryKey``` do utworzenia nowego modułu CosmosClient.

   ```nodejs
   const url = require('url');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```

Teraz, gdy masz kod służący do zainicjowania klienta usługi Azure Cosmos DB, przyjrzyjmy się sposobowi pracy z zasobami usługi Azure Cosmos DB.

## <a name="step-5-create-a-database"></a>Krok 5. Tworzenie bazy danych

1. Skopiuj i wklej poniższy kod, aby ustawić identyfikator bazy danych i identyfikator kontenera. Te identyfikatory określają sposób znajdowania właściwej bazy danych i kontenera przez klienta usługi Azure Cosmos DB.

   ```nodejs
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   Bazę danych można utworzyć przy użyciu funkcji [createIfNotExists](/javascript/api/%40azure/cosmos/databases) lub [create](/javascript/api/%40azure/cosmos/databases) klasy **Databases**. Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach. 

2. Skopiuj i wklej metody **createDatabase** i **readDatabase** do pliku app.js pod definicją ```databaseId``` i ```containerId```. Funkcja **createDatabase** utworzy nową bazę danych (jeśli jeszcze nie istnieje) o identyfikatorze ```FamilyDatabase```określonym na podstawie obiektu ```config```. Funkcja **readDatabase** odczyta definicję bazy danych w celu sprawdzenia, czy baza danych istnieje.

   ```nodejs
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Skopiuj i wklej kod poniżej miejsca ustawienia funkcji **createDatabase** i **readDatabase**, aby dodać funkcję pomocnika **exit**, która będzie wyświetlać komunikat zakończenia. 

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Skopiuj i wklej kod poniżej miejsca ustawienia funkcji **exit** w celu wywołania funkcji **createDatabase** i **readDatabase**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Na tym etapie kod w pliku ```app.js``` powinien wyglądać następująco:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: 

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos DB.

## <a id="CreateContainer"></a>Krok 6. Tworzenie kontenera

> [!WARNING]
> Wywołanie funkcji **createContainer** spowoduje utworzenie nowego kontenera, co ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

Kontener można utworzyć przy użyciu funkcji [createIfNotExists](/javascript/api/%40azure/cosmos/containers) lub [create](/javascript/api/%40azure/cosmos/containers) z klasy **Containers**.  Kontener składa się z elementów (w przypadku interfejsu API SQL są to dokumenty JSON) i skojarzonej logiki aplikacji JavaScript.

1. Skopiuj i wklej funkcje **createContainer** i **readContainer** poniżej funkcji **readDatabase** w pliku app.js. Funkcja **createContainer** utworzy nowy kontener (jeśli jeszcze nie istnieje) o identyfikatorze ```containerId```określonym na podstawie obiektu ```config```. Funkcja **readContainer** odczyta definicję kontenera, aby sprawdzić, czy kontener istnieje.

   ```nodejs
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Skopiuj i wklej kod poniżej wywołania funkcji **readDatabase**, aby wykonać funkcje **createContainer** i **readContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Na tym etapie kod w pliku ```app.js``` powinien wyglądać następująco:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: 

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie utworzono kontener usługi Azure Cosmos DB.

## <a id="CreateItem"></a>Krok 7. Tworzenie elementu

Element można utworzyć za pomocą funkcji [create](/javascript/api/%40azure/cosmos/items) klasy **Items**. Podczas korzystania z interfejsu API SQL elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. Teraz można wstawić element do bazy danych Azure Cosmos DB.

1. Skopiuj i wklej funkcję **createFamilyItem** poniżej funkcji **readContainer**. Funkcja **createFamilyItem** służy do tworzenia elementów zawierających dane JSON zapisane w obiekcie ```config```. Przed utworzeniem wykonamy sprawdzenie w celu sprawdzenia, czy taki sam identyfikator już nie istnieje.

   ```nodejs
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Skopiuj i wklej kod poniżej wywołania funkcji **readContainer**, aby wykonać funkcję **createFamilyItem**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: 

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie utworzono element usługi Azure Cosmos DB.


## <a id="Query"></a>Krok 8. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje [zaawansowane zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w poszczególnych kontenerach. Następujący przykładowy kod przedstawia zapytanie, które można uruchomić względem dokumentów w kontenerze.

1. Skopiuj i wklej funkcję **queryContainer** poniżej funkcji **createFamilyItem** w pliku app.js. Usługa Azure Cosmos DB obsługuje zapytania podobne do zapytań SQL, jak pokazano poniżej. Aby uzyskać więcej informacji na temat tworzenia złożonych zapytań, zobacz [plac zabaw dla zapytań](https://www.documentdb.com/sql/demo) i [dokumentację dotyczącą zapytań](sql-api-sql-query.md).

   ```nodejs
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Skopiuj i wklej kod poniżej wywołań funkcji **createFamilyItem**, aby wykonać funkcję **queryContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie:

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie wykonano zapytanie względem elementów usługi Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 9. Zastępowanie elementu
Usługa Azure Cosmos DB obsługuje zastępowanie zawartości elementów.

1. Skopiuj i wklej funkcję **replaceFamilyItem** poniżej funkcji **queryContainer** w pliku app.js. Należy pamiętać, że właściwość „grade” elementu podrzędnego została zmieniona z poprzedniej wartości 5 na wartość 6.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Skopiuj i wklej kod poniżej wywołania funkcji **queryContainer**, aby wykonać funkcję **replaceFamilyItem**. Ponadto dodaj kod w celu ponownego wywołania funkcji **queryContainer**, aby sprawdzić, czy element został pomyślnie zmieniony.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie:

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie zastąpiono element usługi Azure Cosmos DB.

## <a id="DeleteItem"></a>Krok 10. Usuwanie elementu

Usługa Azure Cosmos DB obsługuje usuwanie elementów JSON.

1. Skopiuj i wklej funkcję **deleteFamilyItem** poniżej funkcji **replaceFamilyItem**.

   ```nodejs
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Skopiuj i wklej kod poniżej wywołania funkcji **queryContainer**, aby wykonać funkcję **deleteFamilyItem**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: 

   ```bash 
   node app.js
   ```

Gratulacje! Pomyślnie usunięto element usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 11. Usuwanie bazy danych

Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kontenerów, elementów itd.).

1. Skopiuj poniższą funkcję **cleanup** i wklej ją poniżej funkcji **deleteFamilyItem**, aby usunąć bazę danych i wszystkie jej zasoby podrzędne.

   ```nodejs
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Skopiuj i wklej kod poniżej wywołania funkcji **deleteFamilyItem**, aby wykonać funkcję **cleanup**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Krok 12. Uruchamianie całej aplikacji Node.js

Cały kod powinien wyglądać następująco:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: 

```bash 
node app.js
```

Powinny zostać wyświetlone dane wyjściowe aplikacji rozpoczynania pracy. Dane wyjściowe powinny odpowiadać tekstowi przykładu poniżej.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

Gratulacje! Udało Ci się ukończyć samouczek języka Node.js i utworzyć swoją pierwszą aplikację konsolową usługi Azure Cosmos DB!

## <a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka środowiska Node.js

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać kod, możesz uzyskać go w serwisie [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Do uruchomienia rozwiązania z wprowadzenia, które zawiera cały kod znajdujący się w tym artykule, będą potrzebne następujące elementy:

* [Konto usługi Azure Cosmos DB][create-account].
* Rozwiązanie [z wprowadzeniem](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) dostępne w witrynie GitHub.

Zainstaluj moduł **@azure/cosmos** za pomocą menedżera npm. Użyj następującego polecenia:

* ```npm install @azure/cosmos --save```

Następnie w pliku ```config.js``` zaktualizuj wartości config.endpoint i config.primaryKey, zgodnie z opisem w sekcji [Krok 3. Ustawianie konfiguracji aplikacji](#Config). 

W terminalu znajdź swój plik ```app.js```, a następnie uruchom następujące polecenie: 

```bash 
node app.js
```

To wszystko — możesz rozpocząć pracę! 

## <a name="next-steps"></a>Następne kroki

* Potrzebujesz bardziej złożonego przykładu środowiska Node.js? Zobacz [Tworzenie aplikacji internetowej Node.js za pomocą usługi Azure Cosmos DB](sql-api-nodejs-application.md).
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png