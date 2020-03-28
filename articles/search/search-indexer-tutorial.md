---
title: 'Samouczek: Indeks danych z baz danych SQL platformy Azure w języku C # '
titleSuffix: Azure Cognitive Search
description: W tym samouczku języka C# połącz się z bazą danych SQL platformy Azure, wyodrębnij dane z wyszukuj i załaduj ją do indeksu usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78193972"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Samouczek: Indeks danych z baz danych SQL w usłudze Azure Cognitive Search za pomocą języka C#

Skonfiguruj [indeksator,](search-indexer-overview.md) aby wyodrębnić dane z przeszukiwania bazy danych SQL, wysyłając je do indeksu wyszukiwania w usłudze Azure Cognitive Search. 

W tym samouczku użyto języka C# i [zestawu .NET SDK](https://aka.ms/search-sdk) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie źródła danych łączącego się z bazą danych SQL usługi Azure
> * Tworzenie indeksatora
> * Uruchamianie indeksatora w celu załadowania danych do indeksu
> * Zapytanie o indeks jako krok weryfikacji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Tworzenie](search-create-service-portal.md) lub [znajdowanie istniejącej usługi wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby zaakceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

Kod źródłowy tego samouczka znajduje się w folderze [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) w repozytorium GitHub [azure-samples/search-dotnet-getting-started.](https://github.com/Azure-Samples/search-dotnet-getting-started)

## <a name="1---create-services"></a>1 - Tworzenie usług

W tym samouczku użyto usługi Azure Cognitive Search do indeksowania i kwerend, a usługa Azure SQL Database jako zewnętrznego źródła danych. Jeśli to możliwe, utwórz obie usługi w tym samym regionie i grupy zasobów dla bliskości i zarządzania. W praktyce usługa Azure SQL Database może znajdować się w dowolnym regionie.

### <a name="start-with-azure-sql-database"></a>Rozpocznij od bazy danych SQL platformy Azure

W tym kroku utwórz zewnętrzne źródło danych w usłudze Azure SQL Database, które indeksator może indeksować. Aby utworzyć zestaw danych w usłudze Azure SQL Database, można użyć witryny Azure portal i pliku *hotels.sql* z przykładowego pobrania. Usługa Azure Cognitive Search zużywa spłaszczone zestawy wierszy, takie jak generowane z widoku lub kwerendy. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

Jeśli masz istniejący zasób usługi Azure SQL Database, możesz dodać do niego tabelę hoteli, począwszy od kroku 4.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/).

1. Znajdź lub utwórz **bazę danych SQL**. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Strona nowej bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png "Strona nowej bazy danych")

1. Kliknij **przycisk Przejrzyj + utwórz,** aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

1. W okienku nawigacji kliknij pozycję **Edytor zapytań (wersja zapoznawcza)** i wprowadź nazwę użytkownika i hasło administratora serwera. 

   Jeśli odmowa dostępu, skopiuj adres IP klienta z komunikatu o błędzie, a następnie kliknij łącze **Ustaw zaporę serwera,** aby dodać regułę umożliwiającą dostęp z komputera klienckiego przy użyciu adresu IP klienta dla zakresu. Może upłynąć kilka minut, aby reguła weszła w życie.

1. W edytorze kwerend kliknij pozycję **Otwórz kwerendę** i przejdź do lokalizacji pliku *hotels.sql* na komputerze lokalnym. 

1. Wybierz plik, a następnie kliknij pozycję **Otwórz**. Skrypt powinien być podobny do tego na poniższym zrzucie ekranu:

   ![Skrypt SQL](./media/search-indexer-tutorial/sql-script.png "Skrypt SQL")

1. Kliknij przycisk **Uruchom**, aby wykonać zapytanie. W okienku wyników powinien zostać wyświetlony komunikat o pomyślnym ukończeniu wykonywania zapytania dla 3 wierszy.

1. Aby zwrócić zestaw wierszy z tej tabeli, w ramach kroku weryfikacyjnego możesz wykonać następujące zapytanie:

    ```sql
    SELECT * FROM Hotels
    ```

1. Skopiuj ADO.NET parametry połączenia bazy danych. W obszarze **Ustawienia** > **parametry połączenia**skopiuj ADO.NET parametry połączenia, podobnie jak w poniższym przykładzie.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Ten ciąg połączenia będzie potrzebny w następnym ćwiczeniu, konfigurując środowisko.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Kolejnym składnikiem jest usługa Azure Cognitive Search, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten instruktaż, można użyć warstwy Bezpłatna. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz api administratora i adres URL usługi Azure Cognitive Search

Wywołania interfejsu API wymagają adresu URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

   ![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

## <a name="2---set-up-your-environment"></a>2 - Skonfiguruj środowisko

1. Uruchom program Visual Studio i otwórz **dotNetHowToIndexers.sln**.

1. W Eksploratorze rozwiązań otwórz **plik appsettings.json,** aby podać informacje o połączeniu.

1. Dla `searchServiceName`, jeśli pełnyhttps://my-demo-service.search.windows.netadres URL to " ", nazwa usługi do udostępnienia to "my-demo-service".

1. Dla `AzureSqlConnectionString`, format ciągu jest podobny do tego:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. W ciągu połączenia upewnij się, że parametry połączenia zawierają prawidłowe hasło. Podczas gdy baza danych i nazwy użytkowników zostaną skopiowane, hasło należy wprowadzić ręcznie.

## <a name="3---create-the-pipeline"></a>3 - Tworzenie potoku

Indeksatory wymagają obiektu źródła danych i indeksu. Odpowiedni kod znajduje się w dwóch plikach:

  + **hotel.cs**, zawierający schemat definiujący indeks
  + **Program.cs**, zawierający funkcje tworzenia i zarządzania strukturami w usłudze

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

Główny program zawiera logikę tworzenia klienta, indeksu, źródła danych i indeksatora. Kod sprawdza i usuwa istniejące zasoby o tej samej nazwie, przy założeniu, że ten program może być uruchamiany wiele razy.

Obiekt źródła danych jest skonfigurowany z ustawieniami specyficznymi dla zasobów bazy danych SQL platformy Azure, w tym [częściowym lub przyrostowym indeksowaniem](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) w celu wykorzystania wbudowanych [funkcji wykrywania zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) usługi Azure SQL. Baza danych hoteli demonstracyjnych w usłudze Azure SQL ma kolumnę "usuwania nietrwałego" o nazwie **IsDeleted**. Gdy ta kolumna jest ustawiona na true w bazie danych, indeksator usuwa odpowiedni dokument z indeksu usługi Azure Cognitive Search.

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

Obiekt indeksatora jest niezależny od platformy, gdzie konfiguracja, planowanie i wywołanie są takie same, niezależnie od źródła. Ten przykładowy indeksator zawiera harmonogram, opcja resetowania, która czyści historię indeksatora i wywołuje metodę natychmiastowego utworzenia i uruchomienia indeksatora.

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

## <a name="4---build-the-solution"></a>4 - Zbuduj rozwiązanie

Naciśnij klawisz F5, aby zbudować i uruchomić rozwiązanie. Program będzie działać w trybie debugowania. Stan każdej operacji będzie zgłaszany w oknie konsoli.

   ![Dane wyjściowe konsoli](./media/search-indexer-tutorial/console-output.png "Dane wyjściowe konsoli")

Kod jest uruchamiany lokalnie w programie Visual Studio, łącząc się z usługą wyszukiwania na platformie Azure, która z kolei łączy się z usługą Azure SQL Database i pobiera zestaw danych. Przy tak wielu operacjach istnieje kilka potencjalnych punktów awarii. Jeśli pojawia się błąd, najpierw sprawdź następujące warunki:

+ Podane informacje o połączeniu usługi wyszukiwania zostały w tym samouczku ograniczone do nazwy usługi. Jeśli wprowadzono pełny adres URL, operacje zatrzymują się na etapie tworzenia indeksu i występuje błąd nawiązywania połączenia.

+ Informacje o połączeniu z bazą danych w pliku **appsettings.json**. Powinny to być parametry połączenia ADO.NET uzyskane z portalu i zmodyfikowane w celu uwzględnienia nazwy użytkownika i hasła, które obowiązują w przypadku Twojej bazy danych. Konto użytkownika musi mieć uprawnienia do pobierania danych. Adres IP klienta lokalnego musi mieć dostęp.

+ Limity zasobów. Przypomnijmy, że warstwa Bezpłatna ma limity 3 indeksów, indeksatorów i źródeł danych. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

## <a name="5---search"></a>5 - Szukaj

Użyj witryny Azure Portal, aby zweryfikować tworzenie obiektów, a następnie użyj **Eksploratora wyszukiwania,** aby zbadać indeks.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania otwórz każdą listę po kolei, aby sprawdzić, czy obiekt jest tworzony. **Indeksy**, **indeksatory**i **źródła danych** będą miały odpowiednio "hotele", "azure-sql-indexer" i "azure-sql".

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)

1. Wybierz indeks hoteli. Na stronie hoteli **eksplorator wyszukiwania** jest pierwszą kartą. 

1. Kliknij **przycisk Wyszukaj,** aby wystawić pustą kwerendę. 

   Trzy wpisy w indeksie zostaną zwrócone jako dokumenty JSON. Eksplorator wyszukiwania zwraca dokumenty w formacie JSON, tak, aby można było przeglądać całą strukturę.

   ![Zapytanie o indeks](./media/search-indexer-tutorial/portal-search.png "Zapytanie o indeks")
   
1. Następnie wprowadź wyszukiwany ciąg: `search=river&$count=true`. 

   To zapytanie powoduje wywołanie wyszukiwania pełnotekstowego terminu `river`, a wynik obejmuje również liczbę pasujących dokumentów. Zwracanie liczby pasujących dokumentów jest przydatne w przypadku testowania scenariuszy, jeśli masz duży indeks z tysiącami lub milionami dokumentów. W takim przypadku tylko jeden dokument pasuje do zapytania.

1. Na koniec wprowadź wyszukiwany ciąg, który ogranicza dane wyjściowe JSON do odpowiednich pól: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpowiedź na zapytanie jest redukowana do wybranych pól, co zapewnia bardziej zwięzły widok danych wyjściowych.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, dzięki czemu można ponownie uruchomić kod.

Można również użyć portalu do usuwania indeksów, indeksatorów i źródeł danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy indeksowania bazy danych SQL, przyjrzyjmy się bliżej konfiguracji indeksatora.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksatora bazy danych SQL platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)