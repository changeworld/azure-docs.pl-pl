---
title: Uruchamianie zapytań raportowania ad hoc w wielu bazach danych Azure SQL | Dokumentacja firmy Microsoft
description: Uruchamianie zapytań ad hoc raportowania w wielu bazach danych SQL w przykładzie aplikacji wielodostępnej.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: d4c5a2ca88f982626c8c2a8b37e4a7d6dfdbe599
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485827"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Uruchamianie zapytań analitycznych ad-hoc w wielu bazach danych Azure SQL

W tym samouczku możesz uruchamiania rozproszonych zapytań dla całego zestawu dzierżawy baz danych, aby włączyć raportowanie interaktywne ad-hoc. Te zapytania mogą wyodrębnić szczegółowe informacje ukryte w codziennych danych operacyjnych, aplikacji SaaS o nazwie Wingtip Tickets. W celu wyodrębnienia te należy wdrożyć dodatkowe analitycznej bazy danych na serwerze wykazu i umożliwiają elastyczne zapytanie zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć ad-hoc bazy danych raportowania
> * Sposób uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja o nazwie Wingtip Tickets SaaS wielodostępnej w bazie danych jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano program SQL Server Management Studio (SSMS). Aby pobrać i zainstalować narzędzie SSMS, zobacz [Pobierz SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Wzorzec raportowania ad hoc

![wzorzec raportowania ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikacje SaaS można analizować ogromnej ilości danych dzierżawy, które są przechowywane centralnie w chmurze. Analizy ujawnić szczegółowych informacji o operacjach i użyciu aplikacji. Te informacje mogą ukierunkować rozwój funkcji, usprawnienia w zakresie użyteczności oraz inne inwestycje w usługach i aplikacjach.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jedno z podejść jest użycie [zapytania elastycznego](sql-database-elastic-query-overview.md), które umożliwia wykonywanie zapytań do rozproszonego zestawu baz danych za pomocą wspólnego schematu. Te bazy danych mogą być rozproszone między różnych grup zasobów i subskrypcji. Jeszcze jednej wspólnej logowania musi mieć dostęp do wyodrębniania danych ze wszystkich baz danych. Elastyczne zapytanie używa pojedynczej *head* bazy danych, w którym są określone tabele zewnętrzne odzwierciedlające tabele lub widoki rozproszone (dzierżawcy) baz danych. Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa podzielonej na fragmenty mapy w bazie danych wykazów do określenia lokalizacji wszystkich baz danych dzierżaw. Konfiguracja i zapytanie są proste przy użyciu standardu [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługiwać zapytań ad hoc z narzędziami, takimi jak usługa Power BI i program Excel.

Dzięki rozłożeniu zapytań w bazach danych dzierżawy, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane w środowisku produkcyjnym. Jednak jak elastyczne zapytanie pobiera dane z potencjalnie wiele baz danych, opóźnienie zapytań może być czasem wyższy niż w przypadku zapytań równoważne przesłane do jednej wielodostępnej bazie danych. Pamiętaj, aby projekt zapytań zminimalizowanie ilości danych, która jest zwracana. Zapytanie elastyczne w usłudze często najlepiej nadaje się do wykonywania zapytań na małe ilości danych w czasie rzeczywistym, w przeciwieństwie do tworzenia często używane lub analytics złożonych zapytań lub raportów. Jeśli zapytania nie zostaną wykonane poprawnie, Przyjrzyj się [plan wykonania](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) aby zobaczyć, jakie części zapytania została przekazana do zdalnej bazy danych. I ocenić, jak dużo danych jest zwracany. Zapytania wymagające złożone przetwarzanie analityczne mogą być lepiej obsługiwani przez zapisywanie danych wyodrębnionych dzierżawcy w bazie danych, który jest zoptymalizowany pod kątem zapytań analitycznych. SQL Database i SQL Data Warehouse może udostępniać takie analitycznej bazy danych.

Ten wzorzec analizy została wyjaśniona w [samouczek analiz dzierżaw](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i skryptów

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Utwórz dane dotyczące sprzedaży biletów

Aby uruchamiać zapytania dla zestawu danych bardziej interesujące, należy utworzyć dane dotyczące sprzedaży biletów, uruchamiając generator biletu.

1. W *PowerShell ISE*, Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* skrypt i ustaw następujące wartości:
   * **$DemoScenario** = 1, **zakup biletów dla zdarzeń na wszystkie lokalizacje**.
2. Naciśnij klawisz **F5** do uruchamiania skryptu i generowaniu możliwości sprzedaży biletów. Po uruchomieniu skryptu można kontynuować czynności w ramach tego samouczka. Dane biletu zostanie wysłane *wykonywania ad hoc rozproszonych zapytań* sekcji, więc poczekaj, aż generator biletu zakończyć.

## <a name="explore-the-tenant-tables"></a>Zapoznaj się z tabel dzierżawy 

W aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych dzierżawy są przechowywane w modelu zarządzania dzierżawy hybrydowej — gdzie dane dzierżawy zostanie przechowywanych w wielodostępnej bazie danych lub bazę danych pojedynczej dzierżawy i można przenosić między nimi. Podczas wykonywania zapytania dla wszystkich baz danych dzierżawy, ważne jest zapytanie elastyczne można traktować dane tak, jakby jest częścią jednej logicznej bazy danych podzielonej na fragmenty przez dzierżawcę. 

Aby osiągnąć ten wzorzec, obejmują wszystkie tabele dzierżawy *VenueId* kolumnę, która identyfikuje który dzierżawy danych należy do. *VenueId* jest obliczany jako skrót nazwy właściwości, ale wszystkie podejście może służyć do wprowadzenia unikatową wartość dla tej kolumny. To podejście jest podobne do sposób klucza dzierżawy jest kolumną obliczaną do użytku w wykazie. Tabele zawierające *VenueId* są używane przez zapytania elastycznego do zrównoleglenia zapytania i odesłać je do odpowiedniego dzierżawy zdalnej bazy danych. To znacznie zmniejsza ilość danych, które są zwracane i powoduje zwiększenie wydajności, szczególnie w przypadku, gdy istnieje wiele dzierżaw, którego dane są przechowywane w bazach danych pojedynczej dzierżawy.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Wdrażanie bazy danych używane dla zapytań rozproszonych ad hoc

To ćwiczenie wdraża *adhocreporting* bazy danych. Jest to główny bazy danych, który zawiera schemat używany podczas wykonywania zapytań we wszystkich bazach danych dzierżaw. Baza danych jest wdrażana do istniejącego serwera wykazu, czyli serwera używanego do wszystkich związanych z zarządzaniem baz danych w przykładowej aplikacji.

1. Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReporting.ps1 pokaz* w *PowerShell ISE* i ustaw następujące wartości:
   * **$DemoScenario** = 2, **wdrażanie Ad hoc analitycznej bazy danych**.

2. Naciśnij klawisz **F5** Aby uruchomić skrypt i utworzyć *adhocreporting* bazy danych.

W następnej sekcji dodasz schematu do bazy danych, dzięki czemu może służyć do uruchamiania rozproszonych zapytań.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "head" do uruchamiania rozproszonych zapytań

To ćwiczenie dodaje schematu (zewnętrznego źródła danych i definicje tabeli zewnętrznej) do ad-hoc raportowania bazy danych, która umożliwia wykonywanie zapytań we wszystkich bazach danych dzierżaw.

1. Otwórz program SQL Server Management Studio i połączenia z bazą danych ad hoc raportowania utworzonej w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *AdhocReportingDB.sql zainicjować* w programie SSMS.
3. Przejrzyj skrypt SQL i pamiętaj o następujących kwestiach:

   Elastyczne zapytanie używa poświadczeń o zakresie bazy danych, aby dostęp do wszystkich wymienionych baz danych dzierżawy. Te poświadczenia muszą być dostępne we wszystkich bazach danych i zwykle przyznanych minimalnych uprawnień powinien włączyć te zapytania ad hoc.

    ![tworzenie poświadczenia](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Za pomocą bazy danych wykazów jako zewnętrzne źródło danych, zapytania są dystrybuowane do wszystkich baz danych zarejestrowanych w wykazie, po uruchomieniu zapytania. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt inicjowania pobiera lokalizację bazy danych wykazów, pobierając bieżącego serwera (@@servername) gdy skrypt zostanie wykonany.

    ![Tworzenie zewnętrznego źródła danych](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Tabele zewnętrzne, odwołujące się do tabel dzierżawy są zdefiniowane przy użyciu **dystrybucji = SHARDED(VenueId)** . Kierowane zapytania dla określonego *VenueId* z odpowiednią bazą danych i zwiększa wydajność w wielu scenariuszach, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Lokalnej tabeli *VenueTypes* , zostanie utworzony i wypełniony. Ta tabela danych odwołania jest typowe we wszystkich bazach danych dzierżaw, więc może być reprezentowany jako lokalnej tabeli i wypełnione za pomocą wspólnych danych. Dla niektórych kwerend, to może zmniejszyć ilość danych przenoszone między baz danych dzierżaw i *adhocreporting* bazy danych.

    ![Tworzenie tabeli](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Tabele odwołań zostaną umieszczone w ten sposób, należy zaktualizować schemat tabeli i dane, po zaktualizowaniu bazy danych dzierżaw.

4. Naciśnij klawisz **F5** do uruchamiania skryptu i zainicjować *adhocreporting* bazy danych. 

Teraz możesz uruchamiania rozproszonych zapytań i zbierania szczegółowych informacji ze wszystkich dzierżaw!

## <a name="run-ad-hoc-distributed-queries"></a>Uruchamianie zapytań ad hoc rozproszonych

Teraz, gdy *adhocreporting* bazy danych jest skonfigurowany, przejdź dalej i uruchomisz kilka zapytań rozproszonych. Obejmują planu wykonania w celu lepszego zrozumienia gdy przetwarzania kwerend się dzieje. 

Podczas sprawdzania plan wykonywania, umieść kursor nad ikony planu, aby uzyskać szczegółowe informacje. 

1. W *SSMS*, Otwórz... \\Learning Modules\\Operational Analytics\\raportowania ad hoc\\*AdhocReportingQueries.sql pokaz*.
2. Upewnij się, nawiązano **adhocreporting** bazy danych.
3. Wybierz **zapytania** menu i kliknij przycisk **zawierają rzeczywiste planu wykonania**
4. Wyróżnij *miejsc, które są aktualnie zarejestrowane?* zapytania, a następnie naciśnij klawisz **F5**.

   Zapytanie zwraca listę całego miejsca, pokazujący, jak szybko i łatwo jest zapytania dla wszystkich dzierżaw i zwrócić dane z każdej dzierżawy.

   Zbadaj plan i sprawdzić, czy cały koszt zapytania zdalnego ponieważ możemy po prostu przechodząc do każdej bazy danych dzierżawy i wybierając informacje miejsc.

   ![Wybierz * z dbo. Lokalizacje](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Wybierz następne zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie sprzęga dane z bazy danych dzierżaw i lokalnej *VenueTypes* tabeli (lokalny, ponieważ znajduje się tabela *adhocreporting* bazy danych).

   Sprawdzanie planu i sprawdzić, czy większość koszt zapytania zdalnego ponieważ możemy wysyłać kwerendy informacji o właściwości każdego dzierżawcy (dbo. Miejsca), a następnie wykonaj szybkie sprzężenie lokalne przy użyciu lokalnej *VenueTypes* tabeli, aby wyświetlić przyjazną nazwę.

   ![Dołącz do danych zdalnych i lokalnych](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teraz wybierz *na dzień najbardziej bilety sprzedano?* zapytania, a następnie naciśnij klawisz **F5**.

   To zapytanie jest nieco bardziej złożonych dołączania i agregacji. Należy pamiętać, jest to że większość przetwarzania jest wykonywane zdalnie, a jeszcze raz, możemy przywrócić tylko wiersze, których potrzebujemy, zwracając tylko pojedynczy wiersz każde miejsce biletu łącznej sprzedaży liczby na dzień.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdrażanie ad-hoc bazy danych raportowania i dodać schematu do niego do uruchamiania rozproszonych zapytań.

Wypróbuj teraz [samouczek analitycznych](saas-multitenantdb-tenant-analytics.md) do eksplorowania wyodrębniania danych do oddzielnej analitycznej bazy danych, aby uzyskać bardziej złożone przetwarzanie analizy.

## <a name="additional-resources"></a>Dodatkowe zasoby

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
