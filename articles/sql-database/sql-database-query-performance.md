---
title: Wyszukiwać szczegółowe informacje o wydajności usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Monitorowanie wydajności zapytania określa większość zapytań korzystających z procesora CPU dla usługi Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585378"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight dla usługi Azure SQL Database

Zarządzanie i dostosowywanie wydajności relacyjnych baz danych trwa wiedzę i czas. Query Performance Insight jest częścią linia produktów inteligentnych wydajności usługi Azure SQL Database. Ułatwia on poświęcają mniej czasu na rozwiązywanie problemów z wydajnością bazy danych, zapewniając:

* Lepszy wgląd w swoje użycie zasobów (DTU) baz danych.
* Szczegółowe informacje dotyczące zapytań najważniejsze bazy danych według liczby procesorów, czas trwania i wykonywania (potencjalne dostrajania kandydatów zwiększające wydajność).
* Przechodzenie do szczegółów szczegółowe zapytania, aby wyświetlić tekst zapytania i historii wykorzystania zasobów.
* Adnotacje, które pokazują zalecenia dotyczące wydajności z [SQL Database Advisor](sql-database-advisor.md).

![Szczegółowe informacje o wydajności zapytań](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Podstawowe monitorowanie wydajności za pomocą usługi Azure SQL Database, zalecamy Query Performance Insight. Należy pamiętać, ograniczenia produktu, opublikowane w tym artykule. Zaawansowane monitorowanie wydajności bazy danych w dużej skali, firma Microsoft zaleca [usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md). Zawiera wbudowaną inteligencją dla Rozwiązywanie problemów z wydajnością automatycznych. Aby automatycznie dopasowuj typowe problemy z wydajnością bazy danych, zaleca się [dostrajania automatycznego](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymaga szczegółowych informacji o wydajności zapytań [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) jest aktywny w bazie danych. Jego jest automatycznie domyślnie dla wszystkich baz danych Azure SQL. Jeśli Query Store nie jest uruchomiona, witryny Azure portal wyświetli monit, aby ją włączyć.

> [!NOTE]
> Jeśli w portalu pojawi się komunikat "Query Store nie jest poprawnie skonfigurowany dla tej bazy danych", zobacz [Optymalizacja konfiguracji Query Store](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Uprawnienia

Wymagane są następujące elementy [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) uprawnienia do korzystania z Query Performance Insight:

* **Czytnik**, **właściciela**, **Współautor**, **Współautor bazy danych SQL**, lub **Współautor serwera SQL Server** nie są wymagane uprawnienia Aby przejrzeć najpopularniejsze zapytania korzystające z zasobów i wykresy.
* **Właściciel**, **Współautor**, **Współautor bazy danych SQL**, lub **Współautor serwera SQL Server** są wymagane uprawnienia, aby wyświetlić tekst zapytania.

## <a name="use-query-performance-insight"></a>Korzystanie ze szczegółowych informacji o wydajności zapytań

Query Performance Insight jest łatwa w użyciu:

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i znaleźć bazy danych, który chcesz zbadać.
2. Z menu po lewej stronie, otwórz **inteligentne wydajności** > **Query Performance Insight**.
  
   ![Query Performance Insight menu](./media/sql-database-query-performance/tile.png)

3. Na pierwszej karcie przejrzyj listę najpopularniejsze zapytania korzystające z zasobów.
4. Wybierz określone zapytanie, aby wyświetlić jego szczegóły.
5. Otwórz **inteligentne wydajności** > **zalecenia dotyczące wydajności** i sprawdź, czy wszystkie zalecenia dotyczące wydajności są dostępne. Aby uzyskać więcej informacji na temat zalecenia dotyczące wydajności wbudowanych, zobacz [SQL Database Advisor](sql-database-advisor.md).
6. Użyj suwaków lub Powiększ, aby zmienić interwał obserwowanych ikon.

   ![pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Usługi SQL Database mają być renderowane informacje w Query Performance Insight Query Store musi przechwytywać dane z kilku godzin. Jeśli baza danych nie ma żadnych działań lub Query Store nie był aktywny w pewnym okresie, wykresy będzie pusta, gdy Query Performance Insight wyświetla ten zakres czasu. Query Store można włączyć w dowolnym momencie, jeśli nie jest uruchomiona. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania z Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Przejrzeć najpopularniejsze zapytania korzystające z procesora CPU

Domyślnie Query Performance Insight pokazano pierwszych pięciu zapytaniach korzystających z procesora CPU, przy pierwszym otwarciu.

1. Zaznacz lub wyczyść pojedynczych zapytań do dołączania lub wykluczania ich z wykresu za pomocą pola wyboru.

    Górny wiersz zawiera ogólną procentowa wartość jednostki DTU na bazę danych. Słupki pokazują procent użycia procesora CPU, które wybrane zapytanie użyte przez wybrany przedział czasu. Na przykład jeśli **ostatni tydzień** jest zaznaczone, Każdy słupek reprezentuje jeden dzień.

    ![Najpopularniejsze zapytania](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Wiersz jednostek DTU, wyświetlane są agregowane wartości maksymalne użycie w okresach jedną godzinę. Jest przeznaczona dla wysokiego poziomu porównanie tylko w przypadku statystyk wykonywania zapytań. W niektórych przypadkach użycie jednostek DTU mogą wydawać się zbyt duże w porównaniu do wykonywane zapytania, ale to może nie zadziałać.
   >
   > Na przykład jeśli zapytanie maksymalnego limitu jednostek DTU do 100% tylko przez kilka minut, wiersz jednostek DTU w Query Performance Insight pokaże całą godzinę zużycie 100% (niejednoznacznego określenia maksymalnej wartości zagregowane).
   >
   > Porównanie bardziej precyzyjną (maksymalnie jedną minutę) należy rozważyć utworzenie niestandardowego wykresu wykorzystanie jednostek DTU:
   >
   > 1. W witrynie Azure portal wybierz **usługi Azure SQL Database** > **monitorowanie**.
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz **+ Dodaj wykres**.
   > 4. Wybierz procent jednostek DTU na wykresie.
   > 5. Ponadto wybierz **ostatnie 24 godziny** w lewym górnym menu i zmienić ją na jedną minutę.
   >
   > Za pomocą niestandardowego wykresu jednostek DTU bardziej precyzyjną poziom szczegółów do porównania z wykresem wykonywania zapytania.

   Siatka dolnej zawierają zagregowane informacje widoczne zapytań:

   * Identyfikator zapytania, który jest unikatowy identyfikator dla zapytania w bazie danych.
   * Procesor zapytań dostrzegalnych przedział czasu, która zależy od funkcji agregacji.
   * Czas trwania jednego zapytania, które zależy także funkcję agregacji.
   * Łączna liczba wykonań dla określonej kwerendy.

2. Jeśli dane się starzeją, wybierz opcję **Odśwież** przycisku.

3. Za pomocą suwaków i powiększenia przycisków, aby zmienić interwał obserwacji i zbadać wzrostów użycia:

   ![Suwaki i przycisków powiększenia zmiany interwału](./media/sql-database-query-performance/zoom.png)

4. Opcjonalnie można wybrać **niestandardowe** kartę, aby dostosować widok dla:

   * Metryki (procesora CPU lub czas trwania, liczba wykonań).
   * Przedział czasu (ostatnie 24 godziny, ostatni tydzień lub Ubiegły miesiąc).
   * Liczba zapytań.
   * Funkcją agregacji.
  
   ![Karta niestandardowa](./media/sql-database-query-performance/custom-tab.png)
  
5. Wybierz **Przejdź >** przycisk, aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Query Performance Insight jest ograniczona do wyświetlania górnej zapytania zużywające 5-20, w zależności od wyboru. Bazy danych można uruchomić wiele więcej zapytań poza największymi wyświetlane, a te zapytania nie zostanie uwzględniona na wykresie.
   >
   > Może istnieć typu obciążenia bazy danych, w której wiele mniejszych zapytaniach poza największymi pokazano, często i korzystać z większości jednostek DTU. Te zapytania nie są wyświetlane na wykresie wydajności.
   >
   > Na przykład zapytania może wykorzystali znacznej ilości jednostek DTU od pewnego czasu, ale jej całkowite użycie w okresie obserwacji jest mniejsza niż inne korzystanie z góry zapytania. W takim przypadku wykorzystanie zasobów tego zapytania nie było wyświetlane na wykresie.
   >
   > Jeśli zachodzi potrzeba wykonania zapytania dotyczącego początkowych poza ograniczenia Query Performance Insight zrozumieć, rozważ użycie [usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) wydajności Zaawansowane bazy danych monitorowania i rozwiązywania problemów.
   >

## <a name="view-individual-query-details"></a>Wyświetlanie szczegółów poszczególnych zapytań

Aby wyświetlić szczegóły kwerendy:

1. Wybierz dowolne zapytanie na liście najpopularniejsze zapytania.

    ![Lista najpopularniejsze zapytania](./media/sql-database-query-performance/details.png)

   Zostanie otwarty widok szczegółowy. Pokazuje użycie procesora CPU, czas trwania i liczby wykonań wraz z upływem czasu.

2. Wybierz funkcje wykresu, aby uzyskać szczegółowe informacje.

   * Górny wykres pokazuje linii z ogólną procentowa wartość jednostki DTU dla bazy danych. Słupki są procent użycia procesora CPU, które używane przez wybrane zapytanie.
   * Drugi wykres przedstawia łączny czas trwania wybranego zapytania.
   * Wykres dołu pokazuje łącznej liczby wykonań przez wybrane zapytanie.

   ![Szczegóły zapytania](./media/sql-database-query-performance/query-details.png)

3. Opcjonalnie za pomocą suwaków, użyj przycisków powiększenia lub wybierz **ustawienia** dostosować sposób wyświetlania zapytania o dane lub wybierz inny zakres czasu.

   > [!IMPORTANT]
   > Query Performance Insight nie przechwytuje wszystkie kwerendy DDL. W niektórych przypadkach może być przechwytuje się wszystkie zapytania ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Przejrzeć najpopularniejsze zapytania na czas trwania

Dwie metryki w Query Performance Insight ułatwia znajdowanie wąskich gardeł: czas trwania i wykonywania licznik.

Długotrwałe zapytania mają największe możliwości blokowania zasobów dłużej blokowania innym użytkownikom oraz ograniczenie skalowalności. Są one też wybrać najlepszych kandydatów do optymalizacji.

Aby zidentyfikować długo wykonywane zapytania:

1. Otwórz **niestandardowe** karcie Query Performance Insight dla wybranej bazy danych.
2. Metryki, aby zmienić **czas trwania**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz funkcję agregacji:

   * **Suma** dodaje się cały czas wykonywania zapytania dla interwału całego obserwacji.
   * **Maksymalna liczba** znajduje zapytania w czasie wykonywania, który został maksymalny interwał całego obserwacji.
   * **Średnia liczba** znajdzie Średni czas wykonania wszystkich wykonań zapytania i pokazuje największymi dla tych wartości średnie.

   ![Czas trwania zapytania](./media/sql-database-query-performance/top-duration.png)

5. Wybierz **Przejdź >** przycisk, aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Dostosowywanie widoku zapytania nie powoduje aktualizacji wiersza jednostek DTU. Wiersz jednostek DTU zawsze wyświetla wartość maksymalnego użycia przez dany interwał.
   >
   > Aby poznać użycie jednostek DTU bazy danych przy użyciu bardziej szczegółowo (maksymalnie jedną minutę), należy rozważyć utworzenie niestandardowego wykresu w witrynie Azure portal:
   >
   > 1. Wybierz **usługi Azure SQL Database** > **monitorowania**.
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz **+ Dodaj wykres**.
   > 4. Wybierz procent jednostek DTU na wykresie.
   > 5. Ponadto wybierz **ostatnie 24 godziny** w lewym górnym menu i zmienić ją na jedną minutę.
   >
   > Zalecamy użycie niestandardowego wykresu jednostek DTU do porównania z Wykres wydajności zapytania.
   >

## <a name="review-top-queries-per-execution-count"></a>Przejrzeć najpopularniejsze zapytania według liczby wykonań

Aplikacja użytkownika, która korzysta z bazy danych może być get wolne, mimo że dużą liczbę wykonań może nie mieć wpływ na samej bazy danych i użycie zasobów jest niskie.

W niektórych przypadkach liczba wykonań Wysoki może prowadzić do bardziej rund sieci. Dwustronne wpłynąć na wydajność. Są one zastrzeżeniem opóźnienia sieci i na czas oczekiwania serwera podrzędnego.

Na przykład wiele witryn internetowych, które są oparte na danych intensywnie dostęp do bazy danych dla każdego żądania użytkownika. Mimo że pula połączeń pomaga ruch sieciowy zwiększone i obciążenie na serwerze bazy danych może zmniejszyć wydajność. Ogólnie rzecz biorąc Zachowaj rund do minimum.

Aby zidentyfikować często wykonywane zapytania ("dużą liczbą"):

1. Otwórz **niestandardowe** karcie Query Performance Insight dla wybranej bazy danych.
2. Metryki, aby zmienić **liczby wykonań**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz **Przejdź >** przycisk, aby wyświetlić dostosowany widok.

   ![Liczba wykonań zapytania](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Zrozumienie adnotacje dostrajania wydajności

Podczas eksplorowania obciążenie w Query Performance Insight, można zauważyć ikony z pionowym wierszem na podstawie wykresu.

Te ikony są adnotacji. Pokazują one zalecenia dotyczące wydajności z [SQL Database Advisor](sql-database-advisor.md). Ustawiając kursor adnotacji, możesz uzyskać podsumowanie informacji na zalecenia dotyczące wydajności.

   ![Adnotacja zapytania](./media/sql-database-query-performance/annotation.png)

Jeśli chcesz dowiedzieć się więcej lub zastosować zalecenia usługi advisor, wybierz ikonę aby otworzyć szczegóły zalecaną akcję. Jeśli jest to aktywna Rekomendacja, można zastosować go następnie od razu z poziomu portalu.

   ![Szczegóły adnotacji zapytania](./media/sql-database-query-performance/annotation-details.png)

W niektórych przypadkach, ze względu na poziom powiększenia jest możliwe, że adnotacje blisko siebie, są zwinięte do pojedynczego adnotacji. Query Performance Insight reprezentuje to ikona adnotacja grupy. Wybierając ikonę adnotacja grupy zostanie otwarty nowy blok zawierający adnotacje.

Korelację zapytań i akcji dostrajania wydajności mogą pomóc lepiej zrozumieć obciążenia.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Optymalizacja konfiguracji Query Store Query Performance Insight

Podczas korzystania z Query Performance Insight, można napotkać następujące komunikaty o błędach Query Store:

* "Query Store nie jest prawidłowo skonfigurowany na tej bazy danych. "Kliknij tutaj, aby dowiedzieć się więcej".
* "Query Store nie jest prawidłowo skonfigurowany na tej bazy danych. "Kliknij tutaj, aby zmienić ustawienia".

Te komunikaty są zwykle wyświetlane, gdy Query Store nie można zebrać nowych danych.

Pierwszy przypadek występuje, gdy Query Store jest w stanie tylko do odczytu, a parametry są ustawione optymalnie. Można rozwiązać ten problem, przez zwiększenie rozmiaru magazynu danych lub usuwając Query Store. (Po wyczyszczeniu Query Store wszystkie uprzednio zebrane dane telemetryczne zostaną utracone.)

   ![Szczegóły Query Store](./media/sql-database-query-performance/qds-off.png)

Drugi przypadek występuje, gdy nie włączono Query Store lub parametry nie są ustawione optymalnie. Zmień zasady przechowywania i przechwytywania, a także włączyć Query Store, uruchamiając następujące polecenia, które są dostarczane z [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witrynie Azure portal.

### <a name="recommended-retention-and-capture-policy"></a>Zalecane zasady dotyczące przechowywania i przechwytywania

Istnieją dwa typy zasad przechowywania:

* **Rozmiar na podstawie**: Jeśli zasada ta jest równa **AUTOMATYCZNIE**, wyczyści jego dane automatycznie, gdy zostanie osiągnięty maksymalny rozmiar w pobliżu.
* **Czas na podstawie**: Domyślnie ta zasada jest równa 30 dni. Query Store wyczerpaniu wolnego miejsca, usunie informacje o kwerendzie starsze niż 30 dni.

Można ustawić zasady przechwytywania,:

* **Wszystkie**: Query Store przechwytuje wszystkie zapytania.
* **Automatyczne**: Query Store ignoruje rzadkie zapytania i z nieznaczące czas kompilacji i wykonywania zapytań. Progi dla liczby wykonań, skompilować czas trwania, a czas wykonywania wewnętrznego są określane. Jest to opcja domyślna.
* **Brak**: Query Store zatrzymuje przechwytywanie nowych zapytań, ale nadal zbieranymi statystyki środowiska uruchomieniowego dla zapytań już przechwycony.

Firma Microsoft zaleca ustawienie wszystkich zasad **AUTOMATYCZNIE** i czyszczenia zasad do 30 dni, wykonując następujące polecenia w [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witrynie Azure portal. (Zastąp `YourDB` nazwą bazy danych.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zwiększ rozmiar Query Store, nawiązując połączenie z bazą danych za pośrednictwem [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witryny Azure portal i uruchamiając następujące zapytanie. (Zastąp `YourDB` nazwą bazy danych.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Stosowanie tych ustawień po pewnym czasie spowoduje, że Query Store zbieranie danych telemetrycznych dla nowych zapytań. Jeśli potrzebujesz Store zapytania, aby działać natychmiast, można opcjonalnie wyczyść Query Store, uruchamiając następujące zapytanie za pomocą programu SSMS lub witryny Azure portal. (Zastąp `YourDB` nazwą bazy danych.)

> [!NOTE]
> Uruchamiając następujące zapytanie usunie wszystkie uprzednio zebrane monitorowane dane telemetryczne w Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Podsumowanie

Query Performance Insight ułatwia zrozumienie wpływu na obciążenie zapytań oraz jej do zużycia zasobów bazy danych. Dzięki tej funkcji poznasz korzystanie z góry zapytań w bazie danych, a można znaleźć zapytania, aby zoptymalizować, zanim staną się problem.

## <a name="next-steps"></a>Kolejne kroki

* Zalecenia dotyczące wydajności bazy danych, można wybrać [zalecenia](sql-database-advisor.md) bloku Query Performance Insight nawigacji.

    ![Karta zaleceń](./media/sql-database-query-performance/ia.png)

* Należy rozważyć włączenie [dostrajania automatycznego](sql-database-automatic-tuning.md) dla typowych problemów z wydajnością bazy danych.
* Dowiedz się, jak [Intelligent Insights](sql-database-intelligent-insights.md) mogą pomóc automatycznie rozwiązać problemy z wydajnością bazy danych.
* Należy rozważyć użycie [usługi Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) zaawansowanych wydajności monitorowania floty dużych baz danych, pul elastycznych i wystąpienia zarządzane SQL dzięki wbudowanym funkcjom analizy.
