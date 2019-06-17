---
title: Uruchom raportowania zapytań w wielu bazach danych Azure SQL | Dokumentacja firmy Microsoft
description: Raportowanie przy użyciu międzydzierżawowe rozproszonych zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9562d0cd1ad97a459c3630456a6070ac2b6e63f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61484742"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Raportowanie przy użyciu międzydzierżawowe rozproszonych zapytań

W tym samouczku możesz uruchamiania rozproszonych zapytań dla całego zestawu dzierżawy baz danych na potrzeby raportowania. Te zapytania mogą wyodrębnić szczegółowe informacje ukryte w codziennych danych operacyjnych dzierżaw SaaS o nazwie Wingtip Tickets. Aby to zrobić, możesz wdrożyć dodatkowe bazy danych raportowania na serwerze wykazu i umożliwiają elastyczne zapytanie zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć bazę danych raportowania
> * Sposób uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżaw
> * Jak włączyć widoki globalne w każdej bazie danych efektywnego wykonywania zapytań dla dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:


* Aplikacja o nazwie Wingtip Tickets SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano program SQL Server Management Studio (SSMS). Aby pobrać i zainstalować narzędzie SSMS, zobacz [Pobierz SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Wzorzec raportowania międzydzierżawowe

![wzorzec zapytania rozproszonego międzydzierżawowe](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Możliwość jednej aplikacji SaaS jest używać dużych ilości danych dzierżawy, przechowywane w chmurze w celu uzyskania szczegółowych informacji o operacjach i użyciu aplikacji. Te informacje mogą ukierunkować rozwój funkcji, usprawnienia w zakresie użyteczności oraz inne inwestycje w usługach i aplikacjach.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jedno z podejść jest użycie [zapytania elastycznego](sql-database-elastic-query-overview.md), które umożliwia wykonywanie zapytań do rozproszonego zestawu baz danych za pomocą wspólnego schematu. Te bazy danych mogą być rozproszone między różnych grup zasobów i subskrypcji, ale konieczne udostępnienie wspólnego logowania. Elastyczne zapytanie używa pojedynczej *head* bazy danych, w którym są określone tabele zewnętrzne odzwierciedlające tabele lub widoki rozproszone (dzierżawcy) baz danych. Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa podzielonej na fragmenty mapy w bazie danych wykazów do określenia lokalizacji wszystkich baz danych dzierżaw. Konfiguracja i zapytanie głównej bazy danych są proste przy użyciu standardu [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługuje zapytań z narzędziami, takimi jak usługa Power BI i program Excel.

Dzięki rozłożeniu zapytań w bazach danych dzierżawy, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane w środowisku produkcyjnym. Elastyczne zapytanie pobiera dane z potencjalnie wiele baz danych, opóźnienie zapytań jest wyższa niż równoważna zapytania, skierowane do jednej wielodostępnej bazie danych. Projektowanie zapytań zminimalizowanie ilości danych, które są zwracane do głównej bazy danych. Zapytanie elastyczne w usłudze często najlepiej nadaje się do wykonywania zapytań na małe ilości danych w czasie rzeczywistym, w przeciwieństwie do tworzenia często używane lub analytics złożonych zapytań lub raportów. Jeśli zapytania nie działać dobrze, Przyjrzyj się [plan wykonania](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) aby zobaczyć, jakie część zapytania jest przekazywany do zdalnej bazy danych oraz jak dużo danych jest zwracany. Zapytania, które wymagają złożonych agregacji lub przetwarzania analitycznego mogą być lepiej obsługuje przez wyodrębnienie danych dzierżawy na bazę danych ani na magazyn danych zoptymalizowana pod kątem zapytań analitycznych. Ten wzorzec jest szczegółowo [samouczek analiz dzierżaw](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobieranie skryptów aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Utwórz dane dotyczące sprzedaży biletów

Aby uruchamiać zapytania dla zestawu danych bardziej interesujące, należy utworzyć dane dotyczące sprzedaży biletów, uruchamiając generator biletu.

1. W *PowerShell ISE*, Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* skrypt i Ustaw następującą wartość:
   * **$DemoScenario** = 1, **zakup biletów dla zdarzeń na wszystkie lokalizacje**.
2. Naciśnij klawisz **F5** do uruchamiania skryptu i generowaniu możliwości sprzedaży biletów. Po uruchomieniu skryptu można kontynuować czynności w ramach tego samouczka. Dane biletu zostanie wysłane *wykonywania ad hoc rozproszonych zapytań* sekcji, więc poczekaj, aż generator biletu zakończyć.

## <a name="explore-the-global-views"></a>Zapoznaj się z widoki globalne

W aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy Każda dzierżawa znajduje się bazy danych. W efekcie danych zawartych w tabelach bazy danych obejmuje perspektywy pojedynczej dzierżawy. Jednak podczas wykonywania zapytania dla wszystkich baz danych, ważne jest zapytanie elastyczne można traktować dane tak, jakby jest częścią jednej logicznej bazy danych podzielonej na fragmenty przez dzierżawcę. 

Aby zasymulować ten wzorzec, zestaw widoków, "global" są dodawane do bazy danych dzierżaw tego projektu identyfikator dzierżawy w każdej z tabel, które będą proszeni globalnie. Na przykład *VenueEvents* widoku dodaje obliczanej *VenueId* do kolumn, przewidywany z *zdarzenia* tabeli. Podobnie *VenueTicketPurchases* i *VenueTickets* widoków dodać obliczanej *VenueId* kolumny przewidywany z ich odpowiednimi tabel. Widoki te są używane przez elastyczne zapytanie równoległe przetwarzanie zapytań i wypychania ich do odpowiednich zdalnej dzierżawy bazy danych, kiedy *VenueId* kolumny jest obecny. To znacznie zmniejsza ilość danych, które są zwracane i powoduje znaczne zwiększenie wydajności dla wielu kwerendach. Te widoki globalne zostały wstępnie utworzone we wszystkich bazach danych dzierżaw.

1. Otwórz aplikację SSMS i [nawiązać tenants1 -&lt;użytkownika&gt; serwera](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozwiń **baz danych**, kliknij prawym przyciskiem myszy _contosoconcerthall_i wybierz **nowe zapytanie**.
1. Uruchom następujące zapytania, aby poznać różnice między tabelami pojedynczej dzierżawy i widoki globalne:

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

W tych widokach *VenueId* jest obliczany jako skrót nazwy właściwości, ale wszystkie podejście może służyć do wprowadzenia unikatową wartość. To podejście jest podobne do sposób klucza dzierżawy jest kolumną obliczaną do użytku w wykazie.

Aby zbadać definicję *Venues* widoku:

1. W **Eksplorator obiektów**, rozwiń węzeł **contosoconcerthall** > **widoków**:

   ![Widoki](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kliknij prawym przyciskiem myszy **dbo. Lokalizacje**.
3. Wybierz **skryptu widoku w postaci** > **utworzyć** > **nowe okno edytora zapytań**

Skrypt w żadnym innym *miejsc* widoków, aby zobaczyć, jak dodać *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Wdrażanie bazy danych używane dla zapytań rozproszonych

To ćwiczenie wdraża _adhocreporting_ bazy danych. Jest to główny bazy danych, który zawiera schemat używany podczas wykonywania zapytań we wszystkich bazach danych dzierżaw. Baza danych jest wdrażana do istniejącego serwera wykazu, czyli serwera używanego do wszystkich związanych z zarządzaniem baz danych w przykładowej aplikacji.

1. w *PowerShell ISE*, Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz*. 

1. Ustaw **$DemoScenario = 2**, _wdrażanie Ad hoc, baza danych raportowania_.

1. Naciśnij klawisz **F5** Aby uruchomić skrypt i utworzyć *adhocreporting* bazy danych.

W następnej sekcji dodasz schematu do bazy danych, dzięki czemu może służyć do uruchamiania rozproszonych zapytań.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "head" do uruchamiania rozproszonych zapytań

To ćwiczenie dodaje schematu (zewnętrznego źródła danych i definicje tabeli zewnętrznej) do _adhocreporting_ bazy danych w celu włączenia zapytań we wszystkich bazach danych dzierżaw.

1. Otwórz program SQL Server Management Studio i nawiązania połączenia z bazą raportowania ad hoc, utworzonej w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _AdhocReportingDB.sql zainicjować_ w programie SSMS.
3. Przejrzyj skrypt SQL i zwróć uwagę:

   Elastyczne zapytanie używa poświadczeń o zakresie bazy danych, aby dostęp do wszystkich wymienionych baz danych dzierżawy. Te poświadczenia muszą być dostępne we wszystkich bazach danych i zwykle przyznanych minimalnych uprawnień powinien włączyć te zapytania.

    ![tworzenie poświadczenia](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Dzięki bazie danych wykazu jako zewnętrzne źródło danych zapytania są dystrybuowane do wszystkich baz danych zarejestrowanych w wykazie w czasie, który uruchamia zapytanie. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt pobiera lokalizację bazy danych katalogu z bieżącego serwera (@@servername) gdy skrypt zostanie wykonany.

    ![Tworzenie zewnętrznego źródła danych](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Tabele zewnętrzne, które odwołują się widoki globalne opisanego w poprzedniej sekcji, a zdefiniowane za pomocą **dystrybucji = SHARDED(VenueId)** . Ponieważ każdy *VenueId* mapuje do poszczególnych baz danych, zwiększa to wydajność w wielu scenariuszach, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Lokalnej tabeli _VenueTypes_ , zostanie utworzony i wypełniony. Ta tabela danych odwołania jest typowe we wszystkich bazach danych dzierżaw, więc może być reprezentowany jako lokalnej tabeli i wypełnione za pomocą wspólnych danych. Dla niektórych kwerend posiadanie w tej tabeli zdefiniowane w głównej bazy danych może zmniejszyć ilość danych, które mają zostać przeniesione do głównej bazy danych.

    ![Tworzenie tabeli](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Tabele odwołań zostaną umieszczone w ten sposób, należy zaktualizować schemat tabeli i dane, po zaktualizowaniu bazy danych dzierżaw.

4. Naciśnij klawisz **F5** do uruchamiania skryptu i zainicjować *adhocreporting* bazy danych. 

Teraz możesz uruchamiania rozproszonych zapytań i zbierania szczegółowych informacji ze wszystkich dzierżaw!

## <a name="run-distributed-queries"></a>Uruchamiania rozproszonych zapytań

Teraz, gdy *adhocreporting* bazy danych jest skonfigurowany, przejdź dalej i uruchomisz kilka zapytań rozproszonych. Obejmują planu wykonania w celu lepszego zrozumienia gdy przetwarzania kwerend się dzieje. 

Podczas sprawdzania plan wykonywania, umieść kursor nad ikony planu, aby uzyskać szczegółowe informacje. 

Ważne, aby pamiętać, jest to ustawienie **dystrybucji = SHARDED(VenueId)** Jeśli jest zdefiniowana z zewnętrznym źródłem danych zwiększa wydajność w wielu scenariuszach. Ponieważ do każdego *VenueId* mapuje do poszczególnych baz danych, filtrowania można łatwo przeprowadzić zdalnie, zwracając tylko potrzebne dane.

1. Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReportingQueries.sql pokaz* w programie SSMS.
2. Upewnij się, nawiązano **adhocreporting** bazy danych.
3. Wybierz **zapytania** menu i kliknij przycisk **zawierają rzeczywiste planu wykonania**
4. Wyróżnij *miejsc, które są aktualnie zarejestrowane?* zapytania, a następnie naciśnij klawisz **F5**.

   Zapytanie zwraca listę całego miejsca, pokazujący, jak szybko i łatwo jest zapytania dla wszystkich dzierżaw i zwrócić dane z każdej dzierżawy.

   Zbadaj plan i czy zapytania zdalnego znajduje się cały koszt. Każda baza danych dzierżawy zdalnie wykonuje zapytanie i zwraca informacje o jego miejsc do głównej bazy danych.

   ![Wybierz * z dbo. Lokalizacje](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Wybierz następne zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie sprzęga dane z bazy danych dzierżaw i lokalnej *VenueTypes* tabeli (lokalny, ponieważ znajduje się tabela *adhocreporting* bazy danych).

   Zbadaj plan i sprawdzić, czy większość koszt zapytania zdalnego. Każda baza danych dzierżawy zwraca informacje o jej właściwości i wykonuje sprzężenie lokalne przy użyciu lokalnej *VenueTypes* tabeli, aby wyświetlić przyjazną nazwę.

   ![Dołącz do danych zdalnych i lokalnych](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teraz wybierz *na dzień najbardziej bilety sprzedano?* zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie jest nieco bardziej złożonych dołączania i agregacji. Większość przetwarzania występuje zdalnie.  Tylko pojedynczy wiersze, zawierające liczba każde miejsce do sprzedaży dziennie biletów na dzień, są zwracane do głównej bazy danych.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdróż bazę danych raportowania i zdefiniować schemat wymagane do uruchamiania rozproszonych zapytań.


Wypróbuj teraz [samouczek analitycznych](saas-tenancy-tenant-analytics.md) do eksplorowania wyodrębniania danych do oddzielnej analitycznej bazy danych, aby uzyskać bardziej złożone przetwarzanie analizy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* Dodatkowe [samouczki nawiązujące aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
