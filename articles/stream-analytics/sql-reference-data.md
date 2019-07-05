---
title: Korzystanie z danych referencyjnych z bazy danych SQL dla zadania usługi Azure Stream Analytics
description: W tym artykule opisano sposób użycia bazy danych SQL jako wejściowych danych referencyjnych dla zadania usługi Azure Stream Analytics, w witrynie Azure portal i w programie Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: ae1954b99e268e8bc44c4ba29bbc79d7734fda6e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461734"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Korzystanie z danych referencyjnych z bazy danych SQL dla zadania usługi Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje usługi Azure SQL Database jako źródło danych wejściowych danych referencyjnych. Bazy danych SQL można użyć jako danych referencyjnych dla zadania usługi Stream Analytics w witrynie Azure portal i w programie Visual Studio za pomocą narzędzi usługi Stream Analytics. W tym artykule przedstawiono, jak to zrobić w obu tych metod.

## <a name="azure-portal"></a>Azure Portal

Użyj następujące kroki, aby dodać bazy danych SQL Azure jako odwołanie do źródła danych wejściowych przy użyciu witryny Azure portal:

### <a name="portal-prerequisites"></a>Wymagania wstępne w portalu

1. Utwórz zadanie usługi Stream Analytics.

2. Utwórz konto magazynu, który będzie używany przez zadanie usługi Stream Analytics.

3. Tworzenie usługi Azure SQL Database przy użyciu zestawu danych, ma być używany jako odwołanie do danych przez zadanie usługi Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Zdefiniuj wejściowych danych referencyjnych bazy danych SQL

1. W ramach zadania usługi Stream Analytics wybierz **dane wejściowe** w obszarze **topologia zadań**. Kliknij przycisk **Dodaj odwołanie do danych wejściowych** i wybierz polecenie **bazy danych SQL**.

   ![Stream Analytics — dane wejściowe zadania](./media/sql-reference-data/stream-analytics-inputs.png)

2. Wypełnij konfiguracji dane wejściowe Stream Analytics. Wybierz nazwę bazy danych, nazwę serwera, nazwę użytkownika i hasło. Dane odwołanie do danych wejściowych w celu okresowego odświeżania, wybierz opcję "On" określić częstotliwość odświeżania w DD:HH:MM. W przypadku dużych zestawów danych w krótkim odświeżania można użyć [zapytania różnicowego](sql-reference-data.md#delta-query).

   ![Konfiguracja odwołanie do bazy danych SQL](./media/sql-reference-data/sql-input-config.png)

3. Testovat dotaz migawki, w edytorze zapytań SQL. Aby uzyskać więcej informacji, zobacz [używać edytora zapytań SQL w witrynie Azure portal do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Wybierz konto magazynu w konfiguracji zadania

Przejdź do **ustawienia konta magazynu** w obszarze **Konfiguruj** i wybierz **Dodaj konto magazynu**.

   ![Ustawienia konta magazynu programu Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Uruchamianie zadania

Po skonfigurowaniu innych danych wejściowych, danych wyjściowych i zapytania, można uruchomić zadania usługi Stream Analytics.

## <a name="tools-for-visual-studio"></a>Narzędzia dla programu Visual Studio

Użyj następujących kroków, aby dodać usługi Azure SQL Database jako odwołanie do źródła danych wejściowych za pomocą programu Visual Studio:

### <a name="visual-studio-prerequisites"></a>Visual Studio wymagań wstępnych.

1. [Zainstaluj narzędzia Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Obsługiwane są następujące wersje programu Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Zapoznanie się z [usługi Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md) Szybki Start.

3. Utwórz konto magazynu.

### <a name="create-a-sql-database-table"></a>Utwórz tabelę bazy danych SQL

Aby utworzyć tabelę do przechowywania danych odwołania, należy użyć programu SQL Server Management Studio. Zobacz [projektowanie pierwszej bazy danych Azure SQL przy użyciu programu SSMS](../sql-database/sql-database-design-first-database.md) Aby uzyskać szczegółowe informacje.

Przykładowa tabela używana w poniższym przykładzie utworzono z następującą instrukcję:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Wybierz subskrypcję

1. W programie Visual Studio w menu **Widok** wybierz opcję **Eksplorator serwera**.

2. Kliknij prawym przyciskiem myszy **Azure**, wybierz opcję **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure**i zaloguj się przy użyciu konta platformy Azure.

### <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. Wybierz pozycję **Plik > Nowy projekt**. 

2. Na liście szablonów po lewej stronie wybierz pozycję **Stream Analytics**, a następnie wybierz pozycję **Azure Stream Analytics Application**. 

3. Wprowadź projektu **nazwa**, **lokalizacji**, i **Nazwa rozwiązania**i wybierz **OK**.

   ![Nowy projekt usługi Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Zdefiniuj wejściowych danych referencyjnych bazy danych SQL

1. Utwórz nowe dane wejściowe.

   ![Nowe usługi Stream Analytics dane wejściowe w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kliknij dwukrotnie **Input.json** w **Eksploratora rozwiązań**.

3. Wypełnij **Stream Analytics dane wejściowe konfiguracji**. Wybierz nazwę bazy danych, nazwę serwera, typ odświeżania i częstotliwość odświeżania. Określ częstotliwość odświeżania w formacie `DD:HH:MM`.

   ![Konfiguracja danych wejściowych Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Jeśli wybierzesz opcję "Tylko raz do wykonania" lub "Okresowo wykonania", jeden plik SQL CodeBehind o nazwie **.snapshot.sql [Alias danych wejściowych]** jest generowany w projekcie, w obszarze **Input.json** węzła pliku.

   ![Wejściowy kodzie w programie Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Jeśli zostanie wybrana opcja "Odśwież okresowo z Delta", zostaną wygenerowane dwa pliki SQL CodeBehind: **.snapshot.sql [Alias danych wejściowych]** i **.delta.sql [Alias danych wejściowych]** .

   ![Możesz pisać kod za zaporą w Eksploratorze rozwiązań](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otwórz plik SQL w edytorze, a następnie napisać zapytanie SQL.

5. Jeśli używasz programu Visual Studio 2019 r i zainstalowano program SQL Server Data tools, możesz sprawdzić zapytanie, klikając **Execute**. Okno Kreator będzie się pojawiać w celu łatwiejszego nawiązania połączenia z bazą danych SQL i wynik zapytania będą wyświetlane w dolnej części okna.

### <a name="specify-storage-account"></a>Określanie konta usługi storage

Otwórz **JobConfig.json** Aby określić konto magazynu do przechowywania migawek SQL na odwołanie.

   ![Stream Analytics zadania konfiguracji w programie Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Lokalne testowanie i wdrażanie na platformie Azure

Przed wdrożeniem zadania na platformie Azure, możesz przetestować logiki zapytania lokalnie w odniesieniu do danych wejściowych na żywo. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio (wersja zapoznawcza) Test](stream-analytics-live-data-local-testing.md). Po zakończeniu testowania, kliknij przycisk **przesyłania na platformie Azure**. Odwołanie [tworzenia usługi Stream Analytics, za pomocą narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md) szybkiego startu, aby dowiedzieć się, jak uruchomić zadanie.

## <a name="delta-query"></a>Zapytania różnicowego

Gdy użycie zapytania różnicowego [tabele danych czasowych w usłudze Azure SQL Database](../sql-database/sql-database-temporal-tables.md) są zalecane.

1. Tworzenie tabeli danych czasowych w usłudze Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Tworzenie zapytań migawki. 

   Użyj  **\@snapshotTime** parametru, aby nakazać środowiska uruchomieniowego usługi Stream Analytics można uzyskać zestawu danych referencyjnych z tabeli bazy danych SQL danych czasowych prawidłowy w czasie systemu. Jeśli nie podano tego parametru, istnieje ryzyko uzyskania niedokładne podstawowego odwołania zestawu danych z powodu wynikających z przesunięcia czasowego zegara. Poniżej przedstawiono przykład Pełna migawka kwerendy:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Możesz tworzyć zapytania różnicowego. 
   
   To zapytanie pobiera wszystkie wiersze w usłudze SQL database, które zostały wstawione lub usuwane w ciągu godziny rozpoczęcia  **\@deltaStartTime**i czas zakończenia  **\@deltaEndTime**. Zapytania różnicowego musi zwracać te same kolumny jako zapytanie migawki, a także kolumny  **_operacji_** . Ta kolumna określa Jeśli wiersz jest wstawionych lub usuniętych między  **\@deltaStartTime** i  **\@deltaEndTime**. Wiersze wynikowe są oznaczane jako **1** rekordy zostały wstawione, lub **2** usunięcie. 

   W przypadku rekordów, które zostały zaktualizowane tabeli danych czasowych wykonuje księgowych, przechwytując operacji wstawiania i usuwania. Środowisko uruchomieniowe usługi Stream Analytics zastosuje następnie wyniki zapytania różnicowego do poprzedniej migawki, aby zapewnić aktualność danych referencyjnych. Przykładem zapytania różnicowego jest wyświetlane poniżej:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Należy pamiętać, że środowisko uruchomieniowe usługi Stream Analytics może okresowo zapytania snapshot oprócz zapytania różnicowego do przechowywania punktów kontrolnych.

## <a name="test-your-query"></a>Testowanie zapytania
   Jest to ważne, aby sprawdzić, czy zapytanie zwraca oczekiwanego zestawu danych, który zadanie usługi Stream Analytics będzie używany jako dane referencyjne. Aby przetestować zapytanie, przejdź do danych wejściowych w sekcji topologia zadania w portalu. Następnie można wybrać danych przykładowych na dane wejściowe użytkownika odwołanie do bazy danych SQL. Po próbki staje się dostępna, możesz pobrać plik i sprawdź, czy dane są zwracane jest co oczekiwano. Chcąc Optymalizuj swoje iteracje projektowania i testowania, zalecane jest użycie [usługi Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Możesz również dowolne inne narzędzie preferowanym najpierw upewnij się, że zapytanie zwraca odpowiednich wyników ze strony użytkownika usługi Azure SQL Database, a następnie użycie, w ramach zadania usługi Stream Analytics. 

## <a name="faqs"></a>Często zadawane pytania

**Czy zostaną naliczone dodatkowych kosztów, za pomocą wejściowych danych referencyjnych SQL w usłudze Azure Stream Analytics?**

Nie dodatkowe [koszt jednostka przesyłania strumieniowego](https://azure.microsoft.com/pricing/details/stream-analytics/) w zadaniu Stream Analytics. Jednak zadanie usługi Stream Analytics musi mieć konto magazynu platformy Azure skojarzone. Zadanie usługi Stream Analytics wysyła zapytanie bazy danych SQL (podczas wykonywania zadania Uruchom i interwał odświeżania) do pobierania zestawu danych referencyjnych i magazynów, które migawki na koncie magazynu. Przechowywania migawek zostaną naliczone dodatkowe opłaty, które szczegółowo opisane w [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/) dla konta usługi Azure storage.

**Skąd mam wiedzieć, migawki danych odwołania jest pobierane z bazy danych SQL i używane w ramach zadania usługi Azure Stream Analytics?**

Istnieją dwie metryki przefiltrowane według nazwy logicznej (w ramach witryny Azure Portal metryki), które służy do monitorowania prawidłowości danych referencyjnych bazy danych SQL, dane wejściowe.

   * Liczba: Ta metryka mierzy liczbę rekordów załadowane w z zestawu danych referencyjnych bazy danych SQL.
   * InputEventBytes: Ta metryka mierzy rozmiar migawek chronionych danych odwołania, które są ładowane w pamięci w procentach zadania usługi Stream Analytics. 

Kombinację obu tych metryk można wywnioskować, jeśli zadanie jest zapytanie do bazy danych SQL, można pobrać zestawu danych referencyjnych i załadowanie go do pamięci.

**Czy będzie wymagać specjalny rodzaj usługi Azure SQL Database?**

Usługa Azure Stream Analytics będzie działać z dowolnego typu usługi Azure SQL Database. Jednak ważne jest zrozumienie, że częstotliwość odświeżania dla Twojego wejściowych danych referencyjnych może mieć wpływ na Twoje obciążenie zapytaniami. Aby użyć opcji zapytanie różnicowe, zalecane jest korzystanie z tabel danych czasowych w usłudze Azure SQL Database.

**Dlaczego Azure Stream Analytics przechowuje migawki na koncie magazynu platformy Azure?**

Poza tym gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń. W przypadkach, w którym przejściowe problemy z wpływu na zadania małą ilością powtórzeń jest niezbędne do przywrócenia stanu. Aby włączyć powtarzania, jest wymagane posiadanie migawek przechowywanych na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat powtarzania punktu kontrolnego, zobacz [punkt kontrolny i powtarzanie pojęcia dotyczące zadań usługi Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu danych referencyjnych dla wyszukiwania w usłudze Stream Analytics](stream-analytics-use-reference-data.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio (wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)
