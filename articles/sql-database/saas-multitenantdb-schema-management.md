---
title: Zarządzanie schematami usługi Azure SQL Database w aplikacji z wieloma dzierżawami | Microsoft Docs
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
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 07e8fce5fd8db5d2070b8e382a0eba2ae7187b0d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242785"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Zarządzanie schematami w aplikacji SaaS, która używa podzielonej na fragmenty wielodostępnych baz danych SQL

Ten samouczek analizuje trudności w utrzymaniu floty bazy danych w aplikacja oprogramowania jako usługi (SaaS). Rozwiązania zostały przedstawione dla wentylujące wprowadzonych zmian schematu w całej flocie baz danych.

Podobnie jak każda aplikacja aplikacji SaaS o nazwie Wingtip Tickets ewoluuje wraz z upływem czasu i będzie wymagać zmiany w bazie danych. Zmiany mogą mieć wpływ na schemat lub odwołanie do danych lub w zadania konserwacji bazy danych. Za pomocą aplikacji SaaS przy użyciu bazy danych na wzorzec dzierżawy zmiany muszą być koordynowane między potencjalnie w bardzo wielu bazach danych dzierżaw. Ponadto musi ona zawierać tych zmian do procesu, aby upewnić się, że są one uwzględnione w nowych baz danych, ponieważ są one tworzone udostępniania bazy danych.

#### <a name="two-scenarios"></a>Dwa scenariusze

Ten samouczek analizuje dwa następujące scenariusze:
- Wdróż aktualizacje danych referencyjnych dla wszystkich dzierżaw.
- Ponowne kompilowanie indeksu dla tabeli, który zawiera dane referencyjne.

[Zadań elastycznych](elastic-jobs-overview.md) funkcja usługi Azure SQL Database jest używana do wykonywania tych operacji w bazach danych dzierżawy. Zadania również działać w bazie danych dzierżawy "template". W przykładowej aplikacji Wingtip Tickets tego szablonu bazy danych jest kopiowany do obsługi administracyjnej nowej bazy danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz agenta zadania.
> * Wykonywanie zapytania T-SQL w wielu bazach danych dzierżaw.
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw.
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja Wtp wielodostępną bazą danych muszą już być rozmieszczone:
    - Instrukcje znajdują się w pierwszym samouczku wprowadza aplikacji SaaS o nazwie Wingtip Tickets wielodostępną bazą danych:<br />[Wdrażanie i eksplorowanie podzielonej na fragmenty aplikacji z wieloma dzierżawami korzystającej z usługi Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Proces wdrażania działa przez mniej niż pięć minut.
    - Konieczne jest posiadanie *podzielonej na fragmenty wielodostępnych* wersję Wingtip zainstalowane. Wersje *autonomiczny* i *bazy danych dla każdego dzierżawcy* nie obsługują tego samouczka.

- Musi być zainstalowany najnowszej wersji programu SQL Server Management Studio (SSMS). [Pobierz i zainstaluj program SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Musi być zainstalowany program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi Azure SQL Database, które znajdują się w ograniczonej wersji zapoznawczej ([zadania Elastic Database](sql-database-elastic-database-client-library.md)). Jeśli użytkownik chce skorzystać z tego samouczka, Prześlij identyfikator swojej subskrypcji w taki sposób, aby *SaaSFeedback\@microsoft.com* podmiotu = Elastic Job Preview. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Tej wersji zapoznawczej jest ograniczona, dlatego skontaktuj się z *SaaSFeedback\@microsoft.com* pytań lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematu SaaS

Model podzielonej na fragmenty wielodostępną bazą danych, używane w tym przykładzie umożliwia bazy danych dzierżaw zawierają co najmniej jednego dzierżawcy. W tym przykładzie przedstawiono mogą używać różnych wielu dzierżawy i jedną dzierżawą baz danych, umożliwiając *hybrydowego* model zarządzania dzierżawy. Zarządzanie zmianami w tych baz danych może być skomplikowane. [Zadania elastyczne](elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie dużą liczbą bazy danych. Zadania umożliwiają bezpieczne i niezawodne uruchamianie skryptów języka Transact-SQL, jako zadań w odniesieniu do grupy baz danych dzierżaw. Zadania są niezależne od działań użytkownika lub danych wejściowych. Ta metoda może służyć do wdrażania zmian schematu lub wspólnych danych referencyjnych, we wszystkich dzierżawach w aplikacji. Zadania elastyczne można również utrzymania złoty szablonu kopię bazy danych. Ten szablon jest używany do tworzenia nowych dzierżaw, zawsze zapewnienie najnowszy schemat i dane referencyjne są używane.

![ekran](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Istnieje nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją usługi Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ograniczony Podgląd aktualnie obsługuje tworzenie agenta zadań i T-SQL do tworzenia zadań i zarządzanie nimi przy użyciu programu PowerShell.
> [!NOTE]
> W tym samouczku korzysta z funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli użytkownik chce skorzystać z tego samouczka, Prześlij identyfikator swojej subskrypcji w taki sposób, aby SaaSFeedback@microsoft.com podmiotu = Elastic Job Preview. Po otrzymaniu potwierdzenia, że Twojej subskrypcji zostało włączone, Pobierz i zainstaluj polecenia cmdlet zadań w najnowszej wersji wstępnej. Tej wersji zapoznawczej jest ograniczona, dlatego skontaktuj się z SaaSFeedback@microsoft.com pytań lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i skryptów

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium w witrynie GitHub. Zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Utwórz agenta zadania bazy danych i nowy agent zadania

Ten samouczek wymaga użycia programu PowerShell do utworzenia baza danych agenta zadań i zadań agenta. Takich jak bazy danych MSDB używany przez agenta programu SQL agent zadań używa usługi Azure SQL database do przechowywania definicji zadań, stan zadania i historię. Po utworzeniu agenta zadań można tworzyć i monitorować zadania od razu.

1. W **PowerShell ISE**, otwórz *... \\Learning Modules\\Zarządzanie schematami\\Demo-SchemaManagement.ps1*.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt.

*Demo-SchemaManagement.ps1* wywołuje skrypt *Deploy-SchemaManagement.ps1* skrypt, aby utworzyć bazę danych o nazwie _jobagent_ na serwerze wykazu. Skrypt następnie tworzy agenta zadania, przekazując _jobagent_ bazy danych jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

#### <a name="prepare"></a>Przygotowanie

Bazy danych z każdej dzierżawy obejmuje zestaw typów miejsc w **VenueTypes** tabeli. Każdy typ właściwości określa rodzaj zdarzeń, które mogą być hostowane w danym miejscu. Te typy miejsc odpowiadają obrazy tła, które są widoczne w aplikacji zdarzeń dzierżawy.  W tym ćwiczeniu wdrożenia aktualizacji do wszystkich baz danych w celu dodania dwóch dodatkowych typów miejsc: *Motorcycle Racing* i *Swimming Club*.

Po pierwsze Przejrzyj typów miejsc zawarte w każdej bazie danych dzierżawy. Połącz się z jednym baz danych dzierżaw w SQL Server Management Studio (SSMS) i sprawdź w tabeli VenueTypes.  Możesz także zbadać tej tabeli w edytorze zapytań w witrynie Azure portal, dostępny na stronie bazy danych.

1. Otwórz aplikację SSMS i nawiązać połączenie z serwerem dzierżaw: *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
1. Aby potwierdzić, że *Motorcycle Racing* i *Swimming Club* **nie** aktualnie włączone, przejdź do *contosoconcerthall* Baza danych *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytania *VenueTypes* tabeli.



#### <a name="steps"></a>Kroki

Po utworzeniu zadania można zaktualizować **VenueTypes** tabeli w każdej bazie danych dzierżaw, przez dodanie dwóch nowych typów miejsc.

Aby utworzyć nowe zadanie, należy użyć zestawu procedur składowanych w systemie zadania, które zostały utworzone w _jobagent_ bazy danych. Procedury składowane zostały utworzone, gdy utworzono agenta zadania.

1. W programie SSMS, połączyć się z serwerem dzierżaw: tenants1-mt -&lt;użytkownika&gt;. database.windows.net

2. Przejdź do *tenants1* bazy danych.

3. Zapytanie *VenueTypes* tabeli, aby potwierdzić, że *Motorcycle Racing* i *Swimming Club* nie znajdują się jeszcze na liście wyników.

4. Połączenie z serwerem wykazu, czyli *katalogu-mt -&lt;użytkownika&gt;. database.windows.net*.

5. Połączyć się z _jobagent_ bazą danych na serwerze wykazu.

6. W programie SSMS Otwórz plik *... \\Learning Modules\\Zarządzanie schematami\\DeployReferenceData.sql*.

7. Modyfikowanie instrukcji: Ustaw @User = &lt;użytkownika&gt; i zastąp wartość użytkownika podanej podczas wdrażania aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych.

8. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwuj

Sprawdź następujące elementy w *DeployReferenceData.sql* skryptu:

- **SP\_Dodaj\_docelowej\_grupy** tworzy nazwę grupy docelowej *DemoServerGroup*, i dodaje docelowych członków do grupy.

- **SP\_Dodaj\_docelowej\_grupy\_elementu członkowskiego** dodaje następujące elementy:
    - A *serwera* docelowy typ elementu członkowskiego.
        - Jest to *tenants1-mt -&lt;użytkownika&gt;*  serwer zawierający bazy danych dzierżaw.
        - Łącznie z serwerem obejmuje baz danych dzierżaw, które istnieją w czasie, który wykonuje zadania.
    - A *bazy danych* docelowy typ elementu członkowskiego dla szablonu bazy danych (*basetenantdb*), znajduje się na *katalogu-mt -&lt;użytkownika&gt;*  serwera
    - A *bazy danych* docelowy typ elementu członkowskiego, aby uwzględnić *adhocreporting* bazy danych, który jest używany później w samouczku.

- **SP\_Dodaj\_zadania** tworzy zadanie o nazwie *Reference Data Deployment*.

- **SP\_Dodaj\_jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL do aktualizacji tabeli referencyjnej VenueTypes.

- Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Korzystać z tych zapytań, sprawdź wartość stanu w **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało ukończone. Zadanie aktualizacji bazy danych dzierżaw i dwóch dodatkowych baz danych zawierających tabelę referencyjną aktualizacji.

W programie SSMS przejdź do bazy danych dzierżaw na *tenants1-mt -&lt;użytkownika&gt;*  serwera. Zapytanie *VenueTypes* tabeli, aby potwierdzić, że *Motorcycle Racing* i *Swimming Club* zostały dodane do tabeli. Łączna liczba typów miejsc powinien została zwiększona przez dwa.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

W tym zostanie utworzone zadanie służące do odbudowania indeksu klucza podstawowego tabeli referencyjnej dla wszystkich baz danych dzierżawy. Odbudowywanie indeksu jest typowy bazy danych operacji zarządzania, które mogą zostać uruchomione przez administratora po załadowaniu dużej ilości danych obciążenia, aby zwiększyć wydajność.

1. W programie SSMS, połącz się z _jobagent_ bazy danych w *catalog-mt -&lt;użytkownika&gt;. database.windows.net* serwera.

2. W programie SSMS Otwórz *... \\Learning Modules\\Zarządzanie schematami\\OnlineReindex.sql*.

3. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwuj

Sprawdź następujące elementy w *OnlineReindex.sql* skryptu:

* **SP\_Dodaj\_zadania** tworzy nowe zadanie o nazwie *Online ponowna indeksacja PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_Dodaj\_jobstep** tworzy krok zadania zawierający tekst polecenia T-SQL do aktualizacji indeksu.

* Pozostałe widoki w skrypcie monitorowania wykonania zadań. Korzystać z tych zapytań, sprawdź wartość stanu w **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało zakończone pomyślnie dla wszystkich członków grupy docelowej.

## <a name="additional-resources"></a>Dodatkowe zasoby

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](elastic-jobs-overview.md)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz zadanie agenta do uruchamiania zadań języka T-SQL w wielu bazach danych
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [raportowanie Ad hoc samouczek](saas-multitenantdb-adhoc-reporting.md) do eksplorowania uruchamiania rozproszonych zapytań w dzierżawie baz danych.

