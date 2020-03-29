---
title: Wykonywanie zapytań SQL w usłudze Azure Cosmos DB
description: Dowiedz się, jak utworzyć kwerendę SQL i wykonać ją w usłudze Azure Cosmos DB. W tym artykule opisano sposób tworzenia i wykonywania kwerendy SQL przy użyciu interfejsu API REST, interfejsu SDK .Net, sdk javascript i różnych innych pakietów SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871265"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Wykonywanie zapytań SQL usługi Azure Cosmos DB

Każdy język zdolny do wysyłania żądań HTTP/HTTPS może wywołać interfejs API REST usługi Cosmos DB. Usługa Cosmos DB oferuje również biblioteki programowania dla języków programowania .NET, Node.js, JavaScript i Python. Interfejs API REST i biblioteki obsługują wykonywanie zapytań za pośrednictwem języka SQL, a zestaw .NET SDK obsługuje również [wykonywanie zapytań LINQ.](sql-query-linq-to-sql.md)

Poniższe przykłady pokazują, jak utworzyć kwerendę i przesłać ją do konta bazy danych usługi Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>INTERFEJS API ODPOCZYNKU

Usługa Cosmos DB oferuje otwarty model programowania RESTful używający protokołu HTTP. Model zasobów składa się z zestawu zasobów w ramach konta bazy danych, które są przepisami subskrypcji platformy Azure. Konto bazy danych składa się z zestawu *baz danych,* z których każda może zawierać wiele *kontenerów,* które z kolei zawierają *elementy,* pliki UDF i inne typy zasobów. Każdy zasób usługi Cosmos DB można adresować przy użyciu logicznego i stabilnego identyfikatora URI. Zestaw zasobów jest nazywany *kanałem informacyjnym*. 

Podstawowy model interakcji z tymi zasobami `GET`odbywa `PUT` `POST`się `DELETE`za pośrednictwem czasowników HTTP , i , z ich interpretacjami standardowymi. Służy `POST` do tworzenia nowego zasobu, wykonywania procedury składowanej lub wystawiania kwerendy usługi Cosmos DB. Zapytania są zawsze operacjami tylko do odczytu bez efektów ubocznych.

Poniższe przykłady `POST` pokazują kwerendę interfejsu API SQL względem przykładowych elementów. Kwerenda ma prosty filtr we `name` właściwości JSON. Nagłówki `x-ms-documentdb-isquery` i Typ `application/query+json` zawartości: oznaczają, że operacja jest kwerendą. Zamień `mysqlapicosmosdb.documents.azure.com:443` identyfikator URI dla swojego konta usługi Cosmos DB.

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

Wyniki są następujące:

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

Następna, bardziej złożona kwerenda zwraca wiele wyników z sprzężenia:

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

Wyniki są następujące: 

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

Jeśli wyniki kwerendy nie może zmieścić się w jednej stronie, interfejs `x-ms-continuation-token` API REST zwraca token kontynuacji za pośrednictwem nagłówka odpowiedzi. Klienci mogą na strony wyniki przez włączenie nagłówka w kolejnych wynikach. Można również kontrolować liczbę wyników na `x-ms-max-item-count` stronie za pomocą nagłówka liczb.

Jeśli kwerenda ma funkcję agregacji, taką jak COUNT, strona kwerendy może zwrócić częściowo zagregowane wartości tylko na jednej stronie wyników. Klienci muszą wykonać agregację drugiego poziomu nad tymi wynikami, aby uzyskać ostateczne wyniki. Na przykład suma ponad liczby zwrócone na poszczególnych stronach, aby zwrócić całkowitą liczbę.

Aby zarządzać zasadami spójności danych dla `x-ms-consistency-level` kwerend, użyj nagłówka, jak we wszystkich żądaniach interfejsu API REST. Spójność sesji wymaga również `x-ms-session-token` powtarzania najnowszego nagłówka pliku cookie w żądaniu kwerendy. Zasady indeksowania kontenera uwzględnionego w zapytaniu mogą również wpływać na spójność wyników zapytania. W przypadku domyślnych ustawień zasad indeksowania kontenerów indeks jest zawsze aktualny z zawartością elementu, a wyniki kwerendy są zgodne ze spójnością wybraną dla danych. Aby uzyskać więcej informacji, zobacz [Poziomy spójności usługi Azure Cosmos DB][poziomy spójności].

Jeśli skonfigurowana zasada indeksowania w kontenerze nie może obsługiwać określonej kwerendy, serwer usługi Azure Cosmos DB zwraca 400 "Nieprawidłowe żądanie". Ten komunikat o błędzie zwraca dla kwerend ze ścieżkami jawnie wykluczonymi z indeksowania. Można określić `x-ms-documentdb-query-enable-scan` nagłówek, aby umożliwić kwerendzie wykonywanie skanowania, gdy indeks nie jest dostępny.

Szczegółowe metryki dotyczące wykonywania kwerendy `x-ms-documentdb-populatequerymetrics` można `true`uzyskać, ustawiając nagłówek na . Aby uzyskać więcej informacji, zobacz [SQL query metrics for Azure Cosmos DB (Metryki zapytań języka SQL dla usługi Azure Cosmos DB)](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ i SQL. W poniższym przykładzie pokazano, jak wykonać poprzednią kwerendę filtrującą za pomocą platformy .NET:

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

Poniższy przykład porównuje dwie właściwości równości w ramach każdego elementu i używa projekcji anonimowych.

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

Następny przykład pokazuje sprzężenia, `SelectMany`wyrażone za pośrednictwem LINQ .

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

Klient platformy .NET automatycznie iteruje przez wszystkie strony `foreach` wyników kwerendy w blokach, jak pokazano w poprzednim przykładzie. Opcje kwerendy wprowadzone w sekcji [INTERFEJSU API REST](#REST-API) są również dostępne `FeedOptions` `FeedResponse` w pliku `CreateDocumentQuery` .NET SDK przy użyciu i klas w metodzie. Liczbę stron można kontrolować za `MaxItemCount` pomocą tego ustawienia.

Można również jawnie kontrolować stronicowanie, `IDocumentQueryable` tworząc obiekt, `IQueryable` `ResponseContinuationToken` a następnie czytając `RequestContinuationToken` `FeedOptions`wartości i przekazując je z powrotem, jak w . Można ustawić, `EnableScanInQuery` aby włączyć skanowanie, gdy kwerenda nie jest obsługiwana przez skonfigurowane zasady indeksowania. W przypadku kontenerów podzielonych na partycje można użyć `PartitionKey` do uruchomienia kwerendy względem jednej partycji, chociaż usługa Azure Cosmos DB może automatycznie wyodrębnić to z tekstu kwerendy. Można użyć `EnableCrossPartitionQuery` do uruchamiania kwerend względem wielu partycji.

Aby uzyskać więcej przykładów platformy .NET z kwerendami, zobacz [przykłady usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) w usłudze GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>Interfejs API języka JavaScript po stronie serwera

Usługa Azure Cosmos DB udostępnia model programowania do wykonywania logiki aplikacji opartej na [języku JavaScript](stored-procedures-triggers-udfs.md) bezpośrednio na kontenerach, przy użyciu procedur składowanych i wyzwalaczy. Logika JavaScript zarejestrowana na poziomie kontenera może następnie wystawiać operacje bazy danych na elementach danego kontenera, zawijanych w otaczające transakcje ACID.

W poniższym przykładzie `queryDocuments` pokazano, jak używać w interfejsie API serwera JavaScript do wywoływania zapytań z wewnętrznych procedur i wyzwalaczy:

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

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Poziomy spójności usługi Azure Cosmos DB](consistency-levels.md)
