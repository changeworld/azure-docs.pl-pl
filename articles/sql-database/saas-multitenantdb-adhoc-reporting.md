---
title: Uruchamianie zapytań ad hoc raportowania przez wiele baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Uruchamianie zapytań ad hoc raportowania przez wiele baz danych w przykładzie wielodostępnych aplikacji.
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: AyoOlubeko
ms.openlocfilehash: 95ea5f2a40bd629384e1bc1f7d707eb30d9eaf5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644277"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Uruchamianie zapytań ad hoc analytics przez wiele baz danych Azure SQL

W tym samouczku możesz uruchomić zapytań rozproszonych przez cały zestaw dzierżawy baz danych, aby włączyć raportowanie interaktywne ad hoc. Te zapytania można wyodrębnić wgląd w codziennych danych operacyjnych aplikacji SaaS biletów Wingtip stosie. Te ekstrakcje celu wdrożenia dalszej analizy bazy danych na serwerze wykazu i umożliwiają elastyczne zapytania zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Wdrażanie ad hoc bazy danych raportowania
> * Jak uruchamiać zapytania rozproszone dla wszystkich baz danych dzierżawy


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip biletów SaaS wielodostępne w bazie danych jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS wielodostępne w bazie danych aplikacji](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować narzędzia SSMS, zobacz [pobierania programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Raportowanie ad hoc wzorca

![wzorzec raportowania ad hoc.](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikacji SaaS można analizować ogromnych ilości danych dzierżawy są przechowywane w chmurze. Analizy ujawnić wgląd w działanie i użycia aplikacji. Te szczegółowe informacje można przeprowadzają opracowanie funkcji, ulepszenia użyteczność i innych inwestycji w aplikacji i usług.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z podejść jest użycie [elastycznej zapytania](sql-database-elastic-query-overview.md), które umożliwia wykonywanie zapytań w rozproszonej zestaw baz danych z wspólny schemat. Te bazy danych mogą być rozproszone na różnych grup zasobów i subskrypcje. Jeszcze jeden wspólnej nazwy logowania muszą mieć dostęp do wyodrębniania danych z wszystkich baz danych. Elastyczne zapytanie używa pojedynczego *head* bazy danych, w którym zdefiniowano tabel zewnętrznych który duplikatów tabel lub widoków w bazach danych rozproszonych (dzierżawcy). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa mapy niezależnego fragmentu w bazie danych katalogu do określenia lokalizacji wszystkie dzierżawy bazy danych. Instalator i zapytania są oczywiste, przy użyciu standardu [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługuje zapytań ad hoc z narzędzi, takich jak usługi Power BI i Excel.

Przekazując kwerendy bazy danych dzierżawy, elastycznej zapytania zapewnia błyskawiczny wgląd w danych produkcyjnych na żywo. Jednak jak elastycznej zapytanie pobiera dane z potencjalnie wiele baz danych, opóźnienie kwerendy można czasami być wyższy niż równoważne zapytań przesłane do pojedynczej bazy danych wielu dzierżawców. Pamiętaj, aby projektowania zapytań zminimalizowanie ilości danych, która jest zwracana. Elastyczne zapytania jest często najlepiej dopasowane do badania niewielkich ilości danych w czasie rzeczywistym, w przeciwieństwie do budynku często używane lub analityka złożonych kwerend lub raportów. Jeśli zapytania nie zostaną wykonane prawidłowo, sprawdzić [plan wykonania](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) aby zobaczyć, jakie część zapytania jest przesuwana, do zdalnej bazy danych. I ocenić, jak dużo danych zostały zwrócone. Zapytania, które wymagają złożonych przetwarzania analitycznego mogą być lepiej obsługiwane przez zapisywanie danych wyodrębnionych dzierżawy do bazy danych, która jest zoptymalizowana pod kątem zapytań analytics. Baza danych SQL i usługi SQL Data Warehouse może udostępniać bazie analytics.

Ten wzorzec analytics została szczegółowo [samouczek analizy dzierżawy](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobieranie kodu źródłowego aplikacji Wingtip biletów SaaS wielodostępne w bazie danych i skryptów

Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Do wykonywania kwerend do bardziej interesującego zestawu danych, należy utworzyć danych sprzedaży biletów, uruchamiając generatora biletu.

1. W *PowerShell ISE*, Otwórz... \\Modułów uczenia\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* skryptów i ustaw następujące wartości:
   * **$DemoScenario** = 1, **zakupu biletów zdarzeń na wszystkich miejsc**.
2. Naciśnij klawisz **F5** do uruchomienia skryptu i generowania sprzedaży biletów. Po uruchomieniu skryptu nadal kroki opisane w tym samouczku. Dane biletu jest poddawany kwerendzie w *uruchomienia kwerendy rozproszone ad hoc* sekcji, więc Zaczekaj generatora biletu, aby zakończyć.

## <a name="explore-the-tenant-tables"></a>Eksploruj w tabelach dzierżawcy 

W aplikacji Wingtip biletów SaaS wielodostępne w bazie danych dzierżawców są przechowywane w modelu zarządzania dzierżawy hybrydowych - gdzie danych dzierżawy jest albo przechowywane w wielu dzierżawców lub pojedynczej dzierżawy bazy danych i można przenosić między nimi. Podczas wykonywania zapytania dla wszystkich baz danych dzierżawy, ważne jest elastyczny zapytania można traktować danych tak, jakby była częścią jednej logicznej bazy danych podzielonej przez dzierżawcę. 

Aby osiągnąć ten wzorzec, obejmują wszystkie tabele dzierżawy *VenueId* kolumny, która identyfikuje którego dzierżawy danych należy. *VenueId* jest obliczany jako skrót nazwę miejsce, ale jakiejkolwiek metody może zostać użyty do wprowadzenia unikatową wartość dla tej kolumny. Ta metoda jest podobny do sposobu klucz dzierżawy jest obliczana do użycia w katalogu. Tabele zawierające *VenueId* są używane przez zapytanie elastycznej parallelize zapytania i wypychanie ich do odpowiedniego dzierżawcy zdalnego bazy danych. To znacznie zmniejsza ilość danych, który jest zwracany i powoduje zwiększenie wydajności, szczególnie w przypadku, gdy istnieje wielu dzierżawców, którego dane są przechowywane w bazach danych pojedynczej dzierżawy.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Wdrażanie bazy danych używanej dla rozproszonych zapytań ad hoc

Wdraża tego ćwiczenia *adhocreporting* bazy danych. Jest to head bazy danych, która zawiera schematu używanego na potrzeby zapytań dla wszystkich baz danych dzierżawy. Bazy danych jest wdrażana na istniejącym serwerze katalogu, co jest serwera używanego do wszystkich związanych z zarządzaniem baz danych w przykładowej aplikacji.

1. Otwórz... \\Modułów uczenia\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* w *PowerShell ISE* i ustaw następujące wartości:
   * **$DemoScenario** = 2, **wdrażanie Ad hoc analityka w bazie danych**.

2. Naciśnij klawisz **F5** uruchomić skrypt i utworzyć *adhocreporting* bazy danych.

W następnej sekcji możesz dodać schematu bazy danych, więc może służyć do uruchamiania zapytań rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Skonfiguruj bazę danych "head" do uruchamiania zapytań rozproszonych

Tego ćwiczenia dodaje schematu (zewnętrznego źródła danych i definicji tabeli zewnętrznej) do ad hoc raportowania bazy danych, który umożliwia wykonywanie zapytania dla wszystkich baz danych dzierżawy.

1. Otwórz program SQL Server Management Studio i łączenia z bazą danych ad hoc raportowania utworzony w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *AdhocReportingDB.sql zainicjować* w programie SSMS.
3. Przejrzyj skrypt SQL i należy uwzględnić następujące informacje:

   Elastyczne zapytanie używa poświadczeń o zakresie bazy danych można uzyskać dostępu do wszystkich baz danych dzierżawy. To poświadczenie musi być dostępna w bazach danych i zwykle przyznawane minimalnych uprawnień powinien włączyć tych zapytań ad hoc.

    ![Tworzenie poświadczeń](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Przy użyciu bazy danych katalogu jako zewnętrzne źródło danych, zapytania są dystrybuowane do wszystkich baz danych zarejestrowane w wykazie, po uruchomieniu kwerendy. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt inicjacji pobiera lokalizacji bazy danych katalogu, pobierając bieżącego serwera (@@servername) gdy skrypt zostanie wykonany.

    ![Tworzenie zewnętrznego źródła danych](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Tabele zewnętrzne, które odwołują się do tabel dzierżawy są zdefiniowane z **dystrybucji = SHARDED(VenueId)**. Kieruje to zapytanie dla danego *VenueId* z odpowiednią bazą danych i zwiększa wydajność w wielu scenariuszach, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Lokalnej tabeli *VenueTypes* które jest tworzone i wypełniane. Tabeli danych to odwołanie jest typowe w przypadku wszystkich dzierżawy baz danych, więc może być reprezentowany jako lokalnej tabeli i wypełnione do wspólnych danych. Dla niektórych kwerend, to może zmniejszyć ilość danych przenoszone między bazami danych dzierżawy i *adhocreporting* bazy danych.

    ![Tworzenie tabeli](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Jeśli dołączysz tabele odwołań w ten sposób, należy zaktualizować schemat tabeli i dane, po zaktualizowaniu bazy danych dzierżawy.

4. Naciśnij klawisz **F5** do uruchomienia skryptu i zainicjuj *adhocreporting* bazy danych. 

Teraz można uruchomić zapytania rozproszone i zbieranie szczegółowych informacji we wszystkich dzierżawców!

## <a name="run-ad-hoc-distributed-queries"></a>Uruchamianie zapytań ad hoc rozproszonych

Teraz, gdy *adhocreporting* baza danych jest skonfigurowany, przejdź dalej i uruchamiania niektórych zapytań rozproszonych. Obejmują plan wykonania w celu lepszego zrozumienia, z której jest przeprowadzana przetwarzania zapytania. 

Podczas sprawdzania plan wykonania, umieść kursor nad ikony planu, aby uzyskać szczegółowe informacje. 

1. W *SSMS*, Otwórz... \\Modułów szkoleniowych\\operacyjne Analytics\\raportowania ad hoc\\*AdhocReportingQueries.sql pokaz*.
2. Upewnij się, czy nawiązano **adhocreporting** bazy danych.
3. Wybierz **zapytania** menu i kliknij przycisk **obejmują rzeczywisty Plan wykonania**
4. Wyróżnij *miejsc, które są obecnie zarejestrowane?* zapytania, a następnie naciśnij klawisz **F5**.

   Zapytanie zwraca listę całego miejsca, pokazujący, jak szybko i łatwo jest zapytania dla wszystkich dzierżawców i zwracanych danych z każdej dzierżawy.

   Zbadaj planu i kosztów całej jest zapytania zdalnego, ponieważ firma Microsoft jest po prostu do każdej dzierżawy bazy danych i wybierając informacji miejsce.

   ![Wybierz * z dbo. Miejsc](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Wybierz przycisk Dalej, zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie sprzężenia danych z baz danych dzierżawy i lokalnej *VenueTypes* tabeli (lokalny, w jakiej jest tabelą *adhocreporting* bazy danych).

   Przejrzyj plan i większość koszt jest zapytania zdalnego, ponieważ możemy wysyłać kwerendy informacji o miejscu każdego dzierżawcy (dbo. Miejsc), a następnie wykonaj szybkie sprzężenie lokalne z lokalnej *VenueTypes* tabela, która ma przyjazną nazwę wyświetlaną.

   ![Dołącz do danych zdalnych i lokalnych](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teraz wybierz *na dzień najbardziej biletów sprzedano?* zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie jest bardziej złożonych przyłączanie i agregacji. Należy pamiętać, jest to najbardziej przetwarzania jest wykonywane zdalnie, czy ponownie, możemy przywrócić tylko wiersze, które są potrzebne, zwracanie tylko jednego wiersza dla każdego miejscową agregacji biletu sprzedaży liczba na dzień.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdrażanie ad hoc bazy danych raportowania i dodać schematu do jego uruchomienie zapytań rozproszonych.

Teraz spróbuj [samouczek analizy dzierżawy](saas-multitenantdb-tenant-analytics.md) do eksplorowania wyodrębnianie danych do bazy danych analizy oddzielne bardziej złożone analizy przetwarzania.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
