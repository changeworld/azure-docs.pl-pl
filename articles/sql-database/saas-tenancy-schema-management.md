---
title: Zarządzanie schematem w aplikacji z jedną dzierżawą
description: Zarządzanie schematem dla wielu dzierżawców w aplikacji z jedną dzierżawą, która korzysta z bazy danych SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269213"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Zarządzanie schematem w aplikacji SaaS przy użyciu wzorca bazy danych na dzierżawę za pomocą usługi Azure SQL Database
 
W miarę rozwoju aplikacji bazy danych zmiany należy nieuchronnie wywłyć do schematu bazy danych lub danych referencyjnych.  Okresowo potrzebne są również zadania konserwacji bazy danych. Zarządzanie aplikacją, która używa bazy danych na wzorzec dzierżawy wymaga zastosowania tych zmian lub zadań konserwacji we flocie baz danych dzierżawy.

W tym samouczku przedstawiono dwa scenariusze — wdrażanie aktualizacji danych referencyjnych dla wszystkich dzierżawców i odbudowanie indeksu w tabeli zawierającej dane referencyjne. Funkcja [Zadania elastyczne](elastic-jobs-overview.md) jest używana do wykonywania tych akcji we wszystkich bazach danych dzierżawy i w bazie danych szablonów używanej do tworzenia nowych baz danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Tworzenie agenta zadania
> * Przyczyna uruchamiania zadań T-SQL we wszystkich bazach danych dzierżawy
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżawy
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Bazy danych SaaS na dzierżawę Wingtip Tickets. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie bazy danych SaaS biletów Wingtip na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano najnowszą wersję programu SSMS (SQL Server Management Studio). [Pobieranie i instalowanie systemu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ten samouczek zakłada użycie funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji SaaSFeedback@microsoft.com z subject=Elastic Jobs Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza SaaSFeedback@microsoft.com jest ograniczona, więc skontaktuj się z powiązanymi pytaniami lub pomocą techniczną.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematami SaaS

Baza danych na wzorzec dzierżawy skutecznie izoluje dane dzierżawy, ale zwiększa liczbę baz danych do zarządzania i obsługi. [Zadania elastyczne](elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie bazami danych SQL. Zadania umożliwiają bezpieczne i niezawodne uruchamianie zadań (skrypty T-SQL) względem grupy baz danych. Zadania można wdrożyć schemat i typowe zmiany danych referencyjnych we wszystkich bazach danych dzierżawy w aplikacji. Zadania elastyczne mogą być również używane do obsługi bazy danych *szablonów* używanej do tworzenia nowych dzierżaw, zapewniając, że zawsze ma najnowsze dane schematu i odwołania.

![ekran](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Istnieje nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją usługi Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ta ograniczona wersja zapoznawcza obsługuje obecnie tworzenie agenta zadań przy użyciu programu PowerShell, a także funkcję T-SQL do tworzenia zadań i zarządzania nimi.

> [!NOTE]
> Ten samouczek zakłada użycie funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji SaaSFeedback@microsoft.com z subject=Elastic Jobs Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza SaaSFeedback@microsoft.com jest ograniczona, więc skontaktuj się z powiązanymi pytaniami lub pomocą techniczną.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz bazę danych SaaS Wingtip Tickets na skrypty aplikacji dzierżawcy

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Tworzenie bazy danych agenta zadań i nowego agenta zadania

Ten samouczek wymaga użycia programu PowerShell do utworzenia agenta zadań i jego zapasowej bazy danych agenta zadań. Baza danych agenta zadań zawiera definicje zadań, stan zadania i historię. Po utworzeniu agenta zadania i jego bazy danych można natychmiast tworzyć i monitorować zadania.

1. **W programie PowerShell ISE**otwórz ... \\Moduły\\szkoleniowe\\Zarządzanie schematem*Demo-SchemaManagement.ps1*.
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt *Demo-SchemaManagement.ps1* wywołuje skrypt *Deploy-SchemaManagement.ps1* w celu utworzenia bazy danych SQL o nazwie *osagent* na serwerze katalogu. Następnie tworzy agenta zadania, używając bazy danych jako parametru.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

W aplikacji Bilety Wingtip każda baza danych dzierżawy zawiera zestaw obsługiwanych typów miejsc. Każde miejsce ma określony typ miejsca, który definiuje rodzaj zdarzeń, które mogą być hostowane i określa obraz tła używany w aplikacji. Aby aplikacja obsługiwała nowe rodzaje zdarzeń, te dane referencyjne muszą zostać zaktualizowane i dodane nowe typy miejsc.  W tym ćwiczeniu wdrożysz i uaktualnisz wszystkie bazy danych dzierżaw w celu dodania dwóch dodatkowych typów miejsc: *Motorcycle Racing* i *Swimming Club*.

Najpierw przejrzyj typy miejsc zawarte w każdej bazie danych dzierżawy. Połącz się z jedną z baz danych dzierżawy w programie SQL Server Management Studio (SSMS) i sprawdź tabelę VenueTypes.  Można również kwerendy tej tabeli w edytorze zapytań w witrynie Azure portal, dostępne ze strony bazy danych. 

1. Otwórz usługę SSMS i połącz się z serwerem dzierżawy: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Aby potwierdzić, że *Wyścigi motocyklowe* i *Klub Pływacki* **nie są** obecnie uwzględnione, przejdź do bazy danych _contosoconcerthall_ na serwerze *użytkownika&lt;&gt; tenants1-dpt-* i prześp tabelę *VenueTypes.*

Teraz utwórzmy zadanie, aby zaktualizować *VenueTypes* tabeli we wszystkich bazach danych dzierżawy, aby dodać nowe typy miejsc.

Aby utworzyć nowe zadanie, należy użyć zestawu procedur przechowywanych systemu zadań utworzonych w bazie danych _jobagent_ podczas tworzenia agenta zadania.

1. W systemie SSMS połącz się z serwerem katalogu: *catalog-dpt-&lt;&gt;user .database.windows.net* server 
1. W SSMS otwórz plik ... \\Moduły\\szkoleniowe\\Wdrażanie zarządzania schematem.sql
1. Zmodyfikuj &lt;&gt; instrukcję: SET @wtpUser = użytkownik i zastąp wartość Użytkownika używaną podczas wdrażania aplikacji Bazy danych SaaS Wingtip na dzierżawę
1. Upewnij się, że masz połączenie z bazą danych _jobagent_ i naciśnij **klawisz F5,** aby uruchomić skrypt

Obserwuj następujące elementy w skrypcie *DeployReferenceData.sql:*
* **sp\_\_dodaj\_grupę docelową** tworzy nazwę grupy docelowej DemoServerGroup.
* **sp\_\_add\_\_element członkowski grupy docelowej** służy do definiowania zestawu docelowych baz danych.  Najpierw jest dodawany serwer _&lt;użytkownika&gt; tenants1-dpt- user._  Dodanie serwera jako obiektu docelowego powoduje, że bazy danych na tym serwerze w momencie wykonywania zadania mają zostać uwzględnione w zadaniu. Następnie _basetenantdb_ bazy danych i bazy danych *adhocreporting* (używane w późniejszym samouczku) są dodawane jako obiekty docelowe.
* **sp\_\_add zadanie** tworzy zadanie o nazwie _Reference Data Deployment_.
* **sp\_\_add jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL, aby zaktualizować tabelę odwołań, VenueTypes.
* Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Użyj tych kwerend, aby przejrzeć wartość stanu w kolumnie **cyklu życia,** aby określić, kiedy zadanie zostało zakończone we wszystkich docelowych bazach danych.

Po zakończeniu skryptu można sprawdzić, czy dane referencyjne zostały zaktualizowane.  W programie SSMS przejdź do bazy danych *contosoconcerthall* na serwerze *użytkownika&lt;&gt; tenants1-dpt-* i przekonuj tabelę *VenueTypes.*  Sprawdź, czy *wyścigi motocyklowe* i *klub pływacki* **są** już obecne.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

To ćwiczenie używa zadania do odbudowania indeksu w kluczu podstawowym tabeli odwołań.  Jest to typowa operacja konserwacji bazy danych, która może być wykonana po załadowaniu dużych ilości danych.

Utwórz zadanie, używając tych samych procedur składowanych „system”.

1. Otwórz SSMS i połącz się z _serwerem .database.windows.net&lt;catalog-dpt- user&gt;_
1. Otwórz plik _... Moduły\\szkoleniowe\\Zarządzanie schematami OnlineReindex.sql \\_
1. Kliknij prawym przyciskiem myszy, wybierz pozycję Połączenie i połącz się z _&lt;serwerem .database.windows.net użytkownika&gt;catalog-dpt,_ jeśli nie jest już podłączony
1. Upewnij się, że masz połączenie z bazą danych _jobagent_ i naciśnij **klawisz F5,** aby uruchomić skrypt

Należy przestrzegać następujących elementów w skrypcie _OnlineReindex.sql:_
* **sp\_\_dodać zadanie** tworzy nowe zadanie o nazwie\_\_"Online\_Reindex PK VenueTyp\_265E44FD7FD4C885"
* **sp\_\_add jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL w celu zaktualizowania indeksu
* Pozostałe widoki w wykonaniu zadania monitora skryptu. Użyj tych kwerend, aby przejrzeć wartość stanu w kolumnie **cyklu życia,** aby określić, kiedy zadanie zostało pomyślnie zakończone na wszystkich członków grupy docelowej.



## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Tworzenie agenta zadań w celu uruchamiania w wielu bazach danych w zadaniach T-SQL
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżawy
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [samouczek raportowania ad hoc,](saas-tenancy-cross-tenant-reporting.md) aby zbadać uruchamianie zapytań rozproszonych w bazach danych dzierżawy.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które opierają się na wdrożeniu aplikacji bazy danych SaaS Wingtip na dzierżawę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](elastic-jobs-overview.md)