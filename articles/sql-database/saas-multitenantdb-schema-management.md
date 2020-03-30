---
title: Zarządzanie schematem w aplikacji z wieloma dzierżawcami
description: Zarządzanie schematami wielu dzierżaw w aplikacji z wieloma dzierżawami, która korzysta z usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269200"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Zarządzanie schematem w aplikacji SaaS, która używa podzielonych wielodostępnych baz danych SQL

W tym samouczku ociekaniu o tym, jakie wyzwania należy się z zachowaniem floty baz danych w aplikacji Software as a Service (SaaS). Rozwiązania są zademonstrowane do fanning się zmiany schematu we flocie baz danych.

Jak każda aplikacja, aplikacja Wingtip Tickets SaaS będzie ewoluować wraz z czasem i będzie wymagać zmian w bazie danych. Zmiany mogą mieć wpływ na schemat lub dane referencyjne lub zastosować zadania konserwacji bazy danych. W przypadku aplikacji SaaS przy użyciu bazy danych na wzorzec dzierżawy zmiany muszą być skoordynowane w potencjalnie ogromnej floty baz danych dzierżawy. Ponadto należy uwzględnić te zmiany w procesie inicjowania obsługi administracyjnej bazy danych, aby upewnić się, że są one uwzględnione w nowych bazach danych podczas ich tworzenia.

#### <a name="two-scenarios"></a>Dwa scenariusze

W tym samouczku przedstawiono następujące dwa scenariusze:
- Wdrażanie aktualizacji danych referencyjnych dla wszystkich dzierżawców.
- Odbuduj indeks w tabeli zawierającej dane referencyjne.

Funkcja [zadania elastyczne](elastic-jobs-overview.md) usługi Azure SQL Database służy do wykonywania tych operacji w bazach danych dzierżawy. Zadania działają również w bazie danych dzierżawy "szablonu". W przykładowej aplikacji Wingtip Tickets ta baza danych szablonu jest kopiowana w celu aprowizowania nowej bazy danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz agenta zadania.
> * Wykonywanie kwerendy T-SQL w wielu bazach danych dzierżawy.
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżawy.
> * Tworzenie indeksu w tabeli we wszystkich bazach danych dzierżawy.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja bazy danych wielodostępnych biletów Wingtip musi być już wdrożona:
    - Aby uzyskać instrukcje, zobacz pierwszy samouczek, który wprowadza Wingtip Bilety SaaS wielodostępnych aplikacji bazy danych:<br />[Wdrażanie i eksplorowanie podzielonej aplikacji wielodostępowej, która korzysta z bazy danych SQL Azure.](saas-multitenantdb-get-started-deploy.md)
        - Proces wdrażania jest uruchamiany przez mniej niż pięć minut.
    - Musisz mieć zainstalowaną wersję *wielodostępną* podzieloną na fragmenty. Wersje *autonomiczne* i *bazy danych na dzierżawę* nie obsługują tego samouczka.

- Należy zainstalować najnowszą wersję programu SQL Server Management Studio (SSMS). [Pobierz i zainstaluj SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Program Azure PowerShell musi zostać zainstalowany. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> W tym samouczku używane są funkcje usługi Azure SQL Database, które znajdują się w ograniczonej wersji zapoznawczej[(zadania elastycznej bazy danych).](sql-database-elastic-database-client-library.md) Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji do *\@SaaSFeedback microsoft.com* z subject=Elastic Jobs Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczona, więc skontaktuj się z *\@SaaSFeedback microsoft.com* w przypadku powiązanych pytań lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematami SaaS

Podzielony na fragmenty model bazy danych z wieloma dzierżawami używany w tym przykładzie umożliwia bazy danych dzierżawców do jednego lub więcej dzierżawców. W tym przykładzie eksploruje potencjał użycia kombinacji wielu dzierżawców i jednej dzierżawy baz danych, umożliwiając model zarządzania dzierżawy *hybrydowej.* Zarządzanie zmianami w tych bazach danych może być skomplikowane. [Zadania elastyczne](elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie dużą liczbą baz danych. Zadania umożliwiają bezpieczne i niezawodne uruchamianie skryptów Transact-SQL jako zadań w grupie baz danych dzierżawy. Zadania są niezależne od interakcji użytkownika lub danych wejściowych. Ta metoda może służyć do wdrażania zmian w schemacie lub wspólnych danych referencyjnych, we wszystkich dzierżawców w aplikacji. Zadania elastyczne mogą być również używane do obsługi złotej kopii szablonu bazy danych. Szablon jest używany do tworzenia nowych dzierżaw, zawsze zapewniając, że używany jest najnowszy schemat i dane referencyjne.

![ekran](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Istnieje nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją usługi Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ograniczona wersja zapoznawcza obsługuje obecnie tworzenie agenta zadań przy użyciu programu PowerShell, a także funkcji T-SQL do tworzenia zadań i zarządzania nimi.
> [!NOTE]
> Ten samouczek zakłada użycie funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji SaaSFeedback@microsoft.com z subject=Elastic Jobs Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej. Ta wersja zapoznawcza SaaSFeedback@microsoft.com jest ograniczona, więc skontaktuj się z powiązanymi pytaniami lub pomocą techniczną.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji I skrypty aplikacji Wingtip Tickets SaaS Multi-tenant Database

Skrypty bazy danych wielodostępnych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) w usłudze GitHub. Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Tworzenie bazy danych agenta zadań i nowego agenta zadania

Ten samouczek wymaga użycia programu PowerShell do utworzenia bazy danych agenta zadań i agenta zadań. Podobnie jak baza danych MSDB używana przez agenta SQL, agent zadań używa bazy danych SQL platformy Azure do przechowywania definicji zadań, stanu zadania i historii. Po utworzeniu agenta zadań można natychmiast tworzyć i monitorować zadania.

1. W **programie PowerShell ISE**otwórz *... Moduły\\szkoleniowe\\Zarządzanie schematem Demo-SchemaManagement.ps1 . \\*
2. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt *Demo-SchemaManagement.ps1* wywołuje skrypt *Deploy-SchemaManagement.ps1* w celu utworzenia bazy danych o nazwie _jobagent_ na serwerze katalogu. Skrypt następnie tworzy agenta zadania, przekazując bazę danych _jobagent_ jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

#### <a name="prepare"></a>Przygotowanie

Baza danych każdego dzierżawcy zawiera zestaw typów miejsc w **VenueTypes** tabeli. Każdy typ miejsca określa rodzaj wydarzeń, które mogą być hostowane w miejscu. Te typy miejsc odpowiadają obrazom tła widocznym w aplikacji zdarzeń dzierżawy.  W tym ćwiczeniu można wdrożyć aktualizację do wszystkich baz danych, aby dodać dwa dodatkowe typy miejsc: *Wyścigi motocyklowe* i *Klub pływacki.*

Najpierw przejrzyj typy miejsc zawarte w każdej bazie danych dzierżawy. Połącz się z jedną z baz danych dzierżawy w programie SQL Server Management Studio (SSMS) i sprawdź tabelę VenueTypes.  Można również kwerendy tej tabeli w edytorze zapytań w witrynie Azure portal, dostępne ze strony bazy danych.

1. Otwórz usługę SSMS i połącz się z serwerem dzierżawy: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Aby potwierdzić, że *Wyścigi motocyklowe* i *Klub Pływacki* **nie są** obecnie uwzględnione, przejdź do bazy danych *contosoconcerthall* na serwerze *użytkownika&lt;&gt; tenants1-dpt-* i prześp tabelę *VenueTypes.*



#### <a name="steps"></a>Kroki

Teraz należy utworzyć zadanie, aby zaktualizować **VenueTypes** tabeli w każdej bazie danych dzierżawy, dodając dwa nowe typy miejsc.

Aby utworzyć nowe zadanie, należy użyć zestawu procedur przechowywanych systemu zadań, które zostały utworzone w bazie danych _jobagent._ Procedury składowane zostały utworzone podczas tworzenia agenta zadania.

1. W systemie SSMS połącz się z serwerem&lt;&gt;dzierżawy: tenants1-mt- user .database.windows.net

2. Przejdź do bazy danych *tenants1.*

3. Kwerenda *VenueTypes* tabeli, aby potwierdzić, że *Wyścigi motocyklowe* i *Klub pływacki* nie są jeszcze na liście wyników.

4. Połącz się z serwerem katalogu, którym jest *catalog-mt-&lt;&gt;user .database.windows.net*.

5. Połącz się z bazą danych _jobagent_ na serwerze wykazu.

6. W SSMS otwórz plik *... Moduły\\szkoleniowe\\Zarządzanie schematem DeployReferenceData.sql . \\*

7. Zmodyfikuj &lt;&gt; instrukcję: set @User = user i zastąp wartość Użytkownika używaną podczas wdrażania aplikacji Bazy danych wielodostępnych Wingtip Tickets SaaS.

8. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwować

Obserwuj następujące elementy w skrypcie *DeployReferenceData.sql:*

- **sp\_\_dodaj\_grupę docelową** tworzy nazwę grupy docelowej *DemoServerGroup*i dodaje członków docelowych do grupy.

- **sp\_\_dodaj\_\_członka grupy docelowej** dodaje następujące elementy:
    - Typ elementu członkowskiego docelowego *serwera.*
        - Jest to *serwer użytkownika&lt;&gt; tenants1-mt,* który zawiera bazy danych dzierżawców.
        - W tym serwer zawiera bazy danych dzierżawy, które istnieją w czasie wykonywania zadania.
    - Typ elementu członkowskiego docelowego *bazy danych* dla bazy danych szablonu *(basetenantdb),* który znajduje się na serwerze *użytkownika&lt;&gt; catalog-mt,,*
    - Typ elementu członkowskiego docelowego *bazy danych,* aby uwzględnić *adhocreporting* bazy danych, który jest używany w późniejszym samouczku.

- **sp\_\_add zadanie** tworzy zadanie o nazwie *Reference Data Deployment*.

- **sp\_\_add jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL, aby zaktualizować tabelę odwołań, VenueTypes.

- Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Użyj tych kwerend, aby przejrzeć wartość stanu w kolumnie **cyklu życia,** aby określić, kiedy zadanie zostało zakończone. Zadanie aktualizuje bazę danych dzierżawców i aktualizuje dwie dodatkowe bazy danych, które zawierają tabelę odwołań.

W systemie SSMS przejdź do bazy danych dzierżawy na serwerze *dzierżawy1-mt-user.&lt;&gt; * Zapytaj w tabeli *VenueTypes,* aby potwierdzić, że *wyścigi motocyklowe* i *klub pływacki* są teraz dodawane do tabeli. Całkowita liczba typów miejsc powinna zostać zwiększona o dwa.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

To ćwiczenie tworzy zadanie odbudowania indeksu w tabeli odwołań klucz podstawowy na wszystkich bazach danych dzierżawy. Przebudowa indeksu jest typową operacją zarządzania bazą danych, którą administrator może uruchomić po załadowaniu dużej ilości danych, aby zwiększyć wydajność.

1. W systemie SSMS połącz się z bazą danych _jobagent_ w *&lt;katalogu-mt-&gt;User .database.windows.net* server.

2. W SSMS otwórz *... Moduły\\szkoleniowe\\Zarządzanie schematem OnlineReindex.sql . \\*

3. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwować

Obserwuj następujące elementy w skrypcie *OnlineReindex.sql:*

* **sp\_\_dodać zadanie** tworzy nowe zadanie o nazwie *Online Reindex\_\_PK VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_\_add jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL, aby zaktualizować indeks.

* Pozostałe widoki w wykonaniu zadania monitora skryptu. Użyj tych kwerend, aby przejrzeć wartość stanu w kolumnie **cyklu życia,** aby określić, kiedy zadanie zostało pomyślnie zakończone na wszystkich członków grupy docelowej.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](elastic-jobs-overview.md)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie agenta zadań w celu uruchamiania zadań T-SQL w wielu bazach danych
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżawy
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [samouczek raportowania ad hoc,](saas-multitenantdb-adhoc-reporting.md) aby zbadać uruchamianie zapytań rozproszonych w bazach danych dzierżawy.

