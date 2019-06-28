---
title: Wykonywanie zapytania SQL w usłudze Azure Cosmos DB
description: Więcej informacji na temat wykonywania zapytań SQL w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342958"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Wykonywanie zapytania w usłudze Azure Cosmos DB SQL

Dowolnego języka realizującego żądania HTTP/HTTPS można wywołać interfejsu API REST usługi DB Cosmos. Usługa cosmos DB oferuje biblioteki programistyczne dla języków programowania .NET, Node.js, JavaScript i Python. Interfejs API REST i biblioteki obsługują wykonywanie zapytań za pomocą języka SQL i zestawu .NET SDK obsługuje również [zapytań LINQ](sql-query-linq-to-sql.md).

Poniższe przykłady pokazują, jak utworzyć zapytanie i przesłać je do konta bazy danych Cosmos DB.

## <a id="REST-API"></a>Interfejs API REST

Usługa Cosmos DB oferuje otwarty model programowania RESTful używający protokołu HTTP. Model zasobów zawiera zestaw zasobów w ramach konta bazy danych, która aprowizuje subskrypcji platformy Azure. Konto bazy danych składa się z szeregu *baz danych*, z których każdy może zawierać więcej niż jednego *kontenery*, który z kolei zawiera *elementów*, funkcje zdefiniowane przez użytkownika i innych typów zasobów. Każdy zasób usługi Cosmos DB to adresy logicznych i stabilnych identyfikatora URI. Zestaw zasobów jest nazywany *kanału informacyjnego*. 

Model interakcji podstawowa przy użyciu tych zasobów jest za pomocą polecenia HTTP `GET`, `PUT`, `POST`, i `DELETE`, przy użyciu ich standardowego interpretacji. Użyj `POST` do utworzenia nowego zasobu, wykonaj procedurę składowaną lub wystawiania zapytanie usługi Cosmos DB. Zapytania są zawsze operacjami tylko do odczytu bez efektów ubocznych.

W poniższych przykładach pokazano `POST` dla elementów przykładowe zapytanie do interfejsu API SQL. Zapytanie zawiera proste filtr w formacie JSON `name` właściwości. `x-ms-documentdb-isquery` I Content-Type: `application/query+json` nagłówki Określa, czy operacja się zapytania. Zastąp `mysqlapicosmosdb.documents.azure.com:443` za pomocą identyfikatora URI dla konta usługi Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Wyniki są:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Dalej, bardziej skomplikowanych zwraca wiele wyników z sprzężenia:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Wyniki są: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Jeśli wyniki zapytania nie mieści się w jednej strony, interfejs API REST zwraca token kontynuacji za pośrednictwem `x-ms-continuation-token` nagłówka odpowiedzi. Klienci mogą stronicowanie wyników, łącznie z nagłówkiem w kolejnych wyników. Można także kontrolować liczbę wyników na stronę za pośrednictwem `x-ms-max-item-count` numer nagłówka.

Jeśli zapytanie zawiera funkcję agregacji, takich jak liczba, na stronie zapytanie może zwracać częściowo zagregowaną wartość w tylko jednej stronie wyników. Klienci, należy wykonać agregacji drugiego poziomu, za pośrednictwem tych wyników, aby wygenerować wyniki końcowe. Na przykład Suma za pośrednictwem liczby zwracanych w poszczególnych stron, aby zwrócić łączna liczba.

Aby zarządzać zasadami spójności danych dla zapytania, należy użyć `x-ms-consistency-level` nagłówka, tak jak wszystkie żądania interfejsu API REST. Spójność sesji wymaga również wyświetlania najnowsze `x-ms-session-token` nagłówek cookie żądania zapytania. Zasady indeksowania kontenera uwzględnionego w zapytaniu mogą również wpływać na spójność wyników zapytania. Za pomocą domyślne ustawienia zasad dla kontenerów indeksowania, indeks jest zawsze aktualny z zawartością elementu, a wyniki zapytania pasuje spójności dla danych. Aby uzyskać więcej informacji, zobacz temat [Azure Cosmos DB poziomów spójności] [poziomów spójności].

Jeśli skonfigurowane zasady indeksowania w kontenerze nie obsługuje określonego zapytania, serwer usługi Azure Cosmos DB zwraca 400 "złe żądanie". Ten komunikat o błędzie zwraca zapytania ze ścieżkami jawnie wykluczone z indeksowania. Można określić `x-ms-documentdb-query-enable-scan` nagłówka, aby zezwolić na zapytanie, aby przeprowadzić skanowanie, gdy indeks jest niedostępny.

Możesz uzyskać szczegółowe metryki na wykonanie zapytania, ustawiając `x-ms-documentdb-populatequerymetrics` nagłówka do `true`. Aby uzyskać więcej informacji, zobacz [SQL query metrics for Azure Cosmos DB (Metryki zapytań języka SQL dla usługi Azure Cosmos DB)](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C#(ZESTAW SDK PLATFORMY .NET)

Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ i SQL. Poniższy przykład pokazuje, jak wykonać poprzednie zapytanie filtru przy użyciu platformy .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Poniższy przykład porównanie dwóch właściwości pod kątem równości w ramach każdego elementu i wykorzystuje anonimowy projekcji.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

W kolejnym przykładzie pokazano sprzężenia, wyrażone za pomocą LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Klient modelu .NET automatycznie wykonuje iterację przez wszystkie strony wyników zapytania w `foreach` blokuje, jak pokazano w powyższym przykładzie. Opcje zapytania wprowadzona w [interfejsu API REST](#REST-API) sekcji są również dostępne w zestawie SDK platformy .NET przy użyciu `FeedOptions` i `FeedResponse` klas w `CreateDocumentQuery` metody. Liczbę stron, które można kontrolować za pomocą `MaxItemCount` ustawienie.

Można także jawnie kontrolować stronicowania, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, a następnie, czytając `ResponseContinuationToken` wartości i przekazywania ich z powrotem jako `RequestContinuationToken` w `FeedOptions`. Możesz ustawić `EnableScanInQuery` Aby włączyć skanowanie, gdy zapytanie nie jest obsługiwany przez skonfigurowane zasady indeksowania. W przypadku partycjonowanego kontenerów, można użyć `PartitionKey` uruchamiać zapytanie jednej partycji, mimo że usługę Azure Cosmos DB można automatycznie prowadzenie to tekst zapytania. Możesz użyć `EnableCrossPartitionQuery` uruchamiać zapytania wiele partycji.

Aby uzyskać więcej przykładów .NET za pomocą zapytań, zobacz [przykładów usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) w usłudze GitHub.

## <a id="JavaScript-server-side-API"></a>Interfejs API języka JavaScript po stronie serwera

Usługa Azure Cosmos DB zapewnia model programowania do [aplikacji opartej na wykonywanie kodu JavaScript](stored-procedures-triggers-udfs.md) logiki bezpośrednio w kontenerach, przy użyciu procedur składowanych i wyzwalaczy. Logika JavaScript zarejestrowany na poziomie kontenera następnie mogą wyzwalać operacje bazy danych w elementach danego kontenera w transakcje ACID otoczenia.

Poniższy przykład pokazuje, jak używać `queryDocuments` na serwerze JavaScript API tworzenie kwerend z wewnątrz procedur składowanych i wyzwalaczy:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
