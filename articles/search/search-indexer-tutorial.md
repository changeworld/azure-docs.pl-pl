---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406711"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

Zaawansowanie w zakresie obsługi konfiguracji indeksatora jest pomocne, ponieważ upraszcza proces zapisu i obsługiwania kodu oraz zmniejsza jego ilość. Zamiast przygotowywać i wypychać zestaw danych JSON zgodny ze schematem, można dołączyć indeksator do źródła danych, poczekać, aż indeksator wyodrębni dane i wstawi je do indeksu, a następnie opcjonalnie uruchomić indeksator zgodnie z cyklicznym harmonogramem w celu zastosowania zmian w odpowiednim źródle.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Przygotowywanie zewnętrznego zestawu danych w bazie danych Azure SQL Database 
> * Przeglądanie definicji indeksu i indeksatora w przykładowym kodzie
> * Uruchamianie kodu indeksatora w celu zaimportowania danych
> * Przeszukiwanie indeksu
> * Wyświetlanie konfiguracji indeksatora w portalu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. Przykładowe rozwiązanie udostępnia plik danych SQL w celu utworzenia tabeli. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych zasobów.

## <a name="get-a-key-and-url"></a>Get a key and URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-connections"></a>Konfigurowanie połączeń
Informacje o połączeniu z wymaganymi usługami są określane w pliku **appsettings.json** w rozwiązaniu. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

W tym kroku zostanie utworzone zewnętrzne źródło danych, które indeksator może przeszukiwać. Przy użyciu witryny Azure Portal i pliku *hotels.sql* z przykładu można utworzyć zestaw danych w bazie danych Azure SQL Database. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

W poniższym ćwiczeniu założono, że nie ma istniejących serwerów ani baz danych — obydwa te elementy zostaną utworzone w kroku 2. Jeśli zasób istnieje można również dodać do niego tabelę hotels, zaczynając pracę od kroku 4.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Strona nowej bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknij pozycję **Utwórz**, aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

4. Otwórz stronę bazy danych SQL Database dla nowej bazy danych, jeśli nie jest została wcześniej otwarta. Nazwą zasobu powinna być *SQL Database*, a nie *SQL Server*.

   ![Strona bazy danych SQL Database](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

5. Kliknij pozycję **Zaloguj się**, a następnie wprowadź nazwę użytkownika i hasło administratora serwera.

6. Kliknij pozycję **Otwórz zapytanie** i przejdź do lokalizacji pliku *hotels.sql*. 

7. Wybierz plik, a następnie kliknij pozycję **Otwórz**. Skrypt powinien być podobny do tego na poniższym zrzucie ekranu:

   ![Skrypt SQL](./media/search-indexer-tutorial/sql-script.png)

8. Kliknij przycisk **Uruchom**, aby wykonać zapytanie. W okienku wyników powinien zostać wyświetlony komunikat o pomyślnym ukończeniu wykonywania zapytania dla 3 wierszy.

9. Aby zwrócić zestaw wierszy z tej tabeli, w ramach kroku weryfikacyjnego możesz wykonać następujące zapytanie:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    Prototypowe zapytanie, `SELECT * FROM Hotels`, nie działa w Edytorze zapytań. Przykładowe dane obejmują współrzędne geograficzne w polu lokalizacji. Nie są one obecnie obsługiwane w edytorze. Aby wyświetlić listę innych kolumn, dla których można wykonać zapytanie, możesz wykonać tę instrukcję: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Teraz masz już zewnętrzny zestaw danych. Skopiuj parametry połączenia ADO.NET dla bazy danych. Na stronie swojej bazy danych SQL Database przejdź do obszaru **Ustawienia** > **Parametry połączenia** i skopiuj parametry połączenia ADO.NET.
 
    Parametry połączenia ADO.NET wyglądają jak w poniższym przykładzie. Zostały one zmodyfikowane w celu użycia prawidłowej nazwy bazy danych, nazwy użytkownika i hasła.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Wklej parametry połączenia w obszarze „AzureSqlConnectionString” jako trzeci wpis w pliku **appsettings.json** w programie Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Zrozumienie kodu

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. Przed wykonaniem tych czynności poświęć chwilę na zapoznanie się z definicjami indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **hotel.cs**, który zawiera schemat definiujący indeks
  + **Program.cs**, który zawiera funkcje służące do tworzenia struktur i zarządzania nimi w usłudze

### <a name="in-hotelcs"></a>W pliku hotel.cs

Schemat indeksu definiuje kolekcję pól, w tym atrybuty określające dozwolone operacje, takie jak wyszukiwanie pełnotekstowe, filtrowanie lub sortowanie w polu, jak pokazano w poniższej definicji pola HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schemat może również obejmować inne elementy, w tym profile oceniania na potrzeby poprawiania wyniku wyszukiwania, niestandardowe analizatory i inne konstrukcje. Jednak na nasze potrzeby zdefiniowaliśmy bardzo prosty schemat, który zawiera tylko pola znalezione w przykładowych bazach danych.

W tym samouczku indeksator ściąga dane z jednego źródła danych. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. Możesz użyć tej samej pary indeks-indeksator różniącej się tylko źródłami danych lub jednego indeksu z różnymi kombinacjami indeksatora i źródła danych, w zależności od tego, w jakim obszarze chcesz zachować elastyczność.

### <a name="in-programcs"></a>W pliku Program.cs

The main program includes logic for creating a client, an index, a data source, and an indexer. Kod sprawdza i usuwa istniejące zasoby o tej samej nazwie, przy założeniu, że ten program może być uruchamiany wiele razy.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Uruchamianie indeksatora

W tym kroku program zostanie skompilowany i uruchomiony. 

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **DotNetHowToIndexers** i wybierz pozycję **Kompiluj**.
2. Ponownie kliknij prawym przyciskiem myszy pozycję **DotNetHowToIndexers**, a następnie pozycje **Debuguj** > **Uruchom nowe wystąpienie**.

Program będzie działać w trybie debugowania. Stan każdej operacji będzie zgłaszany w oknie konsoli.

  ![Skrypt SQL](./media/search-indexer-tutorial/console-output.png)

Kod jest uruchamiany lokalnie w programie Visual Studio. Łączy się on z usługą wyszukiwania na platformie Azure, która z kolei używa parametrów połączenia do łączenia z bazą danych Azure SQL Database i pobierania zestawu danych. Przy tak dużej liczbie operacji istnieje kilka punktów, których potencjalnie mogą wystąpić awarie, ale jeśli pojawi się błąd, sprawdź najpierw następujące warunki:

+ Podane informacje o połączeniu usługi wyszukiwania zostały w tym samouczku ograniczone do nazwy usługi. Jeśli wprowadzono pełny adres URL, operacje zatrzymują się na etapie tworzenia indeksu i występuje błąd nawiązywania połączenia.

+ Informacje o połączeniu z bazą danych w pliku **appsettings.json**. Powinny to być parametry połączenia ADO.NET uzyskane z portalu i zmodyfikowane w celu uwzględnienia nazwy użytkownika i hasła, które obowiązują w przypadku Twojej bazy danych. Konto użytkownika musi mieć uprawnienia do pobierania danych.

+ Limity zasobów. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

## <a name="search-the-index"></a>Przeszukiwanie indeksu 

W witrynie Azure Portal na stronie przeglądu usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** u góry, aby przesłać kilka zapytań dotyczących nowego indeksu.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać indeks *hotels*.

2. Kliknij przycisk **Wyszukaj**, aby utworzyć puste wyszukiwanie. 

   Trzy wpisy w indeksie zostaną zwrócone jako dokumenty JSON. Eksplorator wyszukiwania zwraca dokumenty w formacie JSON, tak, aby można było przeglądać całą strukturę.

3. Następnie wprowadź wyszukiwany ciąg: `search=river&$count=true`. 

   To zapytanie powoduje wywołanie wyszukiwania pełnotekstowego terminu `river`, a wynik obejmuje również liczbę pasujących dokumentów. Zwracanie liczby pasujących dokumentów jest przydatne w przypadku testowania scenariuszy, jeśli masz duży indeks z tysiącami lub milionami dokumentów. W takim przypadku tylko jeden dokument pasuje do zapytania.

4. Na koniec wprowadź wyszukiwany ciąg, który ogranicza dane wyjściowe JSON do odpowiednich pól: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpowiedź na zapytanie jest redukowana do wybranych pól, co zapewnia bardziej zwięzły widok danych wyjściowych.

## <a name="view-indexer-configuration"></a>Wyświetlanie konfiguracji indeksatora

W portalu są wyświetlane wszystkie indeksatory, w tym właśnie utworzony przy użyciu programu. Możesz otworzyć definicję indeksatora i wyświetlić źródło danych lub skonfigurować harmonogram odświeżania w celu zidentyfikowania nowych i zmienionych wierszy.

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Następne kroki

You can attach AI enrichment algorithms to an indexer pipeline. Aby wykonać następny krok, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)