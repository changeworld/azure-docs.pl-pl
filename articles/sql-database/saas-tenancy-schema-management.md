---
title: Zarządzanie schematami usługi Azure SQL Database w aplikacji z jedną dzierżawą | Dokumentacja firmy Microsoft
description: Zarządzanie schematami wielu dzierżaw w aplikacji pojedynczej dzierżawy, który używa usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: eb461367d58f7cadeccd434c0e4ab452b7fc640e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241916"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Zarządzanie schematami w aplikacji SaaS przy użyciu wzorca bazy danych dla dzierżawy usługa Azure SQL Database
 
Zgodnie z rozwojem aplikacji bazy danych zmian natychmiastową należy podjąć, z danymi schematu lub odwołanie do bazy danych.  Okresowo potrzebne są także zadania konserwacji bazy danych. Zarządzanie aplikacji korzystającej z bazy danych dla każdego dzierżawcy wzorzec wymaga zastosować te zmiany, lub zadania konserwacji w wielu bazach danych dzierżaw.

Ten samouczek analizuje dwa scenariusze — wdrażanie aktualizacji danych referencyjnych dla wszystkich dzierżaw i ponowne tworzenie indeksu tabeli zawierającej dane referencyjne. [Zadań elastycznych](elastic-jobs-overview.md) funkcja jest używana do wykonywania tych akcji, na wszystkich baz danych dzierżaw, a na bazie szablonu użyty do utworzenia nowej dzierżawy baz danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Utwórz agenta zadania
> * Spowodować zadań T-SQL, należy uruchomić na wszystkich baz danych dzierżaw
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja o nazwie Wingtip Tickets SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy aplikacji](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano najnowszą wersję programu SSMS (SQL Server Management Studio). [Pobieranie i instalowanie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli użytkownik chce skorzystać z tego samouczka, Prześlij identyfikator swojej subskrypcji w taki sposób, aby SaaSFeedback@microsoft.com podmiotu = Elastic Job Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Tej wersji zapoznawczej jest ograniczona, dlatego skontaktuj się z SaaSFeedback@microsoft.com pytań lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematu SaaS

Bazy danych dla każdego dzierżawcy wzorzec skutecznie izoluje dane dzierżawy, ale zwiększa się liczba baz danych, zarządzanie i obsługa. [Zadania elastyczne](elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie bazami danych SQL. Zadania umożliwiają bezpieczne i niezawodne uruchamiania zadań (skryptów T-SQL) względem grupy baz danych. Zadań można wdrożyć schematu i zmiany wspólnych danych referencyjnych we wszystkich bazach danych dzierżaw w aplikacji. Zadania elastyczne można również utrzymania *szablonu* bazy danych używanej do tworzenia nowych dzierżaw, dzięki czemu zawsze zawiera najnowsze schemat i dane referencyjne.

![ekran](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Istnieje nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją usługi Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ograniczona wersja zapoznawcza obsługuje obecnie przy użyciu programu PowerShell, aby utworzyć agenta zadań i T-SQL do tworzenia zadań i zarządzanie nimi.

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli użytkownik chce skorzystać z tego samouczka, Prześlij identyfikator swojej subskrypcji w taki sposób, aby SaaSFeedback@microsoft.com podmiotu = Elastic Job Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Tej wersji zapoznawczej jest ograniczona, dlatego skontaktuj się z SaaSFeedback@microsoft.com pytań lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy skryptów aplikacji

Skrypty kodu i zarządzanie źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Utwórz agenta zadania bazy danych i nowy agent zadania

Ten samouczek wymaga użycia programu PowerShell, aby utworzyć agenta zadań i jej baza danych agenta zadań tworzenia kopii. Baza danych agenta zadań zawiera definicje zadań, stan zadania i historię. Po utworzeniu agenta zadania i jego bazy danych można tworzyć i monitorować zadania od razu.

1. **W środowisku PowerShell ISE**, Otwórz... \\Learning Modules\\Zarządzanie schematami\\*Demo-SchemaManagement.ps1*.
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

*Demo-SchemaManagement.ps1* wywołuje skrypt *Deploy-SchemaManagement.ps1* skrypt, aby utworzyć bazę danych SQL o nazwie *osagent* na serwerze wykazu. Następnie tworzy zadanie agenta, używana jest baza danych jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

W aplikacji Wingtip Tickets każda baza danych dzierżawy obejmuje zestaw typów miejsc obsługiwane. Każde miejsce jest typu określonych miejsc, który definiuje typ zdarzenia, które mogą być hostowane oraz określa obrazu tła używane w aplikacji. Dla aplikacji do obsługi nowych rodzajów zdarzeń to dane referencyjne muszą być miejsc zaktualizowanych i nowych typów dodanych.  W tym ćwiczeniu wdrożenia aktualizacji do wszystkich baz danych dzierżawy do dodania dwóch dodatkowych typów miejsc: *Motorcycle Racing* i *Swimming Club*.

Po pierwsze Przejrzyj typów miejsc zawarte w każdej bazie danych dzierżawy. Połącz się z jednym baz danych dzierżaw w SQL Server Management Studio (SSMS) i sprawdź w tabeli VenueTypes.  Możesz także zbadać tej tabeli w edytorze zapytań w witrynie Azure portal, dostępny na stronie bazy danych. 

1. Otwórz aplikację SSMS i nawiązać połączenie z serwerem dzierżaw: *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
1. Aby potwierdzić, że *Motorcycle Racing* i *Swimming Club* **nie** aktualnie włączone, przejdź do _contosoconcerthall_ Baza danych *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytania *VenueTypes* tabeli.

Teraz Utwórzmy zadanie, aby zaktualizować *VenueTypes* tabeli we wszystkich bazach danych dzierżaw do dodawania nowych typów miejsc.

Aby utworzyć nowe zadanie, należy użyć zestaw zadań systemu procedury przechowywane utworzone w _jobagent_ bazy danych podczas tworzenia zadania agenta.

1. W programie SSMS, połączenia z serwerem wykazu: *catalog-dpt -&lt;użytkownika&gt;. database.windows.net* serwera 
1. W programie SSMS Otwórz plik... \\Learning Modules\\Zarządzanie schematami\\DeployReferenceData.sql
1. Modyfikowanie instrukcji: Ustaw @wtpUser = &lt;użytkownika&gt; i zastąp wartość użytkownika podanej podczas wdrażania aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy
1. Upewnij się, nawiązano _jobagent_ bazy danych i naciśnij klawisz **F5** do uruchomienia skryptu

Sprawdź następujące elementy w *DeployReferenceData.sql* skryptu:
* **SP\_Dodaj\_docelowej\_grupy** tworzy nazwę grupy docelowej DemoServerGroup.
* **SP\_Dodaj\_docelowej\_grupy\_elementu członkowskiego** służy do definiowania zestawu docelowych baz danych.  Pierwszy _tenants1-dpt -&lt;użytkownika&gt;_  serwer zostanie dodany.  Dodawanie serwera jako element docelowy powoduje, że bazy danych na tym serwerze w czasie wykonywania zadań, które mają zostać uwzględnione w zadaniu. A następnie _basetenantdb_ bazy danych i *adhocreporting* bazy danych (używane później w samouczku) są dodawane jako elementy docelowe.
* **SP\_Dodaj\_zadania** tworzy zadanie o nazwie _Reference Data Deployment_.
* **SP\_Dodaj\_jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL do aktualizacji tabeli referencyjnej VenueTypes.
* Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Korzystać z tych zapytań, sprawdź wartość stanu w **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało zakończone na wszystkich docelowych baz danych.

Po zakończeniu skryptu, możesz sprawdzić, czy dane referencyjne został zaktualizowany.  W programie SSMS przejdź do *contosoconcerthall* bazy danych na *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytania *VenueTypes* tabeli.  Sprawdź, czy *Motorcycle Racing* i *Swimming Club* **są** teraz przedstawiać.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

W tym ćwiczeniu użyto zadania do odbudowania indeksu klucza podstawowego tabeli referencyjnej.  Jest to typowy bazy danych operacji konserwacji, która może być wykonana po załadowaniu dużej ilości danych.

Utwórz zadanie, używając tych samych procedur składowanych „system”.

1. Otwórz aplikację SSMS i połącz się z _catalog-dpt -&lt;użytkownika&gt;. database.windows.net_ serwera
1. Otwórz plik _... \\Learning Modules\\Zarządzanie schematami\\OnlineReindex.sql_
1. Kliknij prawym przyciskiem myszy, wybierz połączenie i połączyć się z _catalog-dpt -&lt;użytkownika&gt;. database.windows.net_ serwera, jeśli nie masz połączenia
1. Upewnij się, nawiązano _jobagent_ bazy danych i naciśnij klawisz **F5** do uruchomienia skryptu

Sprawdź następujące elementy w _OnlineReindex.sql_ skryptu:
* **SP\_Dodaj\_zadania** tworzy nowe zadanie o nazwie "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_Dodaj\_jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL do aktualizacji indeksu
* Pozostałe widoki w skrypcie monitorowania wykonania zadań. Korzystać z tych zapytań, sprawdź wartość stanu w **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało zakończone pomyślnie dla wszystkich członków grupy docelowej.



## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Utwórz zadanie agenta do uruchamiania różnych zadań T-SQL wielu baz danych
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [raportowanie Ad hoc samouczek](saas-tenancy-cross-tenant-reporting.md) do eksplorowania uruchamiania rozproszonych zapytań w dzierżawie baz danych.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki nawiązujące do wdrożenia aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](elastic-jobs-overview.md)