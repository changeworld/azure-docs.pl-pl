---
title: 'Samouczek: Indeksowanie danych z bazy danych Azure SQL w C# przykładowy kod — usługa Azure Search'
description: A C# przykład kodu, w którym pokazano, jak nawiązać połączenie z bazą danych Azure SQL, wyodrębnianie danych z możliwością wyszukiwania i załadować je do indeksu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb082fb83f8c2521b0deabced6f851e62b785e8f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485386"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Samouczek: Przeszukiwanie bazy danych Azure SQL Database przy użyciu indeksatorów usługi Azure Search

Dowiedz się, jak skonfigurować indeksator do wyodrębniania danych z możliwością wyszukiwania z przykładowej bazy danych Azure SQL. [Indeksatory](search-indexer-overview.md) to składnik usługi Azure Search, który przeszukuje zewnętrzne źródła danych, wypełniając [indeks wyszukiwania](search-what-is-an-index.md) przy użyciu zawartości. Wszystkie indeksatory indeksator usługi Azure SQL Database jest najczęściej używana. 

Zaawansowanie w zakresie obsługi konfiguracji indeksatora jest pomocne, ponieważ upraszcza proces zapisu i obsługiwania kodu oraz zmniejsza jego ilość. Zamiast przygotowywać i wypychać zestaw danych JSON zgodny ze schematem, można dołączyć indeksator do źródła danych, poczekać, aż indeksator wyodrębni dane i wstawi je do indeksu, a następnie opcjonalnie uruchomić indeksator zgodnie z cyklicznym harmonogramem w celu zastosowania zmian w odpowiednim źródle.

W tym samouczku [biblioteki klienta .NET usługi Azure Search](https://aka.ms/search-sdk) i aplikacji konsoli .NET Core do wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Przygotowywanie zewnętrznego zestawu danych w bazie danych Azure SQL Database 
> * Przeglądanie definicji indeksu i indeksatora w przykładowym kodzie
> * Uruchamianie kodu indeksatora w celu zaimportowania danych
> * Przeszukiwanie indeksu
> * Wyświetlanie konfiguracji indeksatora w portalu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujących usług, narzędzi i danych są używane w tym przewodniku Szybki Start. 

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

[Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) przechowuje zewnętrzne źródło danych używane w indeksatorze. Przykładowe rozwiązanie udostępnia plik danych SQL w celu utworzenia tabeli. Procedury służące do tworzenia usług i bazy danych znajdują się w tym samouczku.

[Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), w każdej wersji, może służyć do uruchamiania w przykładowym rozwiązaniu. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

[Azure — Przykłady/wyszukiwania dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) zawiera przykładowe rozwiązanie znajduje się w repozytorium GitHub przykładów dla platformy Azure. Pobierz i Wyodrębnij rozwiązania. Domyślnie rozwiązania są tylko do odczytu. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wyczyść atrybut tylko do odczytu, dzięki czemu mogą modyfikować pliki.

> [!Note]
> Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych zasobów.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-connections"></a>Konfigurowanie połączeń
Informacje o połączeniu z wymaganymi usługami są określane w pliku **appsettings.json** w rozwiązaniu. 

1. W programie Visual Studio, otwórz **DotNetHowToIndexers.sln** pliku.

1. W Eksploratorze rozwiązań Otwórz **appsettings.json** tak, aby można było wypełnić poszczególne ustawienia.  

Pierwsze dwie pozycje można wypełnić od razu, za pomocą kluczy administrator i adres URL dla usługi Azure Search. Podany punkt końcowy `https://mydemo.search.windows.net`, nazwa usługi w celu zapewnienia jest `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Ostatni wpis wymaga istniejącej bazy danych. Utworzysz je w następnym kroku.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

W tym kroku zostanie utworzone zewnętrzne źródło danych, które indeksator może przeszukiwać. Przy użyciu witryny Azure Portal i pliku *hotels.sql* z przykładu można utworzyć zestaw danych w bazie danych Azure SQL Database. Usługa Azure Search używa spłaszczonych zestawów wierszy, takich jak zestaw generowany w oparciu o widok lub zapytanie. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

W poniższym ćwiczeniu założono, że nie ma istniejących serwerów ani baz danych — obydwa te elementy zostaną utworzone w kroku 2. Jeśli zasób istnieje można również dodać do niego tabelę hotels, zaczynając pracę od kroku 4.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/). 

2. Znajdowanie lub tworzenie **usługi Azure SQL Database** do utworzenia bazy danych, serwer i grupę zasobów. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Strona nowej bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknij pozycję **Utwórz**, aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

4. Otwórz stronę bazy danych SQL Database dla nowej bazy danych, jeśli nie jest została wcześniej otwarta. Nazwą zasobu powinna być *SQL Database*, a nie *SQL Server*.

   ![Strona bazy danych SQL Database](./media/search-indexer-tutorial/hotels-db.png)

4. W okienku nawigacji kliknij **Edytor zapytań (wersja zapoznawcza)** .

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

Po danych i ustawień konfiguracji znajdują się w miejscu, próbki programu **DotNetHowToIndexers.sln** jest gotowy do skompilowania i uruchomienia. Przed wykonaniem tych czynności poświęć chwilę na zapoznanie się z definicjami indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

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

W tym samouczku indeksator ściąga dane z jednego źródła danych. W praktyce można dołączyć wiele indeksatorów do tego samego indeksu, tworząc skonsolidowany indeks wyszukiwania z wielu źródeł danych. Możesz użyć tej samej pary indeks-indeksator różniącej się tylko źródłami danych lub jednego indeksu z różnymi kombinacjami indeksatora i źródła danych, w zależności od tego, w jakim obszarze chcesz zachować elastyczność.

### <a name="in-programcs"></a>W pliku Program.cs

Główny program zawiera logikę do tworzenia klienta, indeks, źródło danych i indeksatora. Kod sprawdza i usuwa istniejące zasoby o tej samej nazwie, przy założeniu, że ten program może być uruchamiany wiele razy.

Obiekt źródła danych jest skonfigurowany przy użyciu ustawień, które są specyficzne dla zasobów bazy danych Azure SQL, w tym [przyrostowe indeksowania](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) za pomocą wbudowanych [zmiany funkcji wykrywania](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) platformy Azure SQL. Hotele demonstracyjną bazę danych w usłudze Azure SQL zawiera kolumnę "usuwania nietrwałego" o nazwie **IsDeleted**. Gdy w tej kolumnie jest ustawiona na wartość true w bazie danych, indeksator usuwa odpowiednich dokumentów z indeksu wyszukiwania platformy Azure.

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

Obiekt indeksatora jest niezależne od platformy, których konfiguracja, planowanie i wywołania są takie same, niezależnie od źródła. Ten przykładowy indeksator zawiera harmonogramu, opcja resetowania, czyści historię indeksatora, która wywołuje metodę, aby utworzyć i uruchomić indeksator natychmiast.

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

+ Limity zasobów. Pamiętaj, że usługa udostępniona (bezpłatna) ma limit 3 indeksów, indeksatorów i źródeł danych. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

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

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** kliknij linki do **indeksy**, **indeksatory**, i **danych Źródła**.
3. Wybierz poszczególne obiekty, aby wyświetlić lub zmodyfikować ustawienia konfiguracji.

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki

Algorytmy wzbogacania sztucznej Inteligencji można dołączyć do potoku indeksatora. Aby wykonać następny krok, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)