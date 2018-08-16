---
title: Samouczek środowiska Node.js dotyczący interfejsu API SQL dla usługi Azure Cosmos DB | Microsoft Docs
description: Samouczek środowiska Node.js, przedstawiający tworzenie bazy danych Cosmos DB przy użyciu interfejsu API SQL.
keywords: samouczek node.js, baza danych node
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/14/2017
ms.author: sngun
ms.openlocfilehash: 8eba0f70141fe28a478e842523023a22c2db50fe
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628753"
---
# <a name="nodejs-tutorial-use-the-sql-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Samouczek środowiska Node.js: tworzenie aplikacji konsolowej Node.js przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Node.js 2.0 (wersja zapoznawcza)](sql-api-nodejs-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)


Witamy w samouczku środowiska Node.js dla zestawu SDK środowiska Node.js usługi Azure Cosmos DB! W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

Omówione zostaną następujące czynności:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Instalowanie aplikacji
* Tworzenie bazy danych Node
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych Node

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Przejdź do sekcji [Pobieranie kompletnego rozwiązania](#GetSolution), aby uzyskać krótkie instrukcje.

Po ukończeniu samouczka środowiska Node.js użyj przycisków głosowania u góry lub u dołu tej strony, aby przesłać nam swoją opinię. Jeśli chcesz, abyśmy skontaktowali się z Tobą bezpośrednio, możesz w komentarzach podać swój adres e-mail.

Teraz do dzieła!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Wymagania wstępne dla samouczka środowiska Node.js

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) w wersji 0.10.29 lub nowszej.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie aplikacji Node.js](#SetupNode). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Azure Cosmos DB Emulator (Emulator usługi Azure Cosmos DB)](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie aplikacji Node.js](#SetupNode).

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
4. Zainstaluj moduł documentdb za pomocą menedżera npm. Użyj następującego polecenia:
   * ```npm install documentdb --save```

Wspaniale! Teraz, po zakończeniu instalacji, zacznijmy pisanie kodu.

## <a id="Config"></a>Krok 3. Ustawianie konfiguracji aplikacji

Otwórz plik ```config.js``` w ulubionym edytorze tekstu.

Następnie skopiuj i wklej poniższy fragment kodu oraz ustaw identyfikator URI punktu końcowego i klucz podstawowy usługi Azure Cosmos DB jako wartości właściwości ```config.endpoint``` i ```config.primaryKey```. Obie te konfiguracje można znaleźć w witrynie [Azure Portal](https://portal.azure.com).

![Samouczek środowiska Node.js — zrzut ekranu witryny Azure Portal przedstawiający konto usługi Azure Cosmos DB z wyróżnionym AKTYWNYM centrum, przyciskiem KLUCZE wyróżnionym w bloku konta usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI, KLUCZ PODSTAWOWY i KLUCZ POMOCNICZY wyróżnionymi w bloku Klucze — baza danych Node][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Skopiuj i wklej elementy ```database id```, ```collection id``` i ```JSON documents``` do obiektu ```config``` poniżej miejsca, w którym zostały ustawione właściwości ```config.endpoint``` i ```config.primaryKey```. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć [narzędzia migracji danych](import-data.md) usługi Azure Cosmos DB zamiast dodawać definicje dokumentów.

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
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

Definicje bazy danych, kolekcji i dokumentów będą pełnić rolę elementów ```database id``` i ```collection id``` oraz danych dokumentów usługi Azure Cosmos DB.

Na koniec wyeksportuj obiekt ```config```, aby można było odwoływać się do niego w pliku ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a> Krok 4. Łączenie się z kontem usługi Azure Cosmos DB

Otwórz pusty plik ```app.js``` w edytorze tekstu. Skopiuj i wklej kod poniżej, aby zaimportować moduł ```documentdb``` i nowo utworzony moduł ```config```.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    const uriFactory = require('documentdb').UriFactory;
    var config = require("./config");

Skopiuj i wklej kod, aby użyć wcześniej zapisanych właściwości ```config.endpoint``` i ```config.primaryKey``` do utworzenia nowego wystąpienia klasy DocumentClient.

    var config = require("./config");

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Teraz, gdy masz kod do zainicjowania klienta usługi Azure Cosmos DB, przyjrzyjmy się pracy z zasobami usługi Azure Cosmos DB.

## <a name="step-5-create-a-node-database"></a>Krok 5. Tworzenie bazy danych Node

Skopiuj i wklej poniższy kod, aby ustawić stan HTTP, jeśli dane nie zostaną znalezione, identyfikator bazy danych i identyfikator kolekcji. Te identyfikatory określają sposób znajdowania właściwej bazy danych i kolekcji przez klienta usługi Azure Cosmos DB.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseId = config.database.id;
    var collectionId = config.collection.id;

[Bazę danych](sql-api-resources.md#databases) można utworzyć za pomocą funkcji [createDatabase](/javascript/api/documentdb/documentclient) klasy **DocumentClient**. Baza danych jest kontenerem logicznym magazynu dokumentów podzielonym na partycje w kolekcjach.

Skopiuj i wklej funkcję **getDatabase** w celu utworzenia nowej bazy danych w pliku app.js z właściwością ```databaseId``` określoną w obiekcie ```config```. Funkcja sprawdzi, czy istnieje baza danych o takim samym identyfikatorze ```FamilyRegistry```. Jeśli istnieje, zostanie zwrócona ta baza danych zamiast tworzenia nowej.

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${databaseId}\n`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase({ id: databaseId }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Skopiuj i wklej kod poniżej miejsca ustawienia funkcji **getDatabase**, aby dodać funkcję pomocnika **exit**, która będzie drukować komunikat zakończenia i wywołanie funkcji **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    };

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos DB.

## <a id="CreateColl"></a>Krok 6. Tworzenie kolekcji

> [!WARNING]
> Metoda **createCollection** utworzy nową kolekcję, co ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

[Kolekcję](sql-api-resources.md#collections) można utworzyć za pomocą funkcji [createCollection](/javascript/api/documentdb/documentclient) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.

Skopiuj i wklej funkcję **getCollection** poniżej funkcji **getDatabase** w pliku app.js w celu utworzenia nowej kolekcji z właściwością ```collectionId``` określoną w obiekcie ```config```. Znowu sprawdzimy w celu upewnienia się, że kolekcja o takim samym identyfikatorze ```FamilyCollection``` jeszcze nie istnieje. Jeśli istnieje, zostanie zwrócona ta kolekcja zamiast tworzenia nowej.

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${collectionId}\n`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
                        client.createCollection(databaseUrl, { id: collectionId }, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Skopiuj i wklej kod poniżej wywołania funkcji **getDatabase**, aby wykonać funkcję **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie utworzono kolekcję usługi Azure Cosmos DB.

## <a id="CreateDoc"></a>Krok 7. Tworzenie dokumentu

[Dokument](sql-api-resources.md#documents) można utworzyć za pomocą funkcji [createDocument](/javascript/api/documentdb/documentclient) klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Teraz można wstawić dokument do usługi Azure Cosmos DB.

Skopiuj i wklej funkcję **getFamilyDocument** poniżej funkcji **getCollection** w celu utworzenia dokumentów zawierających dane JSON zapisane w obiekcie ```config```. Znowu sprawdzimy w celu upewnienia się, że dokument o takim samym identyfikatorze jeszcze nie istnieje.

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        console.log(`Getting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Skopiuj i wklej kod poniżej wywołania funkcji **getCollection**, aby wykonać funkcję **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie utworzono dokument usługi Azure Cosmos DB.

![Samouczek środowiska Node.js — diagram pokazujący hierarchiczną relację między kontem, bazą danych, kolekcją i dokumentami — baza danych Node](./media/sql-api-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>Krok 8. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje [zaawansowane zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji. Następujący przykładowy kod przedstawia zapytanie, które można uruchomić dla dokumentów w kolekcji.

Skopiuj i wklej funkcję **queryCollection** poniżej funkcji **getFamilyDocument** w pliku app.js. Usługa Azure Cosmos DB obsługuje zapytania podobne do zapytań SQL, jak pokazano poniżej. Aby uzyskać więcej informacji na temat tworzenia złożonych zapytań, zobacz [plac zabaw dla zapytań](https://www.documentdb.com/sql/demo) i [dokumentację dotyczącą zapytań](sql-api-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${collectionId}`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };

Na poniższym diagramie przedstawiono sposób, w jaki składnia zapytania SQL usługi Azure Cosmos DB jest wywoływana względem utworzonej kolekcji.

![Samouczek środowiska Node.js — diagram pokazujący zakres i znaczenie zapytania — baza danych Node](./media/sql-api-nodejs-get-started/node-js-tutorial-collection-documents.png)

Słowo kluczowe [FROM](sql-api-sql-query.md#FromClause) jest opcjonalne w zapytaniu, ponieważ zapytania usługi Azure Cosmos DB mają już zakres określony do pojedynczej kolekcji. W związku z tym klauzula "FROM Families f" może być zamieniona na "FROM root r" lub dowolną inną wybraną nazwę zmiennej. Usługa Azure Cosmos DB będzie wnioskowała, że zmienne Families, root lub wybrana nazwa zmiennej domyślnie odwołują się do bieżącej kolekcji.

Skopiuj i wklej kod poniżej wywołania funkcji **getFamilyDocument**, aby wykonać funkcję **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie wykonano zapytanie względem dokumentów usługi Azure Cosmos DB.

## <a id="ReplaceDocument"></a>Krok 9. Zastępowanie dokumentu
Usługa Azure Cosmos DB obsługuje zastępowanie dokumentów JSON.

Skopiuj i wklej funkcję **replaceFamilyDocument** poniżej funkcji **queryCollection** w pliku app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        console.log(`Replacing document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        document.children[0].grade = 6;
        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Skopiuj i wklej kod poniżej wywołania funkcji **queryCollection**, aby wykonać funkcję **replaceDocument**. Ponadto dodaj kod w celu ponownego wywołania funkcji **queryCollection**, aby sprawdzić, czy dokument został pomyślnie zmieniony.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie zastąpiono dokument usługi Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 10. Usuwanie dokumentu

Usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON.

Skopiuj i wklej funkcję **deleteFamilyDocument** poniżej funkcji **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        console.log(`Deleting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Skopiuj i wklej kod poniżej wywołania drugiej funkcji **queryCollection**, aby wykonać funkcję **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Gratulacje! Pomyślnie usunięto dokument usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 11. Usuwanie bazy danych Node

Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kolekcji, dokumentów itd.).

Skopiuj poniższą funkcję **cleanup** i wklej ją poniżej funkcji **deleteFamilyDocument**, aby usunąć bazę danych i wszystkie zasoby podrzędne.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${databaseId}`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    };

Skopiuj i wklej kod poniżej wywołania funkcji **deleteFamilyDocument**, aby wykonać funkcję **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Krok 12. Uruchamianie całej aplikacji Node.js

Cała sekwencja wywoływania funkcji powinna wyglądać następująco:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```

Powinny zostać wyświetlone dane wyjściowe aplikacji rozpoczynania pracy. Dane wyjściowe powinny odpowiadać tekstowi przykładu poniżej.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Gratulacje! Udało Ci się ukończyć samouczek środowiska Node.js i utworzyć swoją pierwszą aplikację konsolową usługi Azure Cosmos DB!

## <a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka środowiska Node.js

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać kod, możesz uzyskać go w serwisie [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Do uruchomienia rozwiązania GetStarted, które zawiera wszystkie przykłady znajdujące się w tym artykule, będą potrzebne następujące elementy:

* [Konto usługi Azure Cosmos DB][create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) dostępne w witrynie GitHub.

Zainstaluj moduł **documentdb** za pomocą menedżera npm. Użyj następującego polecenia:

* ```npm install documentdb --save```

Następnie w pliku ```config.js``` zaktualizuj wartości config.endpoint i config.primaryKey, zgodnie z opisem w sekcji [Krok 3. Ustawianie konfiguracji aplikacji](#Config). 

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie: ```node app.js```.

To wszystko — skompiluj projekt i gotowe! 

## <a name="next-steps"></a>Następne kroki
* Potrzebujesz bardziej złożonego przykładu środowiska Node.js? Zobacz [Tworzenie aplikacji internetowej Node.js za pomocą usługi Azure Cosmos DB](sql-api-nodejs-application.md).
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
