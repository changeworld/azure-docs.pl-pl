---
title: 'Samouczek: indeksowanie danych C# z baz danych usługi Azure SQL Database'
titleSuffix: Azure Cognitive Search
description: W tym C# samouczku Nawiąż połączenie z usługą Azure SQL Database, Wyodrębnij dane z możliwością wyszukiwania i załaduj je do indeksu wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650091"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Samouczek: indeksowanie danych usługi Azure C# SQL przy użyciu indeksatorów usługi Azure wyszukiwanie poznawcze

Za C#pomocą programu Skonfiguruj [indeksator](search-indexer-overview.md) , który wyodrębnia dane z możliwością wyszukiwania z usługi Azure SQL Database i wysyła je do indeksu wyszukiwania. W tym samouczku do wykonywania następujących zadań używamy [bibliotek klienckich platformy Azure wyszukiwanie poznawcze .NET](https://aka.ms/search-sdk) i aplikacji konsolowej platformy .NET Core:

> [!div class="checklist"]
> * Utwórz źródło danych łączące się z Azure SQL Database
> * Konfigurowanie indeksatora
> * Uruchamianie indeksatora w celu załadowania danych do indeksu
> * Wykonywanie zapytań względem indeksu w ramach kroku weryfikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-source-code"></a>Pobierz kod źródłowy

Kod źródłowy dla tego samouczka znajduje się w folderze [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) w repozytorium GitHub [Azure-Samples/Search-dotnet-](https://github.com/Azure-Samples/search-dotnet-getting-started) The-Started.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu API wymagają adresu URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

## <a name="set-up-connections"></a>Konfigurowanie połączeń

1. Uruchom program Visual Studio i Otwórz **DotNetHowToIndexers. sln**.

1. W Eksplorator rozwiązań otwórz plik **appSettings. JSON** i Zastąp wartości zastępcze informacjami o połączeniu z usługą wyszukiwania. Jeśli pełny adres URL to "https://my-demo-service.search.windows.net", nazwa usługi do udostępnienia to "My-demonstracyjn-Service".

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Ostatni wpis wymaga istniejącej bazy danych. Utworzysz ją w następnym kroku.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

W tym kroku utworzysz zewnętrzne źródło danych na Azure SQL Database, które indeksator może przeszukać. Przy użyciu witryny Azure Portal i pliku *hotels.sql* z przykładu można utworzyć zestaw danych w bazie danych Azure SQL Database. Usługa Azure Wyszukiwanie poznawcze zużywa spłaszczone zestawy wierszy, takie jak wygenerowane na podstawie widoku lub zapytania. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

Jeśli masz istniejący zasób Azure SQL Database, możesz dodać do niego tabelę hoteli, rozpoczynając od kroku 4.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/).

1. Znajdź lub Utwórz **SQL Database**. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Nowa strona bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png "Strona nowej bazy danych")

1. Kliknij przycisk **Przegląd + Utwórz** , aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

1. W okienku nawigacji kliknij pozycję **Edytor zapytań (wersja zapoznawcza)** , a następnie wprowadź nazwę użytkownika i hasło administratora serwera. 

   Jeśli nastąpiła odmowa dostępu, skopiuj adres IP klienta z komunikatu o błędzie, a następnie kliknij link **Ustaw zaporę serwera** , aby dodać regułę zezwalającą na dostęp z komputera klienckiego przy użyciu adresu IP klienta dla zakresu. Zastosowanie reguły może zająć kilka minut.

1. W edytorze zapytań kliknij pozycję **Otwórz zapytanie** i przejdź do lokalizacji pliku *hoteli. SQL* na komputerze lokalnym. 

1. Wybierz plik, a następnie kliknij pozycję **Otwórz**. Skrypt powinien być podobny do tego na poniższym zrzucie ekranu:

   ![Skrypt SQL](./media/search-indexer-tutorial/sql-script.png "Skrypt SQL")

1. Kliknij przycisk **Uruchom**, aby wykonać zapytanie. W okienku wyników powinien zostać wyświetlony komunikat o pomyślnym ukończeniu wykonywania zapytania dla 3 wierszy.

1. Aby zwrócić zestaw wierszy z tej tabeli, w ramach kroku weryfikacyjnego możesz wykonać następujące zapytanie:

    ```sql
    SELECT * FROM Hotels
    ```

1. Skopiuj parametry połączenia ADO.NET dla bazy danych. W obszarze **ustawienia** > **Parametry połączenia**Skopiuj parametry połączenia ADO.NET, podobnie jak w poniższym przykładzie.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Wklej parametry połączenia w obszarze „AzureSqlConnectionString” jako trzeci wpis w pliku **appsettings.json** w programie Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Wprowadź hasło do parametrów połączenia w pliku **appSettings. JSON** . Nazwy baz danych i użytkowników zostaną skopiowane przez parametry połączenia, ale hasło należy wprowadzić ręcznie.

## <a name="build-the-solution"></a>Kompilowanie rozwiązania

Naciśnij klawisz F5, aby skompilować rozwiązanie. Program będzie działać w trybie debugowania. Stan każdej operacji będzie zgłaszany w oknie konsoli.

   ![Dane wyjściowe konsoli](./media/search-indexer-tutorial/console-output.png "Dane wyjściowe konsoli")

Kod jest uruchamiany lokalnie w programie Visual Studio, łącząc się z usługą wyszukiwania na platformie Azure, która z kolei łączy się z Azure SQL Database i Pobiera zestaw danych. W przypadku tej wielu operacji istnieje kilka potencjalnych punktów awarii. Jeśli wystąpi błąd, należy najpierw sprawdzić następujące warunki:

+ Podane informacje o połączeniu usługi wyszukiwania zostały w tym samouczku ograniczone do nazwy usługi. Jeśli wprowadzono pełny adres URL, operacje zatrzymują się na etapie tworzenia indeksu i występuje błąd nawiązywania połączenia.

+ Informacje o połączeniu z bazą danych w pliku **appsettings.json**. Powinny to być parametry połączenia ADO.NET uzyskane z portalu i zmodyfikowane w celu uwzględnienia nazwy użytkownika i hasła, które obowiązują w przypadku Twojej bazy danych. Konto użytkownika musi mieć uprawnienia do pobierania danych. Adres IP klienta lokalnego musi mieć dozwolony dostęp.

+ Limity zasobów. Należy przypomnieć, że warstwa Bezpłatna ma limity trzech indeksów, indeksatorów i źródeł danych. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

## <a name="check-results"></a>Sprawdź wyniki

Użyj Azure Portal, aby zweryfikować Tworzenie obiektów, a następnie użyj **Eksploratora wyszukiwania** do wykonywania zapytań względem indeksu.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Otwórz każdą z list z kolei, aby sprawdzić, czy obiekt został utworzony. **Indeksy**, **indeksatory**i **źródła danych** będą zawierały odpowiednio "Hotele", "Azure-SQL-Indexer" i "Azure-SQL".

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)

1. Wybierz indeks hoteli. Na stronie Hotele, **Eksplorator wyszukiwania** jest pierwszą kartą. 

1. Kliknij przycisk **Wyszukaj** , aby wydać puste zapytanie. 

   Trzy wpisy w indeksie zostaną zwrócone jako dokumenty JSON. Eksplorator wyszukiwania zwraca dokumenty w formacie JSON, tak, aby można było przeglądać całą strukturę.

   ![Tworzenie zapytań względem indeksu](./media/search-indexer-tutorial/portal-search.png "Tworzenie zapytań względem indeksu")
   
1. Następnie wprowadź wyszukiwany ciąg: `search=river&$count=true`. 

   To zapytanie powoduje wywołanie wyszukiwania pełnotekstowego terminu `river`, a wynik obejmuje również liczbę pasujących dokumentów. Zwracanie liczby pasujących dokumentów jest przydatne w przypadku testowania scenariuszy, jeśli masz duży indeks z tysiącami lub milionami dokumentów. W takim przypadku tylko jeden dokument pasuje do zapytania.

1. Na koniec wprowadź wyszukiwany ciąg, który ogranicza dane wyjściowe JSON do odpowiednich pól: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpowiedź na zapytanie jest redukowana do wybranych pól, co zapewnia bardziej zwięzły widok danych wyjściowych.

## <a name="explore-the-code"></a>Eksplorowanie kodu

Teraz, gdy zrozumiesz, jak tworzony jest przykładowy kod, wróćmy do rozwiązania, aby przejrzeć kod. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel.cs**zawierający schemat definiujący indeks
  + **Program.cs**zawierające funkcje tworzenia struktur w usłudze i zarządzania nimi

### <a name="in-hotelcs"></a>W pliku hotel.cs

Schemat indeksu definiuje kolekcję pól, w tym atrybuty określające dozwolone operacje, takie jak wyszukiwanie pełnotekstowe, filtrowanie lub sortowanie w polu, jak pokazano w poniższej definicji pola HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schemat może również obejmować inne elementy, w tym profile oceniania na potrzeby poprawiania wyniku wyszukiwania, niestandardowe analizatory i inne konstrukcje. Jednak na nasze potrzeby zdefiniowaliśmy bardzo prosty schemat, który zawiera tylko pola znalezione w przykładowych bazach danych.

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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Na platformie Azure Wyszukiwanie poznawcze Indeksatory są dostępne dla wielu źródeł danych platformy Azure. Następnym krokiem jest zapoznanie się z indeksatorem usługi Azure Blob Storage.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)