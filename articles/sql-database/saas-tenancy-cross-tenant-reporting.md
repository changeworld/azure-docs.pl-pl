---
title: Uruchom raportowania zapytań przez wiele baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Dzierżawcy między raportowania przy użyciu rozproszonego zapytania.
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptuals
ms.date: 04/01/2018
ms.author: billgib
ms.reviewer: sstein; AyoOlubeko
ms.openlocfilehash: 4738f20ad647e65abffdb65ef350c3c0ad4d2f8f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646106"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Zapytania rozproszone Cross dzierżawy raportowania przy użyciu

W tym samouczku możesz uruchomić zapytań rozproszonych przez cały zestaw dzierżawy baz danych raportowania. Te zapytania można wyodrębnić wgląd w codziennych danych operacyjnych dzierżaw SaaS biletów Wingtip stosie. W tym celu należy wdrożyć dodatkowe bazy danych raportowania na serwerze wykazu i umożliwiają elastyczne zapytania zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Jak wdrożyć bazy danych raportowania
> * Jak uruchamiać zapytania rozproszone dla wszystkich baz danych dzierżawy
> * Jak widoki globalne w każdej bazie danych umożliwiają wydajne wykonywanie zapytań w dzierżawców


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:


* Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować narzędzia SSMS, zobacz [pobierania programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Wzorzec raportowania między dzierżawy

![wzorzec zapytania rozproszonego między dzierżawy](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Możliwość jednej z aplikacji SaaS jest Użyj ogromnych ilości danych dzierżawy przechowywane w chmurze, aby uzyskać wgląd w działanie i użycia aplikacji. Te szczegółowe informacje można przeprowadzają opracowanie funkcji, ulepszenia użyteczność i innych inwestycji w aplikacji i usług.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z podejść jest użycie [elastycznej zapytania](sql-database-elastic-query-overview.md), które umożliwia wykonywanie zapytań w rozproszonej zestaw baz danych z wspólny schemat. Te bazy danych mogą być rozproszone na różnych grup zasobów i subskrypcje, ale muszą udostępniać wspólnej nazwy logowania. Elastyczne zapytanie używa pojedynczego *head* bazy danych, w którym zdefiniowano tabel zewnętrznych który duplikatów tabel lub widoków w bazach danych rozproszonych (dzierżawcy). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa mapy niezależnego fragmentu w bazie danych katalogu do określenia lokalizacji wszystkie dzierżawy bazy danych. Instalator i zapytania head bazy danych są oczywiste, przy użyciu standardu [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługuje zapytań z narzędzi, takich jak usługi Power BI i Excel.

Przekazując kwerendy bazy danych dzierżawy, elastycznej zapytania zapewnia błyskawiczny wgląd w danych produkcyjnych na żywo. Jak elastycznej zapytanie pobiera dane z potencjalnie wiele baz danych, z kwerendami może być większa niż równoważne zapytania przesłane do pojedynczej bazy danych wielu dzierżawców. Projektowanie zapytania zminimalizowanie ilości danych, która jest zwracana do głównego bazy danych. Elastyczne zapytania jest często najlepiej dopasowane do badania niewielkich ilości danych w czasie rzeczywistym, w przeciwieństwie do budynku często używane lub analityka złożonych kwerend lub raportów. Jeśli kwerendy nie wykonać dobrze, obejrzyj [plan wykonania](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) jaka część zapytania jest przesuwana do zdalnej bazy danych i ilość danych, które zostały zwrócone. Zapytania, które wymagają złożoną agregację lub przetwarzania analitycznego mogą być lepiej uchwytów przez wyodrębniania danych dzierżawy do magazynu bazy danych albo dane zoptymalizowane pod kątem zapytania analityczne. Ten wzorzec zostało wyjaśnione w dokumencie [samouczek analizy dzierżawy](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy

Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Do wykonywania kwerend do bardziej interesującego zestawu danych, należy utworzyć danych sprzedaży biletów, uruchamiając generatora biletu.

1. W *PowerShell ISE*, Otwórz... \\Modułów uczenia\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* skryptów i ustawić następującą wartość:
   * **$DemoScenario** = 1, **zakupu biletów zdarzeń na wszystkich miejsc**.
2. Naciśnij klawisz **F5** do uruchomienia skryptu i generowania sprzedaży biletów. Po uruchomieniu skryptu nadal kroki opisane w tym samouczku. Dane biletu jest poddawany kwerendzie w *uruchamianie zapytań ad hoc distributed* sekcji, więc Zaczekaj generatora biletu, aby zakończyć.

## <a name="explore-the-global-views"></a>Eksploruj widoki globalne

W aplikacji Wingtip biletów SaaS bazy danych dla dzierżawy każdego dzierżawcy otrzymuje bazy danych. W związku z tym perspektywy pojedynczej dzierżawy obejmuje dane zawarte w tabelach bazy danych. Jednak podczas wykonywania zapytania dla wszystkich baz danych, ważne jest elastyczny zapytania można traktować danych tak, jakby była częścią jednej logicznej bazy danych podzielonej przez dzierżawcę. 

Aby symulować tego wzorca, zestaw widoków "global" są dodawane do bazy danych dzierżawy projektu identyfikator dzierżawcy do każdej z tabel, które będą pytani globalnie. Na przykład *VenueEvents* widoku dodaje obliczanej *VenueId* kolumny zaprojektowana z *zdarzenia* tabeli. Podobnie *VenueTicketPurchases* i *VenueTickets* widoków dodać obliczanej *VenueId* kolumny zaprojektowana z ich odpowiednich tabel. Widoki te są używane przez elastycznej zapytania do parallelize zapytania i wypychania je do odpowiednich dzierżawy zdalnego bazy danych podczas obliczania *VenueId* występuje. To znacznie zmniejsza ilość danych, który jest zwracany i powoduje znaczne zwiększenie wydajności dla wielu zapytań. Widoki globalne te zostały utworzone wcześniej w wszystkie dzierżawy bazy danych.

1. Otwórz w programie SSMS oraz [nawiązać tenants1 -&lt;użytkownika&gt; serwera](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozwiń węzeł **baz danych**, kliknij prawym przyciskiem myszy _contosoconcerthall_i wybierz **nowe zapytanie**.
1. Uruchom następujące kwerendy, aby eksplorować różnica między pojedynczej dzierżawy tabele i widoki globalne:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

W tych widokach *VenueId* jest obliczany jako skrót nazwę miejsce, ale jakiejkolwiek metody może zostać użyty do wprowadzenia unikatową wartość. Ta metoda jest podobny do sposobu klucz dzierżawy jest obliczana do użycia w katalogu.

Aby sprawdzić definicji *miejsc* widoku:

1. W **Eksplorator obiektów**, rozwiń węzeł **contosoconcerthall** > **widoków**:

   ![wyświetlenia](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kliknij prawym przyciskiem myszy **dbo. Miejsc**.
3. Wybierz **skryptu widoku w postaci** > **utworzyć** > **nowe okno edytora zapytań**

Skrypt żadnym innym *miejscową* widoków, aby zobaczyć, jak dodać *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Wdrażanie bazy danych używanej dla zapytań rozproszonych

Wdraża tego ćwiczenia _adhocreporting_ bazy danych. Jest to head bazy danych, która zawiera schematu używanego na potrzeby zapytań dla wszystkich baz danych dzierżawy. Bazy danych jest wdrażana na istniejącym serwerze katalogu, co jest serwera używanego do wszystkich związanych z zarządzaniem baz danych w przykładowej aplikacji.

1. w *PowerShell ISE*, Otwórz... \\Modułów szkoleniowych\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz*. 

1. Ustaw **$DemoScenario = 2**, _bazy danych raportowania Ad hoc Wdróż_.

1. Naciśnij klawisz **F5** uruchomić skrypt i utworzyć *adhocreporting* bazy danych.

W następnej sekcji możesz dodać schematu bazy danych, więc może służyć do uruchamiania zapytań rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Skonfiguruj bazę danych "head" do uruchamiania zapytań rozproszonych

Tego ćwiczenia dodaje schematu (zewnętrznego źródła danych i definicji tabeli zewnętrznej) do _adhocreporting_ bazy danych w celu włączenia zapytań dla wszystkich baz danych dzierżawy.

1. Otwórz program SQL Server Management Studio i łączenia z bazą danych raportowania ad hoc utworzony w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _AdhocReportingDB.sql zainicjować_ w programie SSMS.
3. Przejrzyj skrypt SQL i Uwaga:

   Elastyczne zapytanie używa poświadczeń o zakresie bazy danych można uzyskać dostępu do wszystkich baz danych dzierżawy. To poświadczenie musi być dostępna w bazach danych i zwykle przyznawane minimalnych uprawnień powinien włączyć te zapytania.

    ![Tworzenie poświadczeń](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Z bazy danych katalogu jako zewnętrzne źródło danych zapytania są dystrybuowane do wszystkich baz danych zarejestrowane w wykazie w czasie wykonywania kwerendy. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt pobiera lokalizacji bazy danych katalogu z bieżącego serwera (@@servername) gdy skrypt zostanie wykonany.

    ![Tworzenie zewnętrznego źródła danych](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Tabele zewnętrzne odwołujące się do widoki globalne opisanych w poprzedniej sekcji oraz zdefiniowana z **dystrybucji = SHARDED(VenueId)**. Ponieważ każdy *VenueId* map do pojedynczej bazy danych, poprawia to wydajność w wielu scenariuszach, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Lokalnej tabeli _VenueTypes_ które jest tworzone i wypełniane. Tabeli danych to odwołanie jest typowe w przypadku wszystkich dzierżawy baz danych, więc może być reprezentowany jako lokalnej tabeli i wypełnione do wspólnych danych. Dla niektórych kwerend o tej tabeli w bazie danych head może zmniejszyć ilość danych, które należy przenieść do głównego bazy danych.

    ![Tworzenie tabeli](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Jeśli dołączysz tabele odwołań w ten sposób, należy zaktualizować schemat tabeli i dane, po zaktualizowaniu bazy danych dzierżawy.

4. Naciśnij klawisz **F5** do uruchomienia skryptu i zainicjuj *adhocreporting* bazy danych. 

Teraz można uruchomić zapytania rozproszone i zbieranie szczegółowych informacji we wszystkich dzierżawców!

## <a name="run-distributed-queries"></a>Uruchamianie zapytań rozproszonych

Teraz, gdy *adhocreporting* baza danych jest skonfigurowany, przejdź dalej i uruchamiania niektórych zapytań rozproszonych. Obejmują plan wykonania w celu lepszego zrozumienia, z której jest przeprowadzana przetwarzania zapytania. 

Podczas sprawdzania plan wykonania, umieść kursor nad ikony planu, aby uzyskać szczegółowe informacje. 

To ustawienie jest ważne, należy pamiętać, **dystrybucji = SHARDED(VenueId)** zwiększa wydajność w różnych scenariuszach, kiedy zdefiniowano z zewnętrznym źródłem danych. Ponieważ do każdego *VenueId* map do pojedynczej bazy danych, jest łatwe wykonać filtrowania zdalnie, zwracanie tylko dane potrzebne.

1. Otwórz... \\Modułów szkoleniowych\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReportingQueries.sql pokaz* w programie SSMS.
2. Upewnij się, czy nawiązano **adhocreporting** bazy danych.
3. Wybierz **zapytania** menu i kliknij przycisk **obejmują rzeczywisty Plan wykonania**
4. Wyróżnij *miejsc, które są obecnie zarejestrowane?* zapytania, a następnie naciśnij klawisz **F5**.

   Zapytanie zwraca listę całego miejsca, pokazujący, jak szybko i łatwo jest zapytania dla wszystkich dzierżawców i zwracanych danych z każdej dzierżawy.

   Zbadaj planu i kosztów całej jest zapytania zdalnego. Każda baza danych dzierżawy zdalnie wykonuje zapytania i zwraca informacje o jego miejsce head bazy danych.

   ![Wybierz * z dbo. Miejsc](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Wybierz przycisk Dalej, zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie sprzężenia danych z baz danych dzierżawy i lokalnej *VenueTypes* tabeli (lokalny, w jakiej jest tabelą *adhocreporting* bazy danych).

   Przejrzyj plan i sprawdź, czy większość koszt zapytania zdalnego. Każda baza danych dzierżawy powoduje zwrócenie informacji o jego miejsce i wykonuje sprzężenie lokalne z lokalnej *VenueTypes* tabela, która ma przyjazną nazwę wyświetlaną.

   ![Dołącz do danych zdalnych i lokalnych](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teraz wybierz *na dzień najbardziej biletów sprzedano?* zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie jest bardziej złożonych przyłączanie i agregacji. Większość przetwarzania występuje zdalnie.  Tylko jednego wiersze, zawierające każdego miejsce codziennie biletu sprzedaży liczba dziennie, nastąpi powrót do głównego bazy danych.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdróż bazę danych raportowania i zdefiniowanie schematu wymagane do uruchamiania zapytań rozproszonych.


Teraz spróbuj [samouczek analizy dzierżawy](saas-tenancy-tenant-analytics.md) do eksplorowania wyodrębnianie danych do bazy danych analizy oddzielne bardziej złożone analizy przetwarzania.

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczków, z którymi aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
