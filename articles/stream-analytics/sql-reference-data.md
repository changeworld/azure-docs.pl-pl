---
title: Używanie danych referencyjnych bazy danych SQL w zadaniu usługi Azure Stream Analytics
description: W tym artykule opisano sposób używania bazy danych SQL jako danych wejściowych dla zadania usługi Azure Stream Analytics w witrynie Azure portal i w programie Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426498"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Używanie danych referencyjnych z bazy danych SQL dla zadania usługi Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje usługę Azure SQL Database jako źródło danych wejściowych dla danych referencyjnych. Baza danych SQL można użyć jako dane referencyjne dla zadania usługi Stream Analytics w witrynie Azure portal i visual studio za pomocą narzędzi usługi Stream Analytics. W tym artykule pokazano, jak wykonać obie metody.

## <a name="azure-portal"></a>Portal Azure

Aby dodać usługę Azure SQL Database jako źródło danych referencyjnych przy użyciu witryny Azure Portal, należy wykonać następujące kroki:

### <a name="portal-prerequisites"></a>Wymagania wstępne portalu

1. Tworzenie zadania usługi Stream Analytics

2. Utwórz konto magazynu, które będzie używane przez zadanie usługi Stream Analytics.

3. Utwórz usługę Azure SQL Database z zestawem danych, który ma być używany jako dane referencyjne przez zadanie usługi Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definiowanie danych wejściowych referencyjnych bazy danych SQL

1. W zadaniu usługi Stream Analytics wybierz pozycję **Wejścia w** obszarze **Topologia zadań**. Kliknij **pozycję Dodaj dane wejściowe odwołania** i wybierz pozycję Baza danych **SQL**.

   ![Dane wejściowe usługi Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Wypełnij konfiguracje wejściowe usługi Stream Analytics. Wybierz nazwę bazy danych, nazwę serwera, nazwę użytkownika i hasło. Jeśli chcesz, aby dane referencyjne były okresowo odświeżane, wybierz "On", aby określić częstotliwość odświeżania w DD:HH:MM. Jeśli masz duże zestawy danych z krótką częstotliwością odświeżania, możesz użyć [kwerendy różnicowej](sql-reference-data.md#delta-query).

   ![Konfiguracja odwołania do bazy danych SQL](./media/sql-reference-data/sql-input-config.png)

3. Przetestuj kwerendę migawką w edytorze zapytań SQL. Aby uzyskać więcej informacji, zobacz Łączenie się i wykonywanie zapytań o dane za [pomocą edytora zapytań SQL w portalu Azure](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Określanie konta magazynu w konfiguracji zadania

Przejdź do **ustawień konta magazynu** w obszarze **Konfigurowanie** i wybierz pozycję **Dodaj konto magazynu**.

   ![Ustawienia konta magazynu usługi Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Uruchamianie zadania

Po skonfigurowaniu innych danych wejściowych, wyjść i kwerendy można uruchomić zadanie usługi Stream Analytics.

## <a name="tools-for-visual-studio"></a>Narzędzia dla programu Visual Studio

Aby dodać usługę Azure SQL Database jako źródło danych referencyjnych przy użyciu programu Visual Studio, należy wykonać następujące kroki:

### <a name="visual-studio-prerequisites"></a>Wymagania wstępne programu Visual Studio

1. [Zainstaluj narzędzia analizy strumienia dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Obsługiwane są następujące wersje programu Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Zapoznaj się z [narzędziami analizy strumienia dla programu Visual Studio](stream-analytics-quick-create-vs.md) Szybki start.

3. Tworzenie konta magazynu

### <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Program SQL Server Management Studio służy do tworzenia tabeli do przechowywania danych referencyjnych. Zobacz [Projektowanie pierwszej bazy danych SQL platformy Azure przy użyciu usługi SSMS, aby](../sql-database/sql-database-design-first-database.md) uzyskać szczegółowe informacje.

Przykładowa tabela użyta w poniższym przykładzie została utworzona na podstawie następującej instrukcji:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Wybierz subskrypcję

1. W programie Visual Studio w menu **Widok** wybierz opcję **Eksplorator serwera**.

2. Kliknij prawym przyciskiem myszy **pozycję Azure**, wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure**i zaloguj się za pomocą konta platformy Azure.

### <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. Wybierz pozycję **Plik > Nowy projekt**. 

2. Na liście szablonów po lewej stronie wybierz pozycję **Stream Analytics**, a następnie wybierz pozycję **Azure Stream Analytics Application**. 

3. Wprowadź **nazwę**projektu, **lokalizację**i **nazwę rozwiązania**i wybierz przycisk **OK**.

   ![Nowy projekt usługi Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definiowanie danych wejściowych referencyjnych bazy danych SQL

1. Utwórz nowe dane wejściowe.

   ![Nowe dane wejściowe usługi Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kliknij dwukrotnie **plik Input.json** w **Eksploratorze rozwiązań**.

3. Wypełnij **konfigurację wprowadzania usługi Stream Analytics**. Wybierz nazwę bazy danych, nazwę serwera, typ odświeżania i częstotliwość odświeżania. Określ częstotliwość odświeżania `DD:HH:MM`w formacie .

   ![Konfiguracja wprowadzania usługi Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Jeśli wybierzesz opcję "Wykonaj tylko raz" lub "Wykonaj okresowo", jeden plik SQL CodeBehind o nazwie **[Input Alias].snapshot.sql** jest generowany w projekcie w węźle pliku **Input.json.**

   ![Kod wprowadzania w programie Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Jeśli wybierzesz opcję "Odśwież okresowo z Delta", zostaną wygenerowane dwa pliki SQL CodeBehind: **[Input Alias].snapshot.sql** i **[Input Alias].delta.sql**.

   ![Kod w eksploratorze rozwiązań](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otwórz plik SQL w edytorze i napisz kwerendę SQL.

5. Jeśli używasz programu Visual Studio 2019 i zainstalowano narzędzia SQL Server Data, możesz przetestować kwerendę, klikając przycisk **Wykonaj**. Pojawi się okno kreatora, aby ułatwić łączenie się z bazą danych SQL, a wynik kwerendy pojawi się w oknie u dołu.

### <a name="specify-storage-account"></a>Określanie konta magazynu

Otwórz **jobconfig.json,** aby określić konto magazynu do przechowywania migawek odwołań SQL.

   ![Konfiguracja zadania usługi Stream Analytics w programie Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testowanie lokalnie i wdrażanie na platformie Azure

Przed wdrożeniem zadania na platformie Azure można przetestować logikę zapytania lokalnie względem danych wejściowych na żywo. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio (Wersja zapoznawcza)](stream-analytics-live-data-local-testing.md). Po zakończeniu testowania kliknij przycisk **Prześlij na platformę Azure**. Odwołaj się [do tworzenia usługi Stream Analytics przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md) Szybki start, aby dowiedzieć się, jak rozpocząć zadanie.

## <a name="delta-query"></a>Kwerenda delta

Podczas korzystania z kwerendy różnicowej zaleca się [tabele czasowe w usłudze Azure SQL Database.](../sql-database/sql-database-temporal-tables.md)

1. Tworzenie tabeli czasowej w bazie danych SQL azure.
   
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
2. Autor kwerendy migawki. 

   Użyj ** \@parametru snapshotTime,** aby poinstruować środowisko uruchomieniowe usługi Stream Analytics w celu uzyskania zestawu danych referencyjnych z tabeli czasowej bazy danych SQL prawidłowej w czasie systemowym. Jeśli nie podasz tego parametru, istnieje ryzyko uzyskania niedokładny zestaw danych referencyjnych podstawowych ze względu na pochylenia zegara. Poniżej przedstawiono przykład pełnej migawki:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Autor kwerendy różnicowej. 
   
   Ta kwerenda pobiera wszystkie wiersze w bazie danych SQL, które zostały wstawione lub usunięte w czasie rozpoczęcia, ** \@deltaStartTime**i ** \@deltaEndTime**czasu zakończenia . Kwerenda delta musi zwracać te same kolumny co kwerenda migawki, a także **_operację_** kolumny . Ta kolumna określa, czy wiersz jest wstawiany lub usuwany między ** \@deltaStartTime** i ** \@deltaEndTime**. Wynikowe wiersze są oflagowane jako **1,** jeśli rekordy zostały wstawione, lub **2,** jeśli zostaną usunięte. 

   W przypadku rekordów, które zostały zaktualizowane, tabela czasowa wykonuje księgowość, przechwytując operację wstawiania i usuwania. Środowisko uruchomieniowe usługi Stream Analytics zastosuje wyniki kwerendy różnicowej do poprzedniej migawki, aby zachować aktualną datę danych referencyjnych. Przykład kwerendy różnicowej jest pokazany poniżej:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Należy zauważyć, że środowisko uruchomieniowe usługi Stream Analytics może okresowo uruchamiać kwerendę migawki oprócz kwerendy różnicowej do przechowywania punktów kontrolnych.

## <a name="test-your-query"></a>Testowanie zapytania
   Ważne jest, aby sprawdzić, czy zapytanie zwraca oczekiwany zestaw danych, który zadanie usługi Stream Analytics będzie używane jako dane referencyjne. Aby przetestować zapytanie, przejdź do sekcji Wprowadzanie w obszarze Topologia zadań w portalu. Następnie można wybrać przykładowe dane na danych wejściowych odwołania do bazy danych SQL. Po udostępnieniu próbki można pobrać plik i sprawdzić, czy zwracane dane są zgodnie z oczekiwaniami. Jeśli chcesz zoptymalizować rozwój i przetestować iteracje, zaleca się użycie [narzędzi analizy strumienia dla programu Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) Można również dowolne inne narzędzie, aby najpierw upewnić się, że kwerenda zwraca odpowiednie wyniki z usługi Azure SQL Database, a następnie użyj go w zadaniu usługi Stream Analytics. 

## <a name="faqs"></a>Często zadawane pytania

**Czy poniesienie dodatkowych kosztów przy użyciu danych referencyjnych SQL w usłudze Azure Stream Analytics?**

W zadaniu usługi Stream Analytics nie ma żadnych dodatkowych [kosztów jednostki przesyłania strumieniowego.](https://azure.microsoft.com/pricing/details/stream-analytics/) Jednak zadanie usługi Stream Analytics musi mieć skojarzone konto magazynu platformy Azure. Zadanie usługi Stream Analytics wysyła zapytanie do bazy danych SQL (podczas interwału uruchamiania i odświeżania zadania), aby pobrać zestaw danych referencyjnych i przechowuje tę migawkę na koncie magazynu. Przechowywanie tych migawek spowoduje naliczenie dodatkowych opłat wyszczególnionych na [stronie cennika](https://azure.microsoft.com/pricing/details/storage/) konta magazynu platformy Azure.

**Skąd mam wiedzieć, migawka danych referencyjnych jest wyszukiwana z bazy danych SQL DB i używana w zadaniu usługi Azure Stream Analytics?**

Istnieją dwie metryki filtrowane według nazwy logicznej (w obszarze Metryki Azure Portal), których można użyć do monitorowania kondycji danych wejściowych referencyjnej bazy danych SQL.

   * InputEvents: Ta metryka mierzy liczbę rekordów załadowanych z zestawu danych referencyjnych bazy danych SQL.
   * InputEventBytes: Ta metryka mierzy rozmiar migawki danych referencyjnych załadowanych do pamięci zadania usługi Stream Analytics. 

Połączenie obu tych metryk może służyć do wnioskowania, jeśli zadanie jest kwerendy bazy danych SQL, aby pobrać zestaw danych referencyjnych, a następnie załadowanie go do pamięci.

**Czy potrzebuję specjalnego typu bazy danych SQL Azure?**

Usługa Azure Stream Analytics będzie współpracować z dowolnym typem usługi Azure SQL Database. Jednak ważne jest, aby zrozumieć, że częstotliwość odświeżania ustawiona dla danych referencyjnych danych wejściowych może mieć wpływ na obciążenie zapytania. Aby użyć opcji kwerendy różnicowej, zaleca się użycie tabel czasowych w bazie danych SQL azure.

**Dlaczego usługa Azure Stream Analytics przechowuje migawki na koncie usługi Azure Storage?**

Poza tym gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń. W przypadkach, gdy przejściowe problemy mają wpływ na zadanie, niewielka ilość powtarzania jest konieczne, aby przywrócić stan. Aby włączyć powtarzanie, wymagane jest, aby te migawki przechowywane na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat powtarzania punktów kontrolnych, zobacz [Pojęcia dotyczące punktu kontrolnego i powtarzania w zadaniach usługi Azure Stream Analytics.](stream-analytics-concepts-checkpoint-replay.md)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z danych referencyjnych dla odnośnych w usłudze Stream Analytics](stream-analytics-use-reference-data.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu narzędzi usługi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio (Wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)
