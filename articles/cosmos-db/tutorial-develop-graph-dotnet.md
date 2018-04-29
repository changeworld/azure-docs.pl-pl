---
title: 'Azure Cosmos DB: programowanie za pomocą interfejsu SQL programu Graph na platformie .NET | Microsoft Docs'
description: Dowiedz się, jak tworzyć aplikacje za pomocą interfejsu SQL API usługi Azure Cosmos DB przy użyciu platformy .NET
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 66f0d0064fe59c6e1d249eb69c1b433fe661c513
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: programowanie za pomocą interfejsu API programu Graph na platformie .NET
Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

W tym samouczku przedstawiono sposób tworzenia konta usługi Azure Cosmos DB za pomocą witryny Azure Portal oraz sposób tworzenia bazy danych grafów i kontenera. Następnie aplikacja tworzy prostą sieć społecznościową z czterema osobami za pomocą [interfejsu API programu Graph](graph-sdk-dotnet.md), po czym ma miejsce przechodzenie grafu i wykonywanie względem niego zapytań za pomocą języka Gremlin.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB 
> * Tworzenie bazy danych grafów i kontenera
> * Serializowanie wierzchołków i krawędzi do obiektów platformy .NET
> * Dodawanie wierzchołków i krawędzi
> * Wykonywanie zapytań względem grafu za pomocą języka Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Grafy w usłudze Azure Cosmos DB
Usługi Azure Cosmos DB można użyć do tworzenia i aktualizowania grafów oraz wykonywania względem nich zapytań za pomocą biblioteki [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). Biblioteka Microsoft.Azure.Graph udostępnia pojedynczą metodę rozszerzenia `CreateGremlinQuery<T>` na podstawie klasy `DocumentClient` służącą do wykonywania zapytań w języku Gremlin.

Język Gremlin to funkcjonalny język programowania, który obsługuje operacje zapisu (DML) oraz operacje tworzenia zapytań i przechodzenia. W tym artykule znajduje się kilka przykładów stanowiących wprowadzenie do języka Gremlin. Aby uzyskać szczegółowe wskazówki dotyczące funkcji języka Gremlin dostępnych w usłudze Azure Cosmos DB, zobacz [Zapytania w języku Gremlin](gremlin-support.md). 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 
    * Na potrzeby tego samouczka możesz także użyć [emulatora lokalnego](local-emulator.md).
* Program [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Tworzenie konta bazy danych

Zacznijmy od utworzenia konta usługi Azure Cosmos DB w witrynie Azure Portal.  

> [!TIP]
> * Masz już konto usługi Azure Cosmos DB? Jeśli tak, przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS)
> * Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Szablony** / **Visual C#** / **Aplikacja konsolowa (.NET Framework)**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.
4. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**
5. Na karcie **NuGet** kliknij pozycję **Przeglądaj**, wpisz wartość **Microsoft.Azure.Graphs** w polu wyszukiwania, a następnie pole **Obejmuje wersje wstępne**.
6. W wynikach znajdź pozycję **Microsoft.Azure.Graphs**, a następnie kliknij przycisk **Zainstaluj**.
   
   Jeśli zostanie wyświetlony komunikat dotyczący przejrzenia zmian wprowadzonych w rozwiązaniu, kliknij przycisk **OK**. Jeśli wyświetlany jest komunikat o akceptacji licencji, kliknij pozycję **Akceptuję**.
   
    Biblioteka `Microsoft.Azure.Graphs` udostępnia pojedynczą metodę rozszerzenia `CreateGremlinQuery<T>` służącą do wykonywania operacji w języku Gremlin. Język Gremlin to funkcjonalny język programowania, który obsługuje operacje zapisu (DML) oraz operacje tworzenia zapytań i przechodzenia. W tym artykule znajduje się kilka przykładów stanowiących wprowadzenie do języka Gremlin. [Zapytań Gremlin](gremlin-support.md) dotyczą szczegółowe wskazówki opisujące możliwości języka Gremlin w usłudze Azure Cosmos DB.

## <a id="add-references"></a>Łączenie aplikacji

Dodaj te dwie stałe i zmienną *client* do aplikacji. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Następnie wróć do witryny [Azure Portal](https://portal.azure.com), aby pobrać adres URL punktu końcowego i klucz podstawowy. Adres URL punktu końcowego i klucz podstawowy są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji. 

W witrynie Azure Portal przejdź do konta usługi Azure Cosmos DB, kliknij pozycję **Klucze**, a następnie kliknij pozycję **Klucze odczytu i zapisu**. 

Skopiuj identyfikator URI z portalu i wklej go w miejsce elementu `Endpoint` w powyższej właściwości punktu końcowego. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce powyższej właściwości `AuthKey`. 

![Zrzut ekranu przedstawiający witrynę Azure Portal używaną w ramach samouczka do tworzenia aplikacji w języku C#. Przedstawia konto usługi Azure Cosmos DB z przyciskiem KLUCZE wyróżnionym w remach nawigacji usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI i KLUCZ PODSTAWOWY wyróżnionymi w bloku Klucze](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Tworzenie wystąpienia klasy DocumentClient 
Następnie utwórz nowe wystąpienie klasy **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Tworzenie bazy danych 

Teraz utwórz [bazę danych](sql-api-resources.md#databases) usługi Azure Cosmos DB za pomocą metody [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) lub metody [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) klasy  **DocumentClient** z zestawu [SQL SDK na platformie .NET](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Tworzenie grafu 

Następnie utwórz kontener grafów za pomocą metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) lub metody [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) klasy **DocumentClient**. Kolekcja to kontener jednostek grafów. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serializowanie wierzchołków i krawędzi do obiektów platformy .NET
Usługa Azure Cosmos DB używa [formatu przewodowego GraphSON](gremlin-support.md), który definiuje schemat JSON dla wierzchołków, krawędzi i właściwości. Zestaw SDK platformy .NET usługi Azure Cosmos DB zawiera program JSON.NET jako zależność, co pozwala na serializację i deserializację elementów GraphSON do obiektów platformy .NET, których można używać w ramach kodu.

W ramach przykładu użyjmy prostej sieci społecznościowej składającej się z czterech osób. Opisany zostanie sposób tworzenia wierzchołków `Person`, dodawania relacji `Knows` między nimi, a następnie tworzenia zapytań względem grafu i przechodzenia go w celu znalezienia relacji „znajomy znajomych”. 

Przestrzeń nazw `Microsoft.Azure.Graphs.Elements` udostępnia klasy `Vertex`, `Edge`, `Property` i `VertexProperty` do deserializacji odpowiedzi w formacie GraphSON do dobrze zdefiniowanych obiektów platformy .NET.

## <a name="run-gremlin-using-creategremlinquery"></a>Uruchamianie zapytań w języku Gremlin za pomocą metody CreateGremlinQuery
Język Gremlin, podobnie jak język SQL, obsługuje operacje odczytu, zapisu i wykonywania zapytań. Na przykład poniższy fragment kodu przedstawia sposób tworzenia wierzchołków, krawędzi, wykonywania pewnych przykładowych zapytań przy użyciu metody `CreateGremlinQuery<T>` oraz asynchronicznego iterowania się po tych wynikach za pomocą metod `ExecuteNextAsync` i HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Dodawanie wierzchołków i krawędzi

Przyjrzyjmy się bliżej instrukcjom języka Gremlin wyświetlanym w poprzedniej sekcji. Najpierw dodajemy kilka wierzchołków przy użyciu metody `addV` języka Gremlin. Na przykład poniższy fragment kodu służy do tworzenia wierzchołka „Thomas Andersen” typu „Person” z właściwościami na potrzeby imienia, nazwiska i wieku.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Następnie dodajemy kilka krawędzi między tymi wierzchołkami przy użyciu metody `addE` języka Gremlin. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Istniejący wierzchołek można zaktualizować przy użyciu krok `properties` w języku Gremlin. Pomijamy wywołanie, aby wykonać zapytanie za pomocą metod `HasMoreResults` i `ExecuteNextAsync` dla pozostałych przykładów.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Krawędzie i wierzchołki można usunąć przy użyciu kroku `drop` w języku Gremlin. W poniższym fragmencie kodu przedstawiono sposób usuwania wierzchołka i krawędzi. Należy pamiętać, że usunięcie wierzchołka spowoduje wykonanie kaskadowego usuwania skojarzonych krawędzi.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Tworzenie zapytań względem grafu

Za pomocą języka Gremlin można również wykonywać zapytania i przejścia. Na przykład w poniższym fragmencie kodu przedstawiono sposób obliczania liczby wierzchołków grafu:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Filtry można wykonywać przy użyciu kroków `has` i `hasLabel`, a także łączyć je przy użyciu operatorów `and`, `or` i `not` w celu utworzenia bardziej złożonych filtrów:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Pewne właściwości wyników zapytania można poddawać projekcji za pomocą kroku `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Jak dotąd przedstawiono tylko operatory zapytań, które działają w dowolnej bazie danych. Grafy są szybkie i wydajne dla operacji przechodzenia, gdy trzeba przejść do powiązanych krawędzi i wierzchołków. Znajdźmy wszystkich znajomych Thomasa. Możemy to zrobić, używając kroku `outE` języka Gremlin w celu znalezienia wszystkich krawędzi wychodzących od Thomasa, a następnie przechodząc do wierzchołków z tych krawędzi przy użyciu kroku `inV` języka Gremlin:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

Następne zapytanie wykonuje dwa przeskoki w celu znalezienia wszystkich „znajomych znajomych” Thomasa, wywołując `outE` i `inV` dwa razy. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin, w tym mieszanie wyrażeń filtrowania, wykonywanie zapętlenia przy użyciu kroku `loop` i implementowanie nawigacji warunkowej przy użyciu kroku `choose`. Dowiedz się więcej o tym, co można zrobić dzięki [obsłudze języka Gremlin](gremlin-support.md).

To wszystko, ten samouczek usługi Azure Cosmos DB został ukończony. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka.  

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Utworzenie konta usługi Azure Cosmos DB 
> * Utworzono bazę danych grafów i kontener
> * Przeprowadzono serializację wierzchołków i krawędzi do obiektów platformy .NET
> * Utworzono wierzchołki i krawędzie
> * Wykonano zapytanie względem grafu za pomocą języka Gremlin

Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)
