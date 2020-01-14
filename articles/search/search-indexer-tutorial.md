---
title: 'Samouczek: indeksowanie danych C# z baz danych usługi Azure SQL Database'
titleSuffix: Azure Cognitive Search
description: W tym C# samouczku Nawiąż połączenie z usługą Azure SQL Database, Wyodrębnij dane z możliwością wyszukiwania i załaduj je do indeksu wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1b03f5569386212905cdeb362cfe0a88774eb887
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754338"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Samouczek: Importowanie bazy danych Azure SQL C# Database przy użyciu usługi Azure wyszukiwanie poznawcze indeksatory

Dowiedz się, jak skonfigurować indeksator do wyodrębniania danych z możliwością wyszukiwania z przykładowej bazy danych SQL Azure. [Indeksatory](search-indexer-overview.md) są składnikiem platformy Azure wyszukiwanie poznawcze przeszukiwania zewnętrznych źródeł danych, wypełniania [indeksu wyszukiwania](search-what-is-an-index.md) zawartością. Dla wszystkich indeksatorów, indeksator dla Azure SQL Database jest najczęściej używany. 

Zaawansowanie w zakresie obsługi konfiguracji indeksatora jest pomocne, ponieważ upraszcza proces zapisu i obsługiwania kodu oraz zmniejsza jego ilość. Zamiast przygotowywać i wypychać zestaw danych JSON zgodny ze schematem, można dołączyć indeksator do źródła danych, poczekać, aż indeksator wyodrębni dane i wstawi je do indeksu, a następnie opcjonalnie uruchomić indeksator zgodnie z cyklicznym harmonogramem w celu zastosowania zmian w odpowiednim źródle.

W tym samouczku Użyj [bibliotek klienckich platformy .net Wyszukiwanie poznawcze](https://aka.ms/search-sdk) i aplikacji konsolowej platformy .NET Core do wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Przygotowywanie zewnętrznego zestawu danych w bazie danych Azure SQL Database 
> * Przeglądanie definicji indeksu i indeksatora w przykładowym kodzie
> * Uruchamianie kodu indeksatora w celu zaimportowania danych
> * Przeszukiwanie indeksu
> * Wyświetlanie konfiguracji indeksatora w portalu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki Start są używane następujące usługi, narzędzia i dane. 

[Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) przechowuje zewnętrzne źródło danych używane przez indeksator. Przykładowe rozwiązanie udostępnia plik danych SQL w celu utworzenia tabeli. Kroki związane z tworzeniem usługi i bazy danych są dostępne w tym samouczku.

Do uruchamiania przykładowego rozwiązania można użyć [programu Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), dowolnej wersji. Przykładowy kod i instrukcje zostały przetestowane w wersji bezpłatnej społeczności.

[Azure-Samples/Search — dotnet — wprowadzenie](https://github.com/Azure-Samples/search-dotnet-getting-started) zapewnia przykładowe rozwiązanie, które znajduje się w repozytorium GitHub usługi Azure Samples. Pobierz i Wyodrębnij rozwiązanie. Domyślnie rozwiązania są tylko do odczytu. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wyczyść atrybut tylko do odczytu, aby można było modyfikować pliki.

> [!Note]
> Jeśli używasz bezpłatnej usługi Wyszukiwanie poznawcze platformy Azure, masz ograniczone do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych zasobów.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-connections"></a>Konfigurowanie połączeń
Informacje o połączeniu z wymaganymi usługami są określane w pliku **appsettings.json** w rozwiązaniu. 

1. W programie Visual Studio Otwórz plik **DotNetHowToIndexers. sln** .

1. W Eksplorator rozwiązań otwórz plik **appSettings. JSON** , aby można było wypełnić każde ustawienie.  

Pierwsze dwa wpisy, które można teraz wypełnić, przy użyciu adresu URL i kluczy administracyjnych usługi Azure Wyszukiwanie poznawcze. Mając punkt końcowy `https://mydemo.search.windows.net`, nazwa usługi do udostępnienia jest `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Ostatni wpis wymaga istniejącej bazy danych. Utworzysz ją w następnym kroku.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

W tym kroku zostanie utworzone zewnętrzne źródło danych, które indeksator może przeszukiwać. Przy użyciu witryny Azure Portal i pliku *hotels.sql* z przykładu można utworzyć zestaw danych w bazie danych Azure SQL Database. Usługa Azure Wyszukiwanie poznawcze zużywa spłaszczone zestawy wierszy, takie jak wygenerowane na podstawie widoku lub zapytania. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

W poniższym ćwiczeniu założono, że nie ma istniejących serwerów ani baz danych — obydwa te elementy zostaną utworzone w kroku 2. Jeśli zasób istnieje można również dodać do niego tabelę hotels, zaczynając pracę od kroku 4.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/). 

2. Znajdź lub Utwórz **Azure SQL Database** , aby utworzyć bazę danych, serwer i grupę zasobów. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Strona nowej bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknij pozycję **Utwórz**, aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

4. Otwórz stronę bazy danych SQL Database dla nowej bazy danych, jeśli nie jest została wcześniej otwarta. Nazwą zasobu powinna być *SQL Database*, a nie *SQL Server*.

   ![Strona bazy danych SQL Database](./media/search-indexer-tutorial/hotels-db.png)

4. W okienku nawigacji kliknij pozycję **Edytor zapytań (wersja zapoznawcza)** .

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

Gdy dane i ustawienia konfiguracji są stosowane, przykładowy program w **DotNetHowToIndexers. sln** jest gotowy do kompilowania i uruchamiania. Przed wykonaniem tych czynności poświęć chwilę na zapoznanie się z definicjami indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

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

W tym samouczku indeksator ściąga dane z jednego źródła danych. W tym celu można dołączyć wiele indeksatorów do tego samego indeksu, tworząc skonsolidowany indeks z możliwością wyszukiwania z wielu źródeł danych. Możesz użyć tej samej pary indeks-indeksator różniącej się tylko źródłami danych lub jednego indeksu z różnymi kombinacjami indeksatora i źródła danych, w zależności od tego, w jakim obszarze chcesz zachować elastyczność.

### <a name="in-programcs"></a>W pliku Program.cs

Program główny zawiera logikę tworzenia klienta, indeksu, źródła danych i indeksatora. Kod sprawdza i usuwa istniejące zasoby o tej samej nazwie, przy założeniu, że ten program może być uruchamiany wiele razy.

Obiekt źródła danych jest skonfigurowany z ustawieniami specyficznymi dla zasobów usługi Azure SQL Database, w tym [częściowym lub przyrostowym indeksem](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) , korzystającym z wbudowanych [funkcji wykrywania zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) usługi Azure SQL. Baza danych hoteli demonstracyjna w usłudze Azure SQL zawiera kolumnę "Usuwanie trwałe" o nazwie **IsDeleted**. Gdy ta kolumna ma wartość true w bazie danych, indeksator usuwa odpowiedni dokument z indeksu Wyszukiwanie poznawcze platformy Azure.

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

Obiekt indeksatora to platform-niezależny od, w których konfiguracja, planowanie i wywoływanie są takie same, niezależnie od źródła. Ten przykład indeksatora zawiera harmonogram, opcję resetowania, która czyści historię indeksatora, i wywołuje metodę, aby natychmiast utworzyć i uruchomić indeksator.

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

+ Limity zasobów. Należy przypomnieć, że warstwa Bezpłatna ma limity trzech indeksów, indeksatorów i źródeł danych. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

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

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania kliknij linki do **indeksów**, **indeksatorów**i **źródeł danych**.
3. Wybierz poszczególne obiekty, aby wyświetlić lub zmodyfikować ustawienia konfiguracji.

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem oczyszczenia po samouczku jest usunięcie grupy zasobów zawierającej usługę Wyszukiwanie poznawcze platformy Azure. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. Nazwa grupy zasobów w portalu znajduje się na stronie Przegląd usługi Azure Wyszukiwanie poznawcze.

## <a name="next-steps"></a>Następne kroki

Można dołączyć algorytmy wzbogacania AI do potoku indeksatora. Aby wykonać następny krok, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)