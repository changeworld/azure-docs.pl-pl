---
title: Programowanie JavaScript po stronie serwera dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć bazy danych rozwiązania Cosmos platformy Azure, aby zapisać procedur składowanych, wyzwalaczy bazy danych i funkcji zdefiniowanych przez użytkownika (UDF) w języku JavaScript. Pobierz porady programing bazy danych i inne.
keywords: Bazy danych wyzwalacze, procedury składowanej, procedury składowanej, program bazy danych, sproc, azure, platformy Microsoft azure
services: cosmos-db
documentationcenter: ''
author: aliuy
manager: kfile
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: e6fd51cb2550549e14934c3f4774a40d42281247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programowanie po stronie serwera w usłudze Azure DB rozwiązania Cosmos: procedury składowane, wyzwalacze bazy danych i funkcji UDF

Dowiedz się, jak Azure rozwiązania Cosmos DB języku zintegrowanym, transakcyjne wykonywanie JavaScript umożliwia deweloperom pisanie **procedur składowanych**, **wyzwalaczy**, i **funkcje zdefiniowane przez użytkownika (UDF)**  natywnie w [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. Integracja języka JavaScript umożliwia pisanie logiki programu, które mogą być dostarczane i wykonywane bezpośrednio w ramach partycji magazynu bazy danych. 

Zalecamy rozpoczęcie pracy od obejrzenia poniższego klipu wideo, w którym Andrew Liu zawiera wprowadzenie do modelu programowania po stronie serwera bazy danych DB rozwiązania Cosmos Azure. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Następnie wróć do tego artykułu, którym poznasz odpowiedzi na następujące pytania:  

* Jak zapisać procedury składowanej, wyzwalacza lub funkcji zdefiniowanej przez użytkownika przy użyciu języka JavaScript?
* Sposób rozwiązania Cosmos DB zagwarantować kwasu?
* Jak działają transakcji w bazie danych rozwiązania Cosmos
* Co to są wstępnie wyzwala i jest wyzwalane po i jak jedną zapisać?
* Jak zarejestrować i wykonać procedury przechowywanej, wyzwalacza lub funkcji zdefiniowanej przez użytkownika w sposób RESTful za pośrednictwem protokołu HTTP?
* Jakie są dostępne na tworzenie i wykonywanie zestawów SDK DB rozwiązania Cosmos przechowywane procedury, wyzwalaczy i funkcji UDF?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Wprowadzenie do procedury składowanej i programowania funkcji zdefiniowanej przez użytkownika
To podejście *"JavaScript jako nowoczesnego dzień T-SQL"* zwalnia deweloperzy aplikacji od złożoności niezgodności typu systemu i technologii mapowania relacyjnego obiektu. Ponadto wprowadzono numer wewnętrzny korzyści, które mogą zostać użyte do tworzenia rozbudowanych aplikacji:  

* **Logika proceduralna:** JavaScript jako języka programowania wysokiego poziomu, udostępnia interfejs zaawansowanych, znanych Express logiki biznesowej. Można wykonywać złożonych sekwencji operacji bliżej do danych.
* **Niepodzielne transakcji:** gwarancje rozwiązania Cosmos bazy danych, które bazy danych operacji wewnątrz jednej procedury składowanej lub wyzwalacza są atomic. Ta funkcjonalność atomic pozwala aplikacji łączy pokrewnych operacje w pojedynczej partii, dzięki czemu wszystkie z nich poprawne albo żadna z nich powiodło się. 
* **Wydajność:** fakt, że JSON leżą jest mapowany na system typów języka Javascript, a także to podstawowa jednostka przestrzeni dyskowej w bazie danych rozwiązania Cosmos umożliwia kilka optymalizacji, takich jak opóźnieniem materialization dokumentów JSON w puli buforów i udostępnia je na żądanie dostępne do wykonywania kodu. Istnieje więcej korzyści wydajności związanych z wysyłanie logiki biznesowej w bazie danych:
  
  * Przetwarzanie wsadowe — deweloperzy mogą grupy operacje, takie jak wstawia i przesyłanie ich zbiorczo. Koszt opóźnienia ruchu sieciowego i obciążenie magazynu można utworzyć oddzielne transakcje zmniejszone. 
  * Wstępna kompilacja — rozwiązania Cosmos DB precompiles procedur składowanych, wyzwalaczy i zdefiniowanych przez użytkownika funkcji (UDF) w celu uniknięcia koszt kompilacji języka JavaScript dla każdego wywołania. Koszty tworzenia kod bajtowy procedurach logiki jest amortyzowanego do minimalnej wartości.
  * Sekwencjonowanie — wiele operacji potrzeby efekt uboczny ("wyzwalacza") która potencjalnie obejmuje wykonanie jednej lub wielu operacji dodatkowej magazynu. Jako uzupełnienie niepodzielność to wydajność więcej po przeniesieniu do serwera. 
* **Hermetyzacja:** zapisane procedury może służyć do grupowania logiki biznesowej w jednym miejscu, który ma dwie zalety:
  * Dodaje warstwy abstrakcji na pierwotnych danych, co umożliwia architektów danych podlegać ewolucji swoich aplikacji niezależnie od danych. Ta warstwa abstrakcji jest korzystne w przypadku, gdy dane przynależy schematu, z powodu łamliwa założeń, które może być konieczne rozszerzania do aplikacji, jeśli mają dotyczyć dane bezpośrednio.  
  * Ta warstwa abstrakcji umożliwia przedsiębiorstwom chronić swoje dane usprawnienie dostęp ze skryptów.  

Tworzenie i wykonywanie wyzwalaczy bazy danych, procedury składowane i operatory zapytań niestandardowych jest obsługiwana przez [portalu Azure](https://portal.azure.com), [interfejsu API REST](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), i [zestawów SDK klienta](sql-api-sdk-dotnet.md) na wielu platformach, w tym .NET, Node.js oraz JavaScript.

W tym samouczku używana [Node.js SDK z ze zobowiązania Q](http://azure.github.io/azure-documentdb-node-q/) w celu zilustrowania składni i użycia procedur składowanych, wyzwalaczy i funkcji UDF.   

## <a name="stored-procedures"></a>Procedury składowane
### <a name="example-write-a-stored-procedure"></a>Przykład: Zapisać procedury składowanej
Zacznijmy od prostego procedury przechowywanej, która zwraca odpowiedź "Hello World".

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Procedury składowane są rejestrowane w jednej kolekcji i może działać na dokumentów i załączników w tej kolekcji. Poniższy fragment kodu pokazano, jak zarejestrować procedury przechowywane helloWorld z kolekcji. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Po zarejestrowaniu procedurę składowaną można ją wykonać kolekcji i przeczytaj wyniki powrotem po stronie klienta. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


Obiekt kontekstu zapewnia dostęp do wszystkich operacji, które mogą być wykonywane w magazynie rozwiązania Cosmos bazy danych, a także dostęp do obiektów żądania i odpowiedzi. W takim przypadku obiekt odpowiedzi używany do ustawiania treści odpowiedzi, który został wysłany do klienta. Aby uzyskać więcej informacji, zobacz [serwera Azure rozwiązania Cosmos DB JavaScript dokumentacji zestawu SDK](http://azure.github.io/azure-documentdb-js-server/).  

Daj nam rozwiń w tym przykładzie i dodać więcej funkcji związanych z bazy danych do procedury składowanej. Procedury składowane można utworzyć, zaktualizować, odczytu, zapytań i usuwania dokumentów i załączników w kolekcji.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Przykład: Pisanie procedurę składowaną, aby utworzyć dokument
Następny fragment kodu przedstawia sposób użycia obiekt kontekstu do interakcji z zasobami DB rozwiązania Cosmos.

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


Tę procedurę składowaną przyjmuje jako documentToCreate wejściowych, treść dokumentu, który ma zostać utworzony w bieżącej kolekcji. Wszystkie operacje są asynchroniczne i zależą od wywołania zwrotne funkcji JavaScript. Funkcja wywołania zwrotnego zawiera dwa parametry: jeden dla obiekt błędu w przypadku, gdy kończy się niepowodzeniem i jeden dla utworzonego obiektu. Wewnątrz wywołania zwrotnego użytkownicy mogą obsłużyć wyjątek albo zgłosić błąd. W przypadku, gdy wywołanie zwrotne nie zostanie podana, i występuje błąd, środowisko uruchomieniowe bazy danych Azure rozwiązania Cosmos zgłasza błąd.   

W powyższym przykładzie wywołanie zwrotne zgłasza błąd, jeśli operacja nie powiodła się. W przeciwnym razie Ustawia identyfikator utworzony dokument jako treść odpowiedzi do klienta. Oto, jak wykonaniem tej procedury składowanej z parametrami wejściowymi.

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


Tę procedurę składowaną można zmodyfikować w taki sposób, aby pobrać tablicę treści dokumentu jako dane wejściowe i je utworzyć w tym samym wykonywanie procedury składowanej zamiast wielu żądań w celu utworzenia każdego z nich osobno. Tę procedurę składowaną może służyć do zaimplementowania importer zbiorczego wydajne rozwiązania Cosmos bazy danych (omówiony w dalszej części tego samouczka).   

Opisanym przykładzie przedstawiono sposób użycia procedur składowanych. Następnie dowiesz się o wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) w dalszej części tego samouczka.

## <a name="database-program-transactions"></a>Transakcji w bazie danych programu
Transakcji w typowej bazy danych można zdefiniować jako sekwencja operacji wykonywanych jako pojedyncza jednostka logiczna pracy. Udostępnia każdą transakcję **ACID gwarancje**. KWAS jest dobrze znanego akronim, zawiera cztery właściwości — niepodzielność, spójności, izolacji i trwałości.  

Krótko mówiąc, niepodzielność gwarantuje, że wszystkie pracy wykonanej w transakcji jest traktowany jako pojedyncza jednostka gdzie albo wszystkie dba lub none. Spójność upewnia się, że dane są zawsze dobrego stanu wewnętrznego w transakcji. Izolacja gwarantuje, że żadne transakcje dwóch koliduje ze sobą — ogólnie rzecz biorąc, większość komercyjnych systemów zapewniają wiele poziomów izolacji, które mogą być używane zgodnie z wymaganiami aplikacji. Trwałości zapewnia żadnych zmian, które zostaje zatwierdzona w bazie danych będą zawsze istnieje.   

W bazie danych rozwiązania Cosmos JavaScript znajduje się w tej samej przestrzeni pamięci, co baza danych. W związku z tym żądań w ramach procedur składowanych i wyzwalacze są wykonywane w tym samym zakresie sesji bazy danych. Ta funkcja umożliwia rozwiązania Cosmos bazy danych zagwarantować kwasu dla wszystkich operacji, które są częścią jednego procedury składowanej/wyzwalacza. Należy wziąć pod uwagę następujące definicji procedury składowanej:

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

Tę procedurę składowaną używa transakcji w ramach aplikacji gry do elementów handlu między dwóch graczy w ramach jednej operacji. Procedura składowana podejmuje próbę odczytania dwa dokumenty, które każdego odpowiadające player identyfikatorów przekazany jako argument. Jeśli oba dokumenty player zostaną znalezione, a następnie procedurę składowaną aktualizuje dokumenty przez zamianę ich elementów. Jeśli wzdłuż sposób nie zostaną napotkane błędy, zgłasza wyjątek JavaScript, która niejawnie przerywa transakcję.

Jeśli jest zarejestrowany w kolekcji procedury składowanej przed to kolekcja jednej partycji, a następnie transakcji obejmuje wszystkie dokumenty w kolekcji. Jeśli kolekcja jest podzielona na partycje, procedury składowane są wykonywane w zakresie transakcji klucza jednej partycji. Każdy składowanych, wykonywanie procedury następnie musi zawierać wartość klucza partycji odpowiadający zakresu transakcji musi być uruchamiana. Aby uzyskać więcej informacji, zobacz [Azure rozwiązania Cosmos DB partycjonowania](partition-data.md).

### <a name="commit-and-rollback"></a>Przekazywania i wycofywania
Transakcje są głęboko i natywnie zintegrowane model programowania JavaScript DB rozwiązania Cosmos. Wewnątrz funkcji JavaScript wszystkie operacje są automatycznie zawijany w ramach jednej transakcji. Jeśli kod JavaScript zostaje ukończona bez żadnych wyjątków, operacji w bazie danych są zatwierdzone. W efekcie instrukcje "BEGIN TRANSACTION" i "COMMIT TRANSACTION" w relacyjnych baz danych są niejawne w bazie danych rozwiązania Cosmos.  

W przypadku dowolnego wyjątek, który jest propagowana ze skryptu środowiska wykonawczego języka JavaScript DB rozwiązania Cosmos cofnie cała transakcja. Jak pokazano w przykładzie wcześniej, zgłaszanie wyjątku jest równoważne "WYCOFYWANIA transakcji" w bazie danych rozwiązania Cosmos.

### <a name="data-consistency"></a>Spójność danych
Procedury składowane i wyzwalaczy zawsze są wykonywane w replice podstawowej kontenera Azure DB rozwiązania Cosmos. Dzięki temu, że odczyty z wewnątrz przechowywane procedury oferta wysoki poziom spójności. Zapytania przy użyciu funkcji zdefiniowanej przez użytkownika mogą być wykonywane w podstawowej lub pomocnicze repliki, ale upewnieniu się, aby spełnić poziomu spójności żądanego, wybierając odpowiednie repliki.

## <a name="bounded-execution"></a>Ograniczonego wykonania
Wszystkie operacje rozwiązania Cosmos bazy danych należy wykonać w ramach serwera określony limit czasu żądania. To ograniczenie dotyczy również funkcje kodu JavaScript (procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika). Jeśli operacja nie została zakończona z limitem czasu, transakcja zostanie wycofana. Funkcje kodu JavaScript musi zakończyć się przed upływem limitu czasu lub zaimplementuj kontynuacji na podstawie modelu do wykonywania wsadowego/wznawiania.  

W celu uproszczenia rozwoju procedur składowanych i wyzwalaczy do obsługi terminów, wszystkie funkcje w obiekcie kolekcji (dla tworzenia, odczytu, zastępowanie i usuwanie dokumentów i załączników) zwracać wartość logiczną, która informuje, czy ta operacja zostanie ukończona. Jeśli ta wartość wynosi false, to wskazanie limitu czasu o zbliżającym się wygaśnięciu i czy procedura musi dobiega końca wykonywania.  Operacji w kolejce przed pierwszą operacją niezaakceptowanych magazynu dotrą do wykonania, jeśli procedura składowana jest przeprowadzany w czasie, a nie umieszcza w kolejce więcej żądań.  

JavaScript — funkcje są również ograniczona zużycia zasobów. Rozwiązania cosmos DB rezerwuje przepływności na kolekcję lub zbiór kontenerów. Przepływność wyrażonych znormalizowane jednostki Procesora, pamięci i wywołać jednostki żądania lub RUs zużycie we/wy. Funkcje kodu JavaScript potencjalnie może zużywać dużą liczbę RUs w krótkim czasie i mogą zostać ograniczony szybkość po osiągnięciu limitu kolekcji. Procedury składowane obciążający zasoby może również zostaną poddane kwarantannie, aby zapewnić dostępność operacji w bazie danych pierwotnych.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Przykład: Zbiorcze importowanie danych do programu bazy danych
Poniżej przedstawiono przykład procedury przechowywanej, która jest zapisywany do importowania zbiorczego dokumentów w kolekcji. Należy zwrócić uwagę, jak procedura składowana obsługuje ograniczonego wykonania, sprawdzając typu Boolean wartość zwracana z createDocument, a następnie używa liczbę dokumentów dodaje w każdym wywołaniu procedury składowanej do śledzenia i wznowić postęp w partiach.

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

## <a id="trigger"></a> Wyzwalacze bazy danych
### <a name="database-pre-triggers"></a>Wyzwalacze wstępne bazy danych
Rozwiązania cosmos DB udostępnia wyzwalacze, które są wykonywane lub wyzwolone przez operację na dokument. Na przykład można określić wstępne wyzwalacza, podczas tworzenia dokumentu — wstępne wyzwalacz zostanie uruchomiony przed utworzeniem dokumentu. W poniższym przykładzie pokazano, jak wstępne wyzwalaczy może służyć do sprawdzania poprawności właściwości dokumentu, który jest tworzony:

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


I odpowiedni kod po stronie klienta środowiska Node.js dla wyzwalacza:

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


Wstępne wyzwalaczy nie może mieć parametrów wejściowych. Obiekt żądania może służyć do manipulowania komunikat żądania skojarzony z operacją. W tym miejscu przed wyzwalacza jest uruchamiana z tworzeniem dokumentu i treść żądania zawiera dokument, który ma zostać utworzony w formacie JSON.   

Gdy wyzwalacze są zarejestrowane, użytkownicy mogą określić operacje, które można uruchomić z. Wyzwalacz został utworzony z TriggerOperation.Create, co oznacza, że w ramach operacji Zamień, jak pokazano w poniższym kodzie za pomocą wyzwalacza jest niedozwolone.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Wyzwalacze po bazy danych
Po wyzwalaczy, takich jak wstępne wyzwalacze są skojarzone z operacji w dokumencie i nie przyjmuje żadnych parametrów wejściowych. Zakres ich działania **po** operacja została zakończona i mają dostęp do komunikatu odpowiedzi, który jest wysyłany do klienta.   

W poniższym przykładzie przedstawiono po Wyzwalacze w akcji:

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


Może być zarejestrowany wyzwalacza, jak pokazano w poniższym przykładzie.

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


Wyzwalacz wysyła zapytanie dokument metadanych i aktualizuje ze szczegółami dotyczącymi nowo utworzonego dokumentu.  

Należy zwrócić uwagę jest to **transakcyjnych** wykonanie wyzwalaczy w bazie danych rozwiązania Cosmos. Po wyzwalacz uruchamia się w ramach tej samej transakcji, jak tworzenie oryginalnego dokumentu. W związku z tym jeśli z po wyzwalacza (Jeśli nie można zaktualizować dokument metadanych powiedzieć) należy zgłosić wyjątek, cała transakcja zakończy się niepowodzeniem i wycofana. Dokument nie zostanie utworzona i zostanie zwrócony wyjątek.  

## <a id="udf"></a>Funkcje zdefiniowane przez użytkownika
Funkcje zdefiniowane przez użytkownika (UDF) służą do rozszerzania gramatyki języka zapytań usługi Azure rozwiązania Cosmos bazy danych SQL i implementować niestandardowe reguły biznesowe. Mogą one wywołać tylko z wewnątrz zapytań. Nie masz dostępu do obiektu kontekstu, a są przeznaczone do użycia jako tylko do obliczeń JavaScript. W związku z tym funkcje UDF może działać w replikach pomocniczych usługi DB rozwiązania Cosmos.  

Poniższy przykład tworzy UDF do obliczenia podatkowe oparte na szybkości dla różnych nawiasy przychodów i używa go wewnątrz kwerendy można znaleźć wszystkie osoby, które płatnej ponad 20 000 $ w podatki.

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


UDF później mogą być używane w zapytaniach, podobnie jak w poniższym przykładzie:

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

## <a name="javascript-language-integrated-query-api"></a>Zapytanie o języku zintegrowanym JavaScript API
Oprócz wysyłania zapytań przy użyciu gramatyki SQL Azure rozwiązania Cosmos DB SDK po stronie serwera umożliwia wykonywanie zoptymalizowane zapytania przy użyciu interfejsu JavaScript fluent bez żadnych wiedzy programu SQL Server. Interfejs API umożliwia programowego tworzenia zapytań przez przekazanie funkcji predykatu w funkcji chainable zapytania JavaScript wywołuje ze składnią znane built-ins tablicy oraz popularnych bibliotek JavaScript, takich jak Lodash ECMAScript5 w. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript do wykonania efektywne wykorzystanie indeksów DB rozwiązania Cosmos Azure.

> [!NOTE]
> `__` (o podwójnej precyzji podkreślenie) jest aliasu `getContext().getCollection()`.
> <br/>
> Innymi słowy, można użyć `__` lub `getContext().getCollection()` kwerendy JavaScript API dostępu do.
> 
> 

Obsługiwane funkcje obejmują:

<ul>
<li>
<b>Chain().... wartość ([wywołania zwrotnego] [, opcje])</b>
<ul>
<li>
Rozpoczyna się value() od łańcuchowa wywołanie, które musi zostać zakończone.
</li>
</ul>
</li>
<li>
<b>Filtr (predicateFunction [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Filtry przy użyciu funkcji predykatu, która zwraca wartość PRAWDA/FAŁSZ, aby filtrować dokumentów wejściowych we/wy na wynikowy zestaw danych wejściowych. Ta funkcja działa podobnie do klauzuli WHERE instrukcji SQL.
</li>
</ul>
</li>
<li>
<b>mapy (transformationFunction [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Stosuje projekcji podanej funkcji przekształcania, która mapuje do obiektu JavaScript lub wartość każdego elementu wejściowego. Ta funkcja działa podobnie do klauzuli SELECT w języku SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Ta funkcja jest skrót mapy wyodrębnia wartości właściwości jednego z każdego elementu wejściowego.
</li>
</ul>
</li>
<li>
<b>spłaszczanie ([isShallow] [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Łączy i spłaszcza tablic z każdego elementu wejściowego do jednej macierzy. Ta funkcja działa podobnie do operacja SelectMany w składniku LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicate] [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Tworzy nowy zestaw dokumentów, sortując dokumentów w strumieniu dokument wejściowy rosnąco przy użyciu podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY w języku SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicate] [, opcje] [, wywołania zwrotnego])</b>
<ul>
<li>
Tworzy nowy zestaw dokumentów, sortując dokumentów w strumieniu dokument wejściowy w kolejności malejącej, używając podanego predykatu. Ta funkcja działa podobnie do klauzuli ORDER BY x DESC w programie SQL.
</li>
</ul>
</li>
</ul>


Gdy się wewnątrz predykatu lub selektora funkcje, następujące konstrukcje JavaScript uzyskać automatycznie zoptymalizowana pod kątem uruchamiać bezpośrednio na indeksy bazy danych Azure rozwiązania Cosmos:

* Operatory prosty: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literały literału obiektu w tym: {}
* var return

Następujące elementy JavaScript nie pobrać zoptymalizowane pod kątem indeksy bazy danych Azure rozwiązania Cosmos:

* Przepływ kontroli (na przykład, jeśli, podczas gdy)
* Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [JSDocs po stronie serwera](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Przykład: Zapisać procedury składowanej przy użyciu zapytania JavaScript API
W poniższym przykładzie kodu przedstawiono przykładowy sposób użycia interfejsu API zapytania JavaScript w kontekście procedury składowanej. Procedura składowana wstawia dokumentu, określonego przez parametr wejściowy i metadane aktualizacji dokumentu przy użyciu `__.filter()` metody minSize, maxSize i totalSize ustalane na podstawie właściwości size dokument wejściowy.

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

## <a name="sql-to-javascript-cheat-sheet"></a>SQL do arkusz ze wskazówkami dotyczącymi języka Javascript
W poniższej tabeli przedstawiono różne zapytania SQL oraz odpowiednich zapytań języka JavaScript.

Z kwerendy SQL dokumentu klucze właściwości (na przykład `doc.id`) jest rozróżniana wielkość liter.

|SQL| JavaScript zapytania interfejsu API|Poniższy opis|
|---|---|---|
|WYBIERZ *<br>Z dokumentów| __.map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Zwraca doc;<br>});|1|
|Wybierz docs.id, docs.message jako msg, docs.actions <br>Z dokumentów|__.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Zwraca {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|WYBIERZ *<br>Z dokumentów<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|WYBIERZ *<br>Z dokumentów<br>GDZIE ARRAY_CONTAINS (dokumentów. Tagi, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4|
|Wybierz docs.id, docs.message jako msg<br>Z dokumentów<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwraca doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwraca {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Identyfikator: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|Wybierz wartość tagu<br>Z dokumentów<br>Dołącz do dokumentów w tagu. Tagi<br>Docs._ts ORDER BY|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć dokumentu. Tagi & & Array.IsArray — (dokumentu. Znaczniki);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwraca doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

Poniższe opisy wyjaśnić, każdego zapytania w powyższej tabeli.
1. To powoduje wszystkie dokumenty (podzielony na strony z token kontynuacji) jako.
2. Projekty identyfikator komunikatu (aliasem do msg) i akcji z wszystkie dokumenty.
3. Zapytania dotyczące dokumentów za pomocą predykat: id = "X998_Y998".
4. Zapytania dotyczące dokumentów, których właściwość tagów i tagów jest tablicą zawierającą wartość 123.
5. Zapytania dotyczące dokumentów z predykatem, id = "X998_Y998", a następnie projektów identyfikatora i komunikatu (aliasem do msg).
6. Filtry dla dokumentów, których właściwości tablicy, tagi, i sortuje wynikowy dokumenty _ts sygnatury czasowej systemu właściwości, a następnie projektów + spłaszcza tablicy tagów.


## <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego
Azure DB rozwiązania Cosmos [JavaScript API po stronie serwera](http://azure.github.io/azure-documentdb-js-server/) obsługuje większość typowych funkcji języka JavaScript jako standardowych przez [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Bezpieczeństwo
Procedury składowane JavaScript i wyzwalacze są w trybie piaskownicy tak, aby skutków jednego skryptu nie zostały publicznie innych bez pośrednictwa izolacji transakcji migawki na poziomie bazy danych. Środowiska wykonawcze w puli, ale czyszczone kontekstu po każdym uruchomieniu. Dlatego ma gwarancji bezpieczne z dowolnym niezamierzone skutki uboczne od siebie.

### <a name="pre-compilation"></a>Wstępna kompilacja
Procedur składowanych, wyzwalaczy i funkcji UDF są niejawnie prekompilowany do formatu kodu bajtów, aby uniknąć kosztów kompilacji w czasie każde wywołanie skryptu. Wstępna kompilacja zapewnia wywołania procedur składowanych jest szybkie i ma niewielki rozmiar.

## <a name="client-sdk-support"></a>Obsługa zestawu SDK klienta
Oprócz Azure DB rozwiązania Cosmos [Node.js](sql-api-sdk-node.md) interfejsu API, bazy danych Azure rozwiązania Cosmos ma [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript ](http://azure.github.io/azure-documentdb-js/), i [zestawów SDK Python](sql-api-sdk-python.md) dla funkcji API SQL. Procedur składowanych, wyzwalaczy i funkcji UDF można tworzyć i wykonywane przy użyciu dowolnej z tych zestawów SDK oraz. Poniższy przykład przedstawia sposób tworzenia i wykonywanie procedury przechowywanej za pomocą klienta programu .NET. Należy zwrócić uwagę, jak przekazany do procedury składowanej w formacie JSON i odczytywania typów .NET.

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


Ten przykład przedstawia sposób użycia [interfejs API .NET SQL](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) do tworzenia wyzwalacza wstępne i Utwórz dokument z wyzwalaczem włączone. 

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


I w poniższym przykładzie pokazano, jak utworzyć funkcji zdefiniowanej przez użytkownika (UDF) i użyć go w [zapytania SQL](sql-api-sql-query.md).

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

## <a name="rest-api"></a>Interfejs API REST
Wszystkie operacje bazy danych rozwiązania Cosmos Azure mogą być wykonywane w sposób RESTful. Procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika może być zarejestrowany w kolekcji przy użyciu metody POST protokołu HTTP. Poniższy przykład przedstawia sposób rejestrowania procedury składowanej:

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


Procedura składowana jest zarejestrowany, wykonując żądanie POST identyfikator URI bazami danych/programu testdb/colls/testColl/sprocs z treścią zawierająca procedurę składowaną, aby utworzyć. Wyzwalaczy i funkcji UDF można zarejestrować podobnie, wysyłając odpowiednio POST przed/wyzwalacze i /udfs.
To przechowywane procedury może następnie być wykonywane przez wystawienie żądania POST przed jego link do zasobu:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


W tym miejscu dane wejściowe procedury składowanej jest przekazywany w treści żądania. Dane wejściowe są przekazywane jako tablica JSON parametrów wejściowych. Procedura składowana przyjmuje pierwszej danej wejściowej jako dokument, który jest treść odpowiedzi. Odpowiedź, który pojawi się następująco:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Wyzwalacze, w przeciwieństwie do procedur składowanych, nie można wykonać bezpośrednio. Zamiast tego są one wykonywane w ramach operacji w dokumencie. Można określić wyzwalaczy do uruchamiania z żądaniem korzystanie z nagłówków HTTP. Poniższy kod przedstawia żądanie utworzenia dokumentu.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


W tym miejscu przed wyzwalacz, który ma działać z tym żądaniem jest określony w nagłówku x-ms-documentdb-pre-trigger-include. Odpowiednio żadne po wyzwalacze są podane w nagłówku x-ms-documentdb-post-trigger-include. Zarówno przed i po wyzwalaczy można określić dla danego żądania.

## <a name="sample-code"></a>Przykładowy kod
Można znaleźć więcej przykładów kodu po stronie serwera (w tym [usuwanie zbiorcze](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), i [aktualizacji](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) w [repozytorium GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Chcesz udostępnić świetny procedury składowanej? przyczyniają się do repozytorium i Utwórz żądanie ściągnięcia! 

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu jednego lub więcej procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika utworzonych można załadować je i wyświetlić je w portalu Azure za pomocą Eksploratora danych.

Można również znaleźć następujące odwołania i zasoby przydatne w ścieżce, aby dowiedzieć się więcej na temat programowania po stronie serwera bazy danych Azure rozwiązania Cosmos:

* [Zestawy SDK Azure rozwiązania Cosmos bazy danych](sql-api-sdk-dotnet.md)
* [Studio usługi DocumentDB](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Rozszerzalność bezpieczne i przenośnych bazy danych](http://dl.acm.org/citation.cfm?id=276339) 
* [Architektura bazy danych zorientowane na usługę](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hostowanie środowiska uruchomieniowego .NET programu Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)

