---
title: Jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB
description: Dowiedz się, jak definiować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: c94509fb39d1c5ebb9aec1acfe1cbacc9cd6fd4a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268422"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia zintegrowane z językiem, transakcyjne wykonywanie kodu JavaScript, które umożliwia pisanie **procedur składowanych**, **wyzwalaczy** i **funkcji zdefiniowanych przez użytkownika (UDF)**. Korzystając z interfejsu API SQL w usłudze Azure Cosmos DB, można definiować procedury składowane, wyzwalacze i funkcje UDF w języku JavaScript. Logikę można napisać w języku JavaScript, a następnie wykonać ją w aparacie bazy danych. Wyzwalacze, procedury składowane i funkcje UDF można tworzyć i wykonywać przy użyciu witryny [Azure Portal](https://portal.azure.com/), [zintegrowanego interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md) i [zestawów SDK klienta interfejsu API SQL usługi Cosmos DB](sql-api-dotnet-samples.md). 

Aby wywołać procedurę składowaną, wyzwalacz i funkcję zdefiniowaną przez użytkownika, należy je zarejestrować. Aby uzyskać więcej informacji, zobacz [Jak pracować z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> W przypadku kontenerów podzielonych na partycje podczas wykonywania procedury składowanej w opcjach żądania należy podać wartość klucza partycji. Procedury składowane są zawsze ograniczone do klucza partycji. Elementy, które mają inną wartość klucza partycji, nie będą widoczne dla procedury składowanej. Ma to również zastosowanie do wyzwalaczy.

## <a id="stored-procedures"></a>Jak pisać procedury składowane

Procedury składowane pisze się przy użyciu języka JavaScript. Mogą one tworzyć, aktualizować, odczytywać i usuwać elementy w kontenerze usługi Azure Cosmos oraz wysyłać względem nich zapytania. Procedury składowane są rejestrowane w danej kolekcji i mogą operować na dowolnych dokumentach lub załącznikach znajdujących się w tej kolekcji.

**Przykład**

Poniżej przedstawiono prostą procedurę składowaną, która zwraca odpowiedź „Hello World”.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Obiekt kontekstu zapewnia dostęp do wszystkich operacji, które mogą być wykonywane w usłudze Azure Cosmos DB, a także do obiektów żądań i odpowiedzi. W tym przypadku obiekt odpowiedzi jest używany w celu ustawienia wysłania treści odpowiedzi z powrotem do klienta.

Po napisaniu procedurę składowaną należy zarejestrować w kolekcji. Aby dowiedzieć się więcej, zobacz artykuł [Jak używać procedur składowanych w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures).

### <a id="create-an-item"></a>Tworzenie elementu za pomocą procedury składowanej

Po utworzeniu elementu za pomocą procedury składowanej jest on wstawiany do kontenera usługi Azure Cosmos DB, a następnie dla tego nowo utworzonego elementu jest zwracany identyfikator. Tworzenie elementu jest operacją asynchroniczną i zależy od funkcji wywołania zwrotnego języka JavaScript. Funkcja wywołania zwrotnego ma dwa parametry — jeden dla obiektu błędu na wypadek, gdyby operacja zakończyła się niepowodzeniem, a drugi dla wartości zwracanej, czyli w tym przypadku dla utworzonego obiektu. Wewnątrz wywołania zwrotnego można obsłużyć wyjątek lub zgłosić błąd. W przypadku, gdy wywołanie zwrotne nie zostanie podane i wystąpi błąd, środowisko uruchomieniowe usługi Azure Cosmos DB zgłosi błąd. 

Procedura składowana obejmuje również parametr umożliwiający ustawienie opisu — jest to wartość logiczna. Jeśli dla tego parametru zostanie ustawiona wartość „true”, a opisu nie będzie, procedura składowana zgłosi wyjątek. W przeciwnym razie pozostała część procedury składowanej zostanie wykonana.

Poniższa przykładowa procedura składowana przyjmuje nowy element usługi Azure Cosmos DB jako dane wejściowe, wstawia je do kontenera usługi Azure Cosmos DB i zwraca identyfikator nowo utworzonego elementu. W tym przykładzie wykorzystujemy próbkę ToDoList z [przewodnika Szybki start interfejsu API SQL platformy .NET](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tablice jako parametry wejściowe dla procedur składowanych 

Podczas definiowania procedury składowanej w witrynie Azure Portal parametry wejściowe są zawsze wysyłane do tej procedury składowanej jako ciąg. Nawet jeśli jako dane wejściowe przekażesz tablicę ciągów, tablica jest konwertowana na ciąg i wysyłana do procedury składowanej. Aby to obejść, można zdefiniować funkcję w swojej procedurze składowanej, aby przeanalizować ciąg jako tablicę. Poniższy kod pokazuje, jak można przeanalizować parametr wejściowy ciągu jako tablicę:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Transakcje w ramach procedur składowanych

Za pomocą procedury składowanej można zaimplementować transakcje na elementach w kontenerze. W poniższym przykładzie użyto transakcji w ramach aplikacji gry w ligę futbolową, aby wymieniać zawodników między dwoma drużynami w jednej operacji. Procedura składowana podejmuje próbę odczytu dwóch elementów usługi Azure Cosmos DB, z których każdy odpowiada identyfikatorom zawodników przekazanych jako argument. Jeśli obydwaj zawodnicy zostaną odnalezieni, to procedura składowana aktualizuje te elementy, zamieniając ich drużyny. Jeśli po drodze zostaną napotkane jakiekolwiek błędy, procedura składowana zgłasza wyjątek języka JavaScript, który niejawnie przerywa transakcję.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>Powiązane wykonywanie w ramach procedur składowanych

Poniżej przedstawiono przykład procedury składowanej, która zbiorczo importuje elementy do kontenera usługi Azure Cosmos. Procedura składowana obsługuje powiązane wykonywanie, sprawdzając wartość logiczną zwracaną z obiektu `createDocument`, a następnie używa liczby elementów wstawianych w każdym wywołaniu procedury składowanej do śledzenia i wznawiania postępu w partiach.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>Jak pisać wyzwalacze

Usługa Azure Cosmos DB obsługuje wyzwalacze wykonywane przed operacją (pre-trigger) i po operacji (post-trigger). Te pierwsze są wykonywane przed modyfikacją elementu bazy danych, a te drugie są wykonywane po modyfikacji elementu bazy danych.

### <a id="pre-triggers"></a>Wyzwalacze wykonywane przed operacją

Poniższy przykład pokazuje użycie wyzwalacza wykonywanego przed operacją do weryfikowania właściwości tworzonego elementu usługi Azure Cosmos DB. W tym przykładzie wykorzystujemy próbkę ToDoList z [przewodnika Szybki start interfejsu API SQL platformy .NET](create-sql-api-dotnet.md), aby dodać właściwość znacznika czasu do nowo dodanego elementu, jeśli jeszcze jej nie zawiera.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Wyzwalacze wykonywane przed operacją nie mogą mieć żadnych parametrów wejściowych. Obiekt żądania w wyzwalaczu służy do manipulowania komunikatem żądania skojarzonym z tą operacją. W powyższym przykładzie wyzwalacz wykonywany przed operacją jest uruchamiany podczas tworzenia elementu usługi Azure Cosmos DB, a treść komunikatu żądania zawiera element, który ma zostać utworzony w formacie JSON.

Podczas rejestrowania wyzwalaczy można określić operacje, z którymi można je uruchamiać. Ten wyzwalacz powinien zostać utworzony z wartością `TriggerOperation` operacji `TriggerOperation.Create`, co oznacza, że użycie tego wyzwalacza w operacji zamieniania, jak pokazano w poniższym kodzie, jest niedozwolone.

Aby zapoznać się z przykładami rejestrowania i wywoływania wyzwalacza wykonywanego przed operacją, zobacz artykuły na temat [wyzwalaczy wykonywanych przed operacją](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) i [wyzwalaczy wykonywanych po operacji](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

### <a id="post-triggers"></a>Wyzwalacze wykonywane po operacji

W poniższym przykładzie pokazano wyzwalacz wykonywany po operacji. Ten wyzwalacz wysyła zapytanie o element metadanych i aktualizuje go za pomocą informacji o nowo utworzonym elemencie.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Jedną rzeczą, o której należy pamiętać, jest transakcyjne wykonywanie wyzwalaczy w usłudze Azure Cosmos DB. Po wprowadzeniu wyzwalacz jest uruchamiany w ramach tej samej transakcji dla elementu bazowego. Wyjątek podczas wykonywania wyzwalacza po zakończy się niepowodzeniem, cała transakcja. Cokolwiek zatwierdzone zostanie wycofana i zwrócony wyjątek.

Aby zapoznać się z przykładami rejestrowania i wywoływania wyzwalacza wykonywanego przed operacją, zobacz artykuły na temat [wyzwalaczy wykonywanych przed operacją](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) i [wyzwalaczy wykonywanych po operacji](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

## <a id="udfs"></a>Jak pisać funkcje zdefiniowane przez użytkownika

Poniższy przykład pokazuje tworzenie funkcji zdefiniowanej przez użytkownika w celu obliczenia podatku dochodowego dla różnych przedziałów dochodu. Ta funkcja zdefiniowana przez użytkownika zostanie następnie użyta w zapytaniu. Na potrzeby tego przykładu załóżmy, że istnieje kontener o nazwie „Incomes” (Dochody) z następującymi właściwościami:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Poniższej przedstawiono definicję funkcji obliczającej podatek dochodowy dla różnych przedziałów dochodu:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Aby zapoznać się z przykładami rejestrowania i używania funkcji zdefiniowanej przez użytkownika, zobacz artykuł [Jak używać funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs).

## <a name="next-steps"></a>Kolejne kroki

Poznaj dodatkowe pojęcia i dowiedz się, jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB:

* [Jak rejestrować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika oraz jak ich używać w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak napisać procedur składowanych i wyzwalaczy w usłudze Azure Cosmos DB przy użyciu interfejsu API zapytań języka Javascript](how-to-write-javascript-query-api.md)

* [Pracę z usługą Azure Cosmos DB procedur przechowywanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Praca z języka JavaScript zintegrowany interfejs API zapytań w usłudze Azure Cosmos DB](javascript-query-api.md)
