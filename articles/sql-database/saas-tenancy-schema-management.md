---
title: Zarządzanie schematami usługi Azure SQL Database w aplikacji z wieloma dzierżawami | Microsoft Docs
description: Zarządzanie schematami wielu dzierżaw w aplikacji z wieloma dzierżawami, która korzysta z usługi Azure SQL Database
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 12d8f3cc9fa4ec75f247b6ed602e120b0302be0e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Zarządzanie schematu w aplikacji SaaS przy użyciu wzorca bazy danych na dzierżawcy z bazy danych SQL Azure

W miarę rozwoju środowisko aplikacji bazy danych zmiany natychmiastową należy wykonać w danych schematu lub odwołanie do bazy danych.  Okresowo potrzebne są także zadania konserwacji bazy danych. Zarządzanie aplikacji korzystającej z bazy danych na wzorzec dzierżawy wymaga zastosowania te zmiany i zadania konserwacji na floty dzierżawy baz danych.

W tym samouczku Eksploruje dwa scenariusze - wdrażania aktualizacji danych odwołania dla wszystkich dzierżawców i odbudowywania indeksu dla tabeli zawierającej dane referencyjne. [Zadania elastyczne](sql-database-elastic-jobs-overview.md) funkcja służy do wykonywania tych akcji, wszystkie bazy danych dzierżawy i z szablonu bazy danych używane do tworzenia baz danych w nowym dzierżawcą.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Utwórz agenta zadania
> * Spowodować T-SQL zadań można uruchamiać na wszystkie dzierżawy bazy danych.
> * Dane referencyjne aktualizacji w wszystkie dzierżawy bazy danych
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS bazy danych na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano najnowszą wersję programu SSMS (SQL Server Management Studio). [Pobieranie i instalowanie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi baza danych SQL, które są w ograniczonym wersji zapoznawczej (zadania elastycznej bazy danych). Jeśli chcesz zrobić w tym samouczku, podaj identyfikator subskrypcji do SaaSFeedback@microsoft.com z podmiotem = elastycznej Podgląd zadania. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczony, więc skontaktuj się z SaaSFeedback@microsoft.com pytania związane z lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców Zarządzanie schematu SaaS

Bazy danych na wzorzec dzierżawy skutecznie izoluje dane dzierżawy, ale zwiększa liczbę baz danych do zarządzania i obsługi. [Zadania elastyczne](sql-database-elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie baz danych serwera SQL. Zadania umożliwiają bezpieczne i niezawodne, uruchamiania zadań (skryptów T-SQL) przed grupę baz danych. Zadania można wdrożyć schematu i wspólne zmiany danych odwołania dla wszystkich dzierżawców baz danych w aplikacji. Zadania elastyczne również może być używany do obsługi *szablonu* bazy danych używane do tworzenia nowi dzierżawcy, zapewniając on zawsze zawiera najnowsze dane schematu i odwołania.

![ekran](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Brak nowej wersji zadania elastyczne, która jest teraz zintegrowany funkcji bazy danych SQL Azure. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ta ograniczona wersja zapoznawcza obecnie obsługuje przy użyciu programu PowerShell, aby utworzyć agenta zadania i T-SQL, aby tworzyć zadania i zarządzać nimi.

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi baza danych SQL, które są w ograniczonym wersji zapoznawczej (zadania elastycznej bazy danych). Jeśli chcesz zrobić w tym samouczku, podaj identyfikator subskrypcji do SaaSFeedback@microsoft.com z podmiotem = elastycznej Podgląd zadania. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczony, więc skontaktuj się z SaaSFeedback@microsoft.com pytania związane z lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz bazę danych SaaS biletów Wingtip na skryptów aplikacji dzierżawy

Skrypty zarządzania i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Utwórz agenta zadania bazę danych i nowego zadania agenta

Ten samouczek wymaga programu PowerShell jest używany do utworzenia agenta zadania i jego bazy danych z agenta zadania tworzenia kopii. Baza danych agenta zadania zawiera definicje zadań, stan zadania i historii. Po utworzeniu zadania agenta i jego bazy danych można tworzyć i natychmiast monitorowania zadań.

1. **W środowisku PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\Zarządzanie schematami\\*SchemaManagement.ps1 pokaz*.
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

*SchemaManagement.ps1 pokaz* skryptu wywołania *SchemaManagement.ps1 Wdróż* skrypt służący do tworzenia bazy danych SQL o nazwie *osagent* na serwerze wykazu. Tworzy następnie agent zadań przy użyciu bazy danych jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

W aplikacji biletów Wingtip każdej dzierżawy bazy danych zawiera zestaw miejscową obsługiwanych typów. Każda właściwość jest typu określone miejsce, który definiuje typ zdarzenia, które mogą być hostowane i określa obrazu tła używane w aplikacji. Dla aplikacji do obsługi nowych rodzajów zdarzeń to dane referencyjne muszą być miejscową zaktualizowanych i nowych typów dodane.  W tym ćwiczeniu wdrożysz i uaktualnisz wszystkie bazy danych dzierżaw w celu dodania dwóch dodatkowych typów miejsc: *Motorcycle Racing* i *Swimming Club*.

Najpierw sprawdź typy miejscową zawarte w każdej bazie danych dzierżawy. Połącz do jednej z baz danych dzierżawy w programu SQL Server Management Studio (SSMS) i sprawdzić VenueTypes tabeli.  Możesz także zbadać tej tabeli w edytorze zapytań w portalu Azure, dostępne na stronie Baza danych. 

1. Otwórz program SSMS i nawiąż połączenie z serwerem dzierżawy: *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
1. Aby potwierdzić, że *Racing motocykla* i *klub pływackich* **nie są** aktualnie włączone, przejdź do _contosoconcerthall_ bazy danych na *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytań *VenueTypes* tabeli.

Teraz Utwórzmy zadanie do aktualizacji *VenueTypes* tabeli w bazach dzierżawy Dodawanie nowych typów miejsce.

Aby utworzyć nowe zadanie, użycie zestawu systemu zadania utworzone procedur składowanych w _jobagent_ bazy danych podczas tworzenia zadania agenta.

1. W programie SSMS, nawiąż połączenie z serwerem wykazu: *katalogu-dpt -&lt;użytkownika&gt;. database.windows.net* serwera 
1. W programie SSMS Otwórz plik... \\Modułów szkoleniowych\\Zarządzanie schematami\\DeployReferenceData.sql
1. Modyfikowanie instrukcji: Ustaw @wtpUser = &lt;użytkownika&gt; i zastąp wartość użytkownika używane w przypadku wdrażania aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy
1. Upewnij się, czy nawiązano _jobagent_ bazy danych i naciśnij klawisz **F5** do uruchomienia skryptu

Sprawdź następujące elementy w *DeployReferenceData.sql* skryptu:
* **SP\_dodać\_docelowej\_grupy** tworzy Nazwa grupy docelowej DemoServerGroup.
* **SP\_dodać\_docelowej\_grupy\_elementu członkowskiego** służy do definiowania zbiór docelowymi bazami danych.  Pierwszy _tenants1-dpt -&lt;użytkownika&gt;_  serwer jest dodawany.  Dodawanie serwera jako miejsce docelowe powoduje baz danych na tym serwerze, w czasie wykonywania zadań, które mają zostać uwzględnione w zadaniu. Następnie przy użyciu _basetenantdb_ bazy danych i *adhocreporting* bazy danych (używane w późniejszym samouczek) są dodawane jako miejsca docelowe.
* **SP\_dodać\_zadania** tworzy zadanie o nazwie _wdrożenia danych odwołania_.
* **SP\_dodać\_etap zadania** tworzy etap zadania zawierające tekst polecenia T-SQL, aby zaktualizować tabeli referencyjnej VenueTypes.
* Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Aby przejrzeć wartość stanu, użyj tych zapytań **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało zakończone na wszystkich z docelowymi bazami danych.

Po ukończeniu działania skryptu, możesz sprawdzić, czy dane referencyjne zostały zaktualizowane.  W programie SSMS, przejdź do *contosoconcerthall* bazy danych na *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytań *VenueTypes* tabeli.  Sprawdź, czy *Racing motocykla* i *klub pływackich* **są** teraz istnieje.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

Tego ćwiczenia używa zadania, aby odbudować indeksu przy użyciu klucza podstawowego tabeli odniesienia.  Jest to typowe bazy danych operacji konserwacji, które mogą zostać wykonane po załadowaniu dużych ilości danych.

Utwórz zadanie, używając tych samych procedur składowanych „system”.

1. Otwórz SSMS i połącz się _katalogu-dpt -&lt;użytkownika&gt;. database.windows.net_ serwera
1. Otwórz plik _... \\Modułów szkoleniowych\\Zarządzanie schematami\\OnlineReindex.sql_
1. Kliknij prawym przyciskiem myszy, wybierz połączenie i połącz się _katalogu-dpt -&lt;użytkownika&gt;. database.windows.net_ serwera, jeśli nie został jeszcze połączony
1. Upewnij się, czy nawiązano _jobagent_ bazy danych i naciśnij klawisz **F5** do uruchomienia skryptu

Sprawdź następujące elementy w _OnlineReindex.sql_ skryptu:
* **SP\_dodać\_zadania** tworzy nowe zadanie o nazwie "Online PK indeksowanie\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_dodać\_etap zadania** tworzy etap zadania zawierające tekst polecenia T-SQL do aktualizowania indeksu
* Pozostałe widoków w skrypcie monitorować wykonywania zadania. Aby przejrzeć wartość stanu, użyj tych zapytań **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało ukończone pomyślnie dla wszystkich członków grupy docelowej.



## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Utworzyć agenta zadania do uruchomienia przez zadania T-SQL wielu baz danych
> * Dane referencyjne aktualizacji w wszystkie dzierżawy bazy danych
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [samouczek raportowania Ad hoc](saas-tenancy-cross-tenant-reporting.md) do eksplorowania uruchamianie zapytań rozproszonych we dzierżawcy z bazy danych.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które zależą od wdrożenia aplikacji Wingtip biletów SaaS bazy danych dla dzierżawy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](sql-database-elastic-jobs-overview.md)
* [Tworzenie baz danych skalowanych w poziomie i zarządzanie nimi](sql-database-elastic-jobs-create-and-manage.md)