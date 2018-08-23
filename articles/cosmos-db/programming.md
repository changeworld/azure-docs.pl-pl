---
title: Programowanie JavaScript po stronie serwera dla usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak napisać procedury składowane, wyzwalacze bazy danych i funkcji zdefiniowanych przez użytkownika (UDF) w języku JavaScript za pomocą usługi Azure Cosmos DB. Uzyskuj wskazówki programistyczne bazy danych i nie tylko.
keywords: Bazy danych, wyzwalacze, procedury składowanej, procedury składowanej, program bazy danych, procedury sproc, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 6296eb423f24762ed32a21ef40852dc1a9dd8f36
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055974"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programowanie po stronie serwera w usłudze Azure Cosmos DB: procedury składowane, wyzwalacze bazy danych i funkcji zdefiniowanych przez użytkownika

Dowiedz się, jak usługi Azure Cosmos DB wykonywania języku zintegrowanym, transakcyjne języka JavaScript umożliwia programistom pisanie **procedur składowanych**, **wyzwalaczy**, i **funkcje zdefiniowane przez użytkownika (UDF)**  natywnie w [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) języka JavaScript. Integracja z językiem JavaScript umożliwia pisanie logiki programu, które mogą być dostarczane i wykonywane bezpośrednio w ramach partycji magazynu bazy danych. 

Firma Microsoft zaleca rozpoczęcie od obejrzenia poniższego klipu wideo, gdzie Andrew Liu zawiera wprowadzenie do modelu programowania bazy danych po stronie serwera usługi Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Następnie wróć do tego artykułu, którym poznasz odpowiedzi na następujące pytania:  

* Jak pisać procedury składowanej, wyzwalacza lub funkcji definiowanych przez użytkownika przy użyciu języka JavaScript
* Jak Cosmos DB gwarantuje ACID?
* Jak działają transakcje w usłudze Cosmos DB?
* Co to są wstępnie wyzwala i używane po tej operacji wyzwala i jak jedną pisać?
* Jak zarejestrować i wykonaj procedurę składowaną, wyzwalacza lub funkcji definiowanych przez użytkownika w sposób RESTful przy użyciu protokołu HTTP
* Co Cosmos DB w zestawy SDK są dostępne do tworzenia i wykonywanie procedur składowanych, wyzwalaczy i funkcje zdefiniowane przez użytkownika?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Wprowadzenie do programowania systemu plików UDF i procedury składowanej
To podejście *"JavaScript jako nowoczesnego dzień języka T-SQL"* ułatwia deweloperom aplikacji od złożoności niezgodności systemu typu i technologii mapowania obiektowo relacyjny. Ponadto wprowadzono szereg zalet wewnętrzne, które mogą być wykorzystywane do tworzenia rozbudowanych aplikacji:  

* **Proceduralne logiki:** języka JavaScript jako język programowania wysokiego poziomu, zapewnia szerokie i powszechnie znane interfejs na wyrażenie logiki biznesowej. Można wykonywać złożone sekwencje operacji bliżej do danych.
* **Transakcje niepodzielne:** usługi Cosmos DB gwarantuje, że baza danych operacje przeprowadzane w ramach jednej procedury składowanej lub wyzwalacza są niepodzielne. Funkcja ta atomic umożliwia aplikacji łączenie powiązanych operacji w jednej partii, tak, aby wszystkie z nich powodzenie lub żadna z nich powiodło się. 
* **Wydajność:** fakt, że JSON wewnętrznie jest mapowany na system typów języka Javascript, a także jest podstawową jednostką magazynu w usłudze Cosmos DB pozwala na liczbę optymalizacje, takie jak z opóźnieniem materializacja dokumentów JSON w puli buforów i dzięki czemu dostępne na żądanie na wykonywanie kodu. Istnieje więcej korzyści wydajności skojarzone z wysyłki logiki biznesowej w bazie danych:
  
  * Przetwarzanie wsadowe — deweloperzy mogą grupować operacje, takie jak operacje wstawiania i przesłać je zbiorczo. Opóźnienie ruchu w sieci, koszt i koszty magazynu umożliwia tworzenie oddzielnych transakcji są znacznie mniejsze. 
  * Prekompilowanie — usługa Cosmos DB wstępnie kompiluje procedur składowanych, wyzwalaczy i funkcji definiowanych (przez użytkownika UDF) w celu uniknięcia JavaScript koszt kompilacji dla każdego wywołania. Koszty tworzenia kod bajtowy procedurach logiki jest amortyzowanego do minimalnej wartości.
  * Sekwencjonowanie — wiele operacji potrzebę efekt uboczny ("Wyzwól") która potencjalnie obejmuje, wykonując jedną lub wiele operacji magazynu pomocniczego. Oprócz niepodzielność to wydajniej po przeniesieniu do serwera. 
* **Hermetyzacja:** składowane mogą służyć do grupowania logikę biznesową w jednym miejscu, która ma dwie zalety:
  * Dodaje warstwę abstrakcji na podstawie danych pierwotnych, co umożliwia architektów danych rozwój swoich aplikacji, niezależnie od danych. Ta warstwa abstrakcji jest korzystne, gdy dane znajdują się bez schematu, ze względu na kruchy założeń, które może być konieczne wbudowanymi do aplikacji, jeśli mają do czynienia z danymi bezpośrednio.  
  * Ta warstwa abstrakcji umożliwia przedsiębiorstwom chronić swoje dane przez usprawnienie dostępu ze skryptów.  

Utworzenie i wykonanie wyzwalacze bazy danych, procedury składowane i niestandardowych operatorów zapytań, jest świadczona za pośrednictwem [witryny Azure portal](https://portal.azure.com), [interfejsu API REST](/rest/api/cosmos-db/), [usługi Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), i [zestawów SDK klienta](sql-api-sdk-dotnet.md) na wielu platformach, w tym .NET, Node.js i JavaScript.

W tym samouczku [zestawu SDK środowiska Node.js za pomocą funkcji pytania i odpowiedzi obietnic](http://azure.github.io/azure-documentdb-node-q/) aby zilustrować składni i użycia, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika.   

## <a name="stored-procedures"></a>Procedury składowane
### <a name="example-write-a-stored-procedure"></a>Przykład: Napisać procedurę składowaną
Zacznijmy od prostego procedury przechowywanej zwracającej odpowiedź "Hello World".

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

Procedury składowane są rejestrowane w jednej kolekcji, a może działać na dowolnym dokument i załącznika w tej kolekcji. Poniższy fragment kodu przedstawia sposób zarejestrować procedury helloWorld przechowywane w kolekcji. 


```javascript
// register the stored procedure
var createdStoredProcedure;
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
    .then(function (response) {
        createdStoredProcedure = response.resource;
        console.log("Successfully created stored procedure");
    }, function (error) {
        console.log("Error", error);
    });
```


Po zarejestrowaniu procedury składowanej można ją wykonać kolekcji i przeczytaj wyniki ponownie na kliencie. 

```javascript
// execute the stored procedure
client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
    .then(function (response) {
        console.log(response.result); // "Hello, World"
    }, function (err) {
        console.log("Error", error);
    });
```

Obiekt kontekstu zapewnia dostęp do wszystkich operacji, które mogą być wykonywane w magazynie usługi Cosmos DB, a także dostęp do obiektów żądań i odpowiedzi. W tym przypadku obiekt odpowiedzi używany do ustawiania treść odpowiedzi, który został wysłany do klienta. Aby uzyskać więcej informacji, zobacz [serwera usługi Azure Cosmos DB JavaScript dokumentacji zestawu SDK](https://azure.github.io/azure-cosmosdb-js-server/).  

Daj nam rozwinąć w tym przykładzie i dodać więcej funkcji związanych z bazy danych do procedury składowanej. Procedury składowane można utworzyć, zaktualizować, odczytu, zapytania i usuwania dokumentów i załączników w kolekcji.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Przykład: Napisać procedurę przechowywaną, aby utworzyć dokument
Następny fragment kodu pokazuje, jak użyć obiektu context do interakcji z zasobami usługi Cosmos DB.

```javascript
var createDocumentStoredProc = {
    id: "createMyDocument",
    serverScript: function createMyDocument(documentToCreate) {
        var context = getContext();
        var collection = context.getCollection();

        var accepted = collection.createDocument(collection.getSelfLink(),
              documentToCreate,
              function (err, documentCreated) {
                  if (err) throw new Error('Error' + err.message);
                  context.getResponse().setBody(documentCreated.id)
              });
        if (!accepted) return;
    }
}
```


Tę procedurę składowaną przyjmuje jako danych wejściowych documentToCreate, treść dokumentu w bieżącej kolekcji. Operacje takie są asynchroniczne i są zależne od wywołania zwrotne funkcji języka JavaScript. Funkcja wywołania zwrotnego zawiera dwa parametry: jeden dla obiektu błędu w przypadku, gdy kończy się niepowodzeniem i jeden dla utworzonego obiektu. Wewnątrz wywołania zwrotnego użytkownicy mogą obsłużyć wyjątek albo zgłosić błąd. W przypadku, gdy wywołanie zwrotne nie zostanie podany, i występuje błąd, środowisko uruchomieniowe usługi Azure Cosmos DB zgłasza błąd.   

W powyższym przykładzie wywołanie zwrotne zgłasza błąd, jeśli operacja nie powiodła się. W przeciwnym wypadku Ustawia identyfikator utworzonego dokumentu jako treść odpowiedzi do klienta. Oto, jak ta procedura składowana jest wykonywany z parametrami wejściowymi.

```javascript
// register the stored procedure
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;

        // run stored procedure to create a document
        var docToCreate = {
            id: "DocFromSproc",
            book: "The Hitchhiker’s Guide to the Galaxy",
            author: "Douglas Adams"
        };

        return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
              docToCreate);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response); // "DocFromSproc"
}, function (error) {
    console.log("Error", error);
});
```

Tę procedurę składowaną można zmodyfikować w taki sposób, aby pobrać tablicę jako dane wejściowe treści dokumentu i utworzyć je wszystkie w tym samym wykonywanie procedury składowanej zamiast wielu żądań w celu utworzenia każdego z nich osobno. Tę procedurę składowaną może służyć do implementowania importera wydajne zbiorcze usługi Cosmos DB (zostało to omówione w dalszej części tego samouczka).   

Opisywanym przykładzie pokazano sposób użycia procedur składowanych. Następnie dowiesz się o wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) w dalszej części tego samouczka.

### <a name="known-issues"></a>Znane problemy

Podczas definiowania procedury składowanej przy użyciu witryny Azure portal, parametry wejściowe są zawsze wysyłane jako ciąg znaków do procedury składowanej. Nawet wtedy, gdy możesz przekazać tablicę ciągów jako dane wejściowe, tablica jest konwertowana na ciąg i wysyłane do procedury składowanej. Aby obejść ten problem, można zdefiniować funkcję w swojej przechowywanej procedurze, aby przeanalizować ciąg jako tablica. Poniższy kod jest przykładem, aby przeanalizować ciąg jako tablica: 

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>Transakcje programu bazy danych
Transakcji w typowej bazy danych można zdefiniować za pomocą sekwencji operacji wykonywanych jako pojedynczą jednostką logiczną pracy. Każda transakcja zapewnia **kwas gwarantuje**. ACID jest dobrze znanym akronimem, zawiera cztery właściwości - niepodzielności, spójności, izolacji i trwałości.  

Krótko mówiąc, niepodzielność gwarantuje, że wszystkie prace wykonane w transakcji jest traktowany jako pojedyncza jednostka gdzie albo dba o wszystkie lub żadne. Spójności gwarantuje, że dane są zawsze w dobrym stanie wewnętrznego na różnych transakcji. Izolacja gwarantuje, że żadne dwa transakcje kolidują ze sobą — ogólnie, większość komercyjnych systemów udostępniają wiele poziomy izolacji, które mogą służyć odpowiednio do potrzeb aplikacji. Trwałość gwarantuje żadnych zmian, które jest zaangażowana w bazie danych zawsze będzie istnieć.   

W usłudze Cosmos DB JavaScript znajduje się w tej samej przestrzeni pamięci jako bazy danych. W związku z tym żądania są wykonywane w ramach procedury składowane i wyzwalacze są wykonywane w tym samym zakresie sesji bazy danych. Ta funkcja umożliwia Cosmos DB gwarantuje ACID dla wszystkich operacji, które są częścią jednego procedury składowanej/wyzwalacza. Należy wziąć pod uwagę poniższą definicję procedura składowana:

```javascript
// JavaScript source code
var exchangeItemsSproc = {
    id: "exchangeItems",
    serverScript: function (playerId1, playerId2) {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        var player1Document, player2Document;

        // query for players
        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
            function (err, documents, responseOptions) {
                if (err) throw new Error("Error" + err.message);

                if (documents.length != 1) throw "Unable to find both names";
                player1Document = documents[0];

                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                    function (err2, documents2, responseOptions2) {
                        if (err2) throw new Error("Error" + err2.message);
                        if (documents2.length != 1) throw "Unable to find both names";
                        player2Document = documents2[0];
                        swapItems(player1Document, player2Document);
                        return;
                    });
                if (!accept2) throw "Unable to read player details, abort ";
            });

        if (!accept) throw "Unable to read player details, abort ";

        // swap the two players’ items
        function swapItems(player1, player2) {
            var player1ItemSave = player1.item;
            player1.item = player2.item;
            player2.item = player1ItemSave;

            var accept = collection.replaceDocument(player1._self, player1,
                function (err, docReplaced) {
                    if (err) throw "Unable to update player 1, abort ";

                    var accept2 = collection.replaceDocument(player2._self, player2,
                        function (err2, docReplaced2) {
                            if (err) throw "Unable to update player 2, abort"
                        });

                    if (!accept2) throw "Unable to update player 2, abort";
                });

            if (!accept) throw "Unable to update player 1, abort";
        }
    }
}

// register the stored procedure in Node.js client
client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;
    }
);
```

Tę procedurę składowaną używa transakcji w ramach aplikacji gry do elementów kompromis między dwie gracze w ramach jednej operacji. Procedura składowana podejmuje próbę odczytu dwa dokumenty, które każdy odpowiadający identyfikatory player przekazanego jako argument. Jeśli oba dokumenty player zostaną znalezione, a następnie procedurę składowaną aktualizuje dokumenty przez zamianę ich elementów. Jeśli nie zostaną napotkane błędy po drodze, zgłasza wyjątek języka JavaScript, który niejawnie przerywa transakcję.

Jeśli jest zarejestrowany w kolekcji procedury składowanej względem to kolekcja jednej partycji, a następnie transakcja obejmuje wszystkie dokumenty w kolekcji. Jeśli kolekcja jest partycjonowana, procedury składowane są wykonywane w zakresie transakcji pojedynczego klucza partycji. Każdy składowanych, wykonywanie procedury następnie musi zawierać wartość klucza partycji, odpowiadający zakresu transakcji musi być uruchamiana. Aby uzyskać więcej informacji, zobacz [usługi Azure Cosmos DB partycjonowanie](partition-data.md).

### <a name="commit-and-rollback"></a>Zatwierdź i wycofywania
Transakcje są głęboko i natywnie zintegrowane w modelu programowania języka JavaScript usługi Cosmos DB. Wewnątrz funkcji JavaScript wszystkie operacje są automatycznie opakowane w pojedynczą transakcję. Jeśli kod JavaScript zakończy się bez żadnych wyjątków, operacji w bazie danych dokłada wszelkich starań. W efekcie instrukcji "BEGIN TRANSACTION" i "COMMIT TRANSACTION" w relacyjnych baz danych są niejawne w usłudze Cosmos DB.  

W przypadku każdego wyjątku, który jest propagowany ze skryptu środowiska uruchomieniowego JavaScript usługi Cosmos DB będzie wycofać cała transakcja. Jak pokazano we wcześniejszym przykładzie, zostanie zgłoszony wyjątek jest skutecznie równoważne "ROLLBACK TRANSACTION" w usłudze Cosmos DB.

### <a name="data-consistency"></a>Spójność danych
Procedury składowane i wyzwalacze są zawsze wykonywane w replice podstawowej kontenera usługi Azure Cosmos DB. Daje to gwarancję, że odczyty wewnątrz przechowywane procedury oferty silnej spójności. Zapytania przy użyciu funkcji zdefiniowanych przez użytkownika mogą być wykonywane na podstawowe lub pomocnicze repliki, ale upewnij się, aby spełnić poziomu spójności żądanego, wybierając odpowiednie repliki.

## <a name="bounded-execution"></a>Wykonanie ograniczonego
Wszystkie operacje usługi Cosmos DB, należy wykonać w ramach serwera określony limit czasu żądania. To ograniczenie dotyczy także funkcje języka JavaScript (procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika). Jeśli operacja nie została zakończona z limitem czasu, transakcja zostanie wycofana. Funkcje języka JavaScript należy zakończyć przed upływem limitu czasu lub wdrożenia modelu na podstawie kontynuacji do przetwarzania wsadowego/wznowień wykonywania.  

Aby uprościć tworzenie procedur składowanych i wyzwalaczy, aby obsłużyć limity czasu, wszystkie funkcje w obiekcie kolekcji (tworzenia, odczytu, Zastąp i usuwanie dokumentów i załączników) wartość logiczną wartość zwracana reprezentująca czy tej operacji zostanie ukończona. Jeśli ta wartość wynosi false, jest wskazanie, że limit czasu niedługo wygaśnie i czy procedura musi zawinąć wykonywania.  Operacje w kolejce przed pierwszą operacją niezaakceptowanych magazynu są gwarantowane do wykonania, jeśli procedura składowana kończy w czasie, a nie umieszcza w kolejce kolejnych żądań.  

Funkcje języka JavaScript są również ograniczone na zużycie zasobów. Usługa cosmos DB rezerwuje przepływność dla kolekcji lub zestaw kontenerów. Przepływność jest wyrażona w jednostkach znormalizowanych procesora CPU, pamięci i we/wy zużycia jednostek żądania lub jednostek RU. Funkcje języka JavaScript potencjalnie może zużyć dużą liczbę jednostek żądań w krótkim czasie i mogą się pojawiać limited współczynnik w przypadku osiągnięcia limitu kolekcji. Procedury składowane dużej ilości zasobów może również zostaną poddane kwarantannie, aby zapewnić dostępność operacji bazy danych pierwotnych.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Przykład: Zbiorcze importowanie danych do programu bazy danych
Poniżej przedstawiono przykład procedury składowanej, który jest zapisywany w celu grupowego importu dokumenty w kolekcji. Należy zauważyć, jak procedura składowana obsługuje wykonywanie ograniczonego, sprawdzając wartość logiczna zwróć wartość z createDocument, a następnie używa liczbę dokumentów wstawione w każdym wywołaniu procedury składowanej do śledzenia i wznowić postęp w partiach.

```javascript
function bulkImport(docs) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();

    // The count of imported docs, also used as current doc index.
    var count = 0;

    // Validate input.
    if (!docs) throw new Error("The array is undefined or null.");

    var docsLength = docs.length;
    if (docsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create a document.
    tryCreate(docs[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted. 
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called docs.length times.
    //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(doc, callback) {
        var isAccepted = collection.createDocument(collectionLink, doc, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client, 
        // which will call the script again with remaining set of docs.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when collection.createDocument is done in order to process the result.
    function callback(err, doc, options) {
        if (err) throw err;

        // One more document has been inserted, increment the count.
        count++;

        if (count >= docsLength) {
            // If we created all documents, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(docs[count], callback);
        }
    }
}
```

## <a id="trigger"></a> Wyzwalacze bazy danych
### <a name="database-pre-triggers"></a>Wyzwalacze wstępne bazy danych
Usługa cosmos DB udostępnia wyzwalacze, które są wykonywane lub wyzwolone przez operacji na dokumencie. Na przykład można określić wstępne wyzwalacza, podczas tworzenia dokumentu — wstępne wyzwalacz zostanie uruchomiony, przed utworzeniem dokumentu. Poniższy przykład pokazuje, jak wyzwalacze wstępnej może służyć do sprawdzania poprawności właściwości dokumentu, który jest tworzony:

```javascript
var validateDocumentContentsTrigger = {
    id: "validateDocumentContents",
    serverScript: function validate() {
        var context = getContext();
        var request = context.getRequest();

        // document to be created in the current operation
        var documentToCreate = request.getBody();

        // validate properties
        if (!("timestamp" in documentToCreate)) {
            var ts = new Date();
            documentToCreate["my timestamp"] = ts.getTime();
        }

        // update the document that will be created
        request.setBody(documentToCreate);
    },
    triggerType: TriggerType.Pre,
    triggerOperation: TriggerOperation.Create
}
```

I odpowiedni kod rejestracji klienta Node.js wyzwalacza:

```javascript
// register pre-trigger
client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
    .then(function (response) {
        console.log("Created", response.resource);
        var docToCreate = {
            id: "DocWithTrigger",
            event: "Error",
            source: "Network outage"
        };

        // run trigger while creating above document 
        var options = { preTriggerInclude: "validateDocumentContents" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response.resource); // document with timestamp property added
}, function (error) {
    console.log("Error", error);
});
```

Wstępne wyzwalaczy nie może mieć parametrów wejściowych. Obiekt żądania może służyć do manipulowania komunikat żądania skojarzony z operacją. W tym miejscu przed wyzwalacz jest uruchamiany z tworzeniem dokumentu, a treść żądania zawiera dokument, który ma zostać utworzony w formacie JSON.   

Jeśli zarejestrowano wyzwalaczami, użytkownicy mogą określić operacje, które można uruchomić z. Ten wyzwalacz został utworzony z TriggerOperation.Create, co oznacza, że w ramach operacji zamieniania, jak pokazano w poniższym kodzie przy użyciu wyzwalacza jest niedozwolone.

```javascript
var options = { preTriggerInclude: "validateDocumentContents" };

client.replaceDocumentAsync(docToReplace.self,
              newDocBody, options)
.then(function (response) {
    console.log(response.resource);
}, function (error) {
    console.log("Error", error);
});

// Fails, can’t use a create trigger in a replace operation
```
### <a name="database-post-triggers"></a>Wyzwalacze po bazy danych
Po wyzwalacze, takich jak wstępnego wyzwalacze są skojarzone z operacji na dokumencie i nie przyjmuje żadnych parametrów wejściowych. Zakres ich działania **po** operacja została zakończona i mieć dostęp do komunikatu odpowiedzi, które są wysyłane do klienta.   

Poniższy przykład przedstawia po wyzwalaczy w akcji:
```javascript
var updateMetadataTrigger = {
    id: "updateMetadata",
    serverScript: function updateMetadata() {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        // document that was created
        var createdDocument = response.getBody();

        // query for metadata document
        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
            updateMetadataCallback);
        if(!accept) throw "Unable to update metadata, abort";

        function updateMetadataCallback(err, documents, responseOptions) {
            if(err) throw new Error("Error" + err.message);
                     if(documents.length != 1) throw 'Unable to find metadata document';

                     var metadataDocument = documents[0];

                     // update metadata
                     metadataDocument.createdDocuments += 1;
                     metadataDocument.createdNames += " " + createdDocument.id;
                     var accept = collection.replaceDocument(metadataDocument._self,
                           metadataDocument, function(err, docReplaced) {
                                  if(err) throw "Unable to update metadata, abort";
                           });
                     if(!accept) throw "Unable to update metadata, abort";
                     return;                    
        }                                                                                            
    },
    triggerType: TriggerType.Post,
    triggerOperation: TriggerOperation.All
}

```
Mogą być rejestrowane wyzwalacza, jak pokazano w następującym przykładzie.
```javascript
// register post-trigger
client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
    .then(function(createdTrigger) { 
        var docToCreate = { 
            name: "artist_profile_1023",
            artist: "The Band",
            albums: ["Hellujah", "Rotators", "Spinning Top"]
        };

        // run trigger while creating above document 
        var options = { postTriggerInclude: "updateMetadata" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

Ten wyzwalacz zapytań dla dokumentów metadanych i aktualizuje go ze szczegółowymi informacjami o nowo utworzonego dokumentu.  

Warto pamiętać, jest **transakcyjnych** wykonania wyzwalaczy w usłudze Cosmos DB. Ten po wyzwalacz jest uruchamiany w ramach tej samej transakcji, ponieważ utworzenie oryginalnego dokumentu. W związku z tym Jeśli zgłoszenie wyjątku z po wyzwalacza (np. Jeśli nie można zaktualizować dokument metadanych), cała transakcja zakończy się niepowodzeniem i wycofana. Żaden dokument zostanie utworzona i zostanie zwrócony wyjątek.  

## <a id="udf"></a>Funkcje zdefiniowane przez użytkownika
Funkcje zdefiniowane przez użytkownika (UDF) są używane do rozszerzania gramatyki języka zapytania SQL usługi Azure Cosmos DB i implementuje niestandardową logikę biznesową. Tylko może zostać wywołana z wewnątrz zapytania. One nie mają dostępu do obiektu kontekstu i są przeznaczone do służyć jako tylko do obliczeń języka JavaScript. W związku z tym funkcje zdefiniowane przez użytkownika może działać w replikach pomocniczych usługi Cosmos DB.  

Poniższy przykład tworzy funkcji zdefiniowanej przez użytkownika, aby obliczyć podatku oparte na stawkach dla różnych dochodu nawiasów klamrowych i używa go w zapytaniu można znaleźć wszystkich użytkowników, którzy zapłacili więcej niż 20 000 w podatków.

```javascript
var taxUdf = {
    id: "tax",
    serverScript: function tax(income) {

        if(income == undefined) 
            throw 'no input';

        if (income < 1000) 
            return income * 0.1;
        else if (income < 10000) 
            return income * 0.2;
        else
            return income * 0.4;
    }
}
```

Następnie można funkcji zdefiniowanej przez użytkownika w zapytaniach, podobnie jak w następującym przykładzie:

```javascript
// register UDF
client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
    .then(function(response) { 
        console.log("Created", response.resource);

        var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
        return client.queryDocuments('dbs/testdb/colls/testColl',
               query).toArrayAsync();
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    var documents = response.feed;
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

## <a name="javascript-language-integrated-query-api"></a>Zapytanie o języku zintegrowanym JavaScript API
Oprócz wysyłania zapytań przy użyciu Gramatyka języka SQL w usłudze Azure Cosmos DB zestawu SDK po stronie serwera umożliwia wykonywanie zoptymalizowane zapytania przy użyciu fluent interfejsu języka JavaScript, bez konieczności znajomości języka SQL. Zapytanie języka JavaScript, które interfejs API umożliwia programowe tworzenie zapytań, przekazując predykatu funkcje do funkcji chainable wywołuje składnią znane elementy tablicy wbudowane i popularne biblioteki JavaScript, takich jak Lodash ECMAScript5 firmy. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript do wykonania wydajnie za pomocą indeksów usługi Azure Cosmos DB.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem, aby `getContext().getCollection()`.
> <br/>
> Innymi słowy, możesz użyć `__` lub `getContext().getCollection()` kwerendy JavaScript API dostępu do.
> 
> 

Obsługiwane funkcje obejmują:

<ul>
<li>
<b>Chain().... wartość ([wywołania zwrotnego] [, opcje])</b>
<ul>
<li>
Łańcuchowe wywołanie, które musi być zakończona zaczyna się od value().
</li>
</ul>
</li>
<li>
<b>Filtr (predicateFunction [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Służy do przefiltrowania przy użyciu funkcji predykatu, który zwraca wartość PRAWDA/FAŁSZ, aby przefiltrować wchodzącym/wychodzącym dokumentów wejściowych do Wynikowy zestaw danych wejściowych. Ta funkcja działa podobnie do klauzuli WHERE w języku SQL.
</li>
</ul>
</li>
<li>
<b>Mapa (transformationFunction [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Stosuje projekcji, biorąc pod uwagę funkcja transformacji, która mapuje każdego elementu wejściowego obiektu JavaScript lub wartość. Ta funkcja działa podobnie do klauzuli SELECT w tabeli SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Ta funkcja jest skrót mapę, która wyodrębnia wartość właściwości jednego z każdego elementu wejściowego.
</li>
</ul>
</li>
<li>
<b>spłaszczanie ([isShallow] [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Łączy i spłaszcza tablic z każdego elementu wejściowego w jednej tablicy. Ta funkcja działa podobnie jak SelectMany w składniku LINQ.
</li>
</ul>
</li>
<li>
<b>sortby — ([predykatu] [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Generuje nowy zestaw dokumentów, sortując dokumentów w usłudze stream dokument wejściowy rosnąco przy użyciu podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY w języku SQL.
</li>
</ul>
</li>
<li>
<b>sortbydescending — ([predykatu] [, opcje] [, wywołanie zwrotne])</b>
<ul>
<li>
Generuje nowy zestaw dokumentów, sortując dokumentów w usłudze stream dokument wejściowy w porządku malejącym przy użyciu podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY x DESC w języku SQL.
</li>
</ul>
</li>
</ul>


Gdy się wewnątrz predykatu lub selektora funkcje, następujące konstrukcje JavaScript Pobierz automatycznie zoptymalizowane pod kątem uruchamiania bezpośrednio na indeksów usługi Azure Cosmos DB:

* Operatory proste: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literały, literał obiektu w tym: {}
* var zwrotu

Następujące elementy języka JavaScript nie uzyskać zoptymalizowane pod kątem indeksów usługi Azure Cosmos DB:

* Przepływ sterowania (na przykład, jeśli, podczas gdy)
* Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [JSDocs po stronie serwera](https://azure.github.io/azure-cosmosdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Przykład: Napisać procedury składowanej przy użyciu interfejsu API zapytań języka JavaScript
Poniższy przykładowy kod znajduje się przykład jak JavaScript API zapytania mogą być używane w kontekście procedury składowanej. Procedura składowana wstawia dokumentu, określone przez parametr wejściowy, a metadane aktualizacji dokumentów, używając `__.filter()` metoda minSize, maxSize i totalSize na podstawie właściwości rozmiaru dokument wejściowy.

```javascript
/**
 * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
 */
function insertDocumentAndUpdateMetadata(doc) {
  // HTTP error codes sent to our callback funciton by DocDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
    if (err) throw err;

    // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
    if (!doc.isMetadata && doc.size > 0) {
      // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata doc was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

        // The metadata document.
        var metaDoc = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
        else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

        // Update metaDoc.maxSize.
        metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

        // Update metaDoc.totalSize.
        metaDoc.totalSize += doc.size;

        // Update/replace the metadata document in the store.
        var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
}
```

## <a name="sql-to-javascript-cheat-sheet"></a>SQL do ściągawka dotycząca języka Javascript
W poniższej tabeli przedstawiono różne zapytania SQL i odpowiednie zapytania języka JavaScript.

Za pomocą zapytań SQL dokumentu klucze właściwości (na przykład `doc.id`) jest rozróżniana wielkość liter.

|SQL| Zapytanie JavaScript API|Poniższy opis|
|---|---|---|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH| __.map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc;<br>});|1|
|Wybierz docs.id, docs.message jako msg, docs.actions <br>DOKUMENTACH|__.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|WYBIERZ POZYCJĘ *<br>DOKUMENTACH<br>GDZIE ARRAY_CONTAINS (docs. Tagi, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4|
|Wybierz docs.id, docs.message jako msg<br>DOKUMENTACH<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;komunikat: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|Wybierz wartość tagu<br>DOKUMENTACH<br>Dołącz do dokumentów w tagu. Tagi<br>Klauzula ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć dokumentu. Tagi & & Array.isArray (doc. Znaczniki);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

Poniższe opisy wyjaśnienia każdego zapytania w powyższej tabeli.
1. To powoduje wszystkie dokumenty (z podziałem na strony za pomocą tokenu kontynuacji) jako.
2. Projekty identyfikator, komunikat (alias do msg) i akcji z wszystkich dokumentów.
3. Zapytania dotyczące dokumentów za pomocą predykat: identyfikator = "X998_Y998".
4. Zapytania dotyczące dokumentów, które mają tagi i właściwość Tags jest tablicą, zawierającego wartość 123.
5. Zapytania dotyczące dokumentów z predykatem, id = "X998_Y998", a następnie projekty identyfikatora i komunikatu (alias do komunikatu).
6. Filtry dla dokumentów, których właściwości tablicy, tagi, i sortuje wynikowy dokumenty _ts sygnatura czasowa systemu właściwości, a następnie projekty + spłaszcza tablica tagów.


## <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego
Azure Cosmos DB [interfejsu API po stronie serwera języka JavaScript](https://azure.github.io/azure-cosmosdb-js-server/) zapewnia obsługę najbardziej typowe funkcje języka JavaScript jako standardowy przez [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Bezpieczeństwo
Procedury składowane JavaScript i wyzwalacze są w trybie piaskownicy tak, aby skutki jednego skryptu w nie nastąpił przeciek do drugiego bez pośrednictwa izolacji transakcji migawki na poziomie bazy danych. Środowiska uruchomieniowe w puli, ale oczyszczone kontekstu po każdym uruchomieniu. Dlatego mają gwarancję, że bezpieczne z dowolnego wystąpienie niezamierzonych skutków ubocznych od siebie nawzajem.

### <a name="pre-compilation"></a>Prekompilowanie
Procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika są niejawnie wstępnie skompilowane do formatu kod bajtowy, aby uniknąć kosztów kompilacji w czasie wywołania każdego skryptu. Wstępna kompilacja zapewnia wywołania procedur składowanych jest szybkie i mają niewielki rozmiar.

## <a name="client-sdk-support"></a>Obsługa zestawu SDK klienta
Oprócz usługi Azure Cosmos DB [Node.js](sql-api-sdk-node.md) interfejsu API, usługa Azure Cosmos DB ma [.NET](sql-api-sdk-dotnet.md), [platformy .NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [języka JavaScript ](sql-api-sdk-node.md), i [Python SDK](sql-api-sdk-python.md) dla interfejsu API SQL. Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika mogą być tworzone i wykonywane przy użyciu dowolnej z tych zestawów SDK oraz. Poniższy przykład przedstawia sposób tworzenia i wykonywanie procedury przechowywanej za pomocą klienta platformy .NET. Należy zauważyć, jak przekazany do procedury składowanej w formacie JSON i odczytywania typów .NET.

```javascript
var markAntiquesSproc = new StoredProcedure
{
    Id = "ValidateDocumentAge",
    Body = @"
            function(docToCreate, antiqueYear) {
                var collection = getContext().getCollection();    
                var response = getContext().getResponse();    

                if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                    docToCreate.antique = true;
                }

                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                    function(err, docCreated, options) { 
                        if(err) throw new Error('Error while creating document: ' + err.message);                              
                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                        response.setBody(docCreated);
                });
         }"
};

// register stored procedure
StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
dynamic document = new Document() { Id = "Borges_112" };
document.Title = "Aleph";
document.Year = 1949;

// execute stored procedure
Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);
```

Ten przykład ilustruje sposób używania [interfejsie SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) do utworzenia wstępnego wyzwalacza, a następnie utwórz dokument z wyzwalaczem włączone. 

```javascript
Trigger preTrigger = new Trigger()
{
    Id = "CapitalizeName",
    Body = @"function() {
        var item = getContext().getRequest().getBody();
        item.id = item.id.toUpperCase();
        getContext().getRequest().setBody(item);
    }",
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};

Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
    new RequestOptions
    {
        PreTriggerInclude = new List<string> { "CapitalizeName" },
    });
```

I poniższy przykład pokazuje, jak utworzyć funkcję zdefiniowaną przez użytkownika (UDF) i użyć go w [zapytania SQL](sql-api-sql-query.md).

```javascript
UserDefinedFunction function = new UserDefinedFunction()
{
    Id = "LOWER",
    Body = @"function(input) 
    {
        return input.toLowerCase();
    }"
};

foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
{
    Console.WriteLine("Read {0} from query", book);
}
```

## <a name="rest-api"></a>Interfejs API REST
Wszystkie operacje usługi Azure Cosmos DB można wykonać w sposób RESTful. Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika można zarejestrować w kolekcji za pomocą żądania HTTP POST. Poniższy przykład pokazuje, jak zarejestrować procedury składowanej:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Procedura składowana jest zarejestrowany, wykonując żądanie POST względem identyfikatora URI baz danych/testdb/colls/testColl/sprocs o treści zawierającej procedura składowana do utworzenia. Wyzwalacze i funkcje zdefiniowane przez użytkownika można zarejestrować podobnie, wysyłając WPIS przed/wyzwalacze i /udfs odpowiednio.
To przechowywane procedury może następnie być wykonywane przez wysłanie żądania POST względem jego link do zasobu:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


W tym miejscu dane wejściowe procedury składowanej jest przekazywany w treści żądania. Dane wejściowe są przekazywane jako tablica JSON parametrów wejściowych. Procedura składowana przyjmuje pierwszy element danych wejściowych jako dokument, który jest treść odpowiedzi. Odpowiedzi, które otrzymujesz, jest następujący:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: 'Autumn of the Patriarch',
      id: 'V7tQANV3rAkDAAAAAAAAAA==',
      ts: 1407830727,
      self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
      etag: '6c006596-0000-0000-0000-53e9cac70000',
      attachments: 'attachments/',
      Price: 200
    }


Wyzwalacze, w przeciwieństwie do procedur przechowywanych, nie można wykonać bezpośrednio. Zamiast tego są one wykonywane w ramach operacji na dokumencie. Można określić wyzwalaczy do uruchomienia przy użyciu żądania przy użyciu nagłówków HTTP. Poniższy kod przedstawia żądanie utworzenia dokumentu.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger

    {
       "name": "newDocument",
       "title": "The Wizard of Oz",
       "author": "Frank Baum",
       "pages": 92
    }


W tym miejscu przed wyzwalacza do uruchamiania z tym żądaniem jest określony w nagłówku x-ms-documentdb-pre-trigger-include. Odpowiednio żadne po wyzwalacze są podane w nagłówku x-ms-documentdb-post-trigger-include. Przed i po wyzwalaczy może być określony dla danego żądania.

## <a name="sample-code"></a>Przykładowy kod
Można znaleźć więcej przykładów kodu po stronie serwera (w tym [zbiorczego usuwania](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/bulkDelete.js), i [aktualizacji](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/update.js)) w [repozytorium GitHub](https://github.com/Azure/azure-cosmosdb-js-server/tree/master/samples).

Chcesz udostępnić swoje awesome procedury składowanej? przyczynia się do repozytorium i Utwórz żądanie ściągnięcia. 

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu co najmniej jeden procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika, utworzony, możesz załadować je i wyświetlać je w witrynie Azure portal, za pomocą Eksploratora danych.

Można również znaleźć następujące informacje i zasoby przydatne w ścieżce w taki sposób, aby dowiedzieć się więcej na temat programowanie po stronie serwera usługi Azure Cosmos dB:

* [Zestawy SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Studio bazy danych DocumentDB](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Rozszerzalność bezpieczne i przenośnej bazy danych](http://dl.acm.org/citation.cfm?id=276339) 
* [Usługa zorientowanej na architekturę bazy danych](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hostowanie środowiska uruchomieniowego .NET, programie Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
