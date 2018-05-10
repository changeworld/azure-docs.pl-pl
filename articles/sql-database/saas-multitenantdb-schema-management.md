---
title: Zarządzanie schematami usługi Azure SQL Database w aplikacji z wieloma dzierżawami | Microsoft Docs
description: Zarządzanie schematami wielu dzierżaw w aplikacji z wieloma dzierżawami, która korzysta z usługi Azure SQL Database
keywords: samouczek usługi sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: dc70fe43c63d6b77d4a122f196f59fe51573b7aa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Zarządzanie schematu w aplikacji SaaS, która używa podzielonej wielodostępnych baz danych SQL

W tym samouczku sprawdza trudności w zachowaniu floty baz danych w aplikacja oprogramowania jako usługa (SaaS). Rozwiązania przedstawiono dla wentylujące zmian schematu w floty baz danych.

Podobnie jak wszelkie aplikacje aplikacji SaaS biletów Wingtip rozpoczyna się wraz z upływem czasu i będzie wymagać zmiany w bazie danych. Zmiany mogą mieć wpływ na dane schematu lub odwołanie lub Zastosuj zadania konserwacji bazy danych. Przy użyciu aplikacji SaaS przy użyciu bazy danych na wzorzec dzierżawy zmiany musi koordynowane przez potencjalnie ogromną floty dzierżawy baz danych. Ponadto musi ona zawierać te zmiany do bazy danych procesu, aby upewnić się, że są one uwzględnione w nowych baz danych, tworzonych udostępniania.

#### <a name="two-scenarios"></a>Dwa scenariusze

W tym samouczku Eksploruje dwa następujące scenariusze:
- Wdróż aktualizacje danych odwołania dla wszystkich dzierżawców.
- Odbudować indeksu dla tabeli, która zawiera dane referencyjne.

[Zadania elastyczne](sql-database-elastic-jobs-overview.md) funkcja bazy danych SQL Azure jest używana do wykonywania tych operacji dla dzierżawy baz danych. Zadania również działać w bazie danych dzierżawy "template". W przykładowej aplikacji Wingtip biletów tego szablonu bazy danych jest kopiowany do obsługi administracyjnej nowej bazy danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz agenta zadania.
> * Wykonywanie zapytań T-SQL na wielu dzierżawców baz danych.
> * Aktualizuj dane odwołanie w wszystkie dzierżawy bazy danych.
> * Tworzenie indeksu dla tabeli w wszystkie dzierżawy bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Bilety Wingtip aplikacji wielodostępnych bazy danych musi już wdrożony:
    - Aby uzyskać instrukcje Zobacz pierwszy samouczek wprowadza aplikacji bazy danych z wieloma dzierżawcami Wingtip biletów SaaS:<br />[Wdrażanie i Eksploruj podzielonej aplikacji wielodostępnych, która używa usługi Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Proces wdrażania jest uruchamiany na mniej niż pięć minut.
    - Musi mieć *podzielonej wielodostępne* wersji Wingtip zainstalowane. Wersje *autonomiczny* i *bazy danych dla każdego dzierżawcy* nie obsługują tego samouczka.

- Musi być zainstalowana najnowsza wersja programu SQL Server Management Studio (SSMS). [Pobierz i zainstaluj narzędzia SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Musi być zainstalowany program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi baza danych SQL Azure, które są w podglądzie ograniczone ([zadania elastycznej bazy danych](sql-database-elastic-database-client-library.md)). Jeśli chcesz zrobić w tym samouczku, podaj identyfikator subskrypcji do *SaaSFeedback@microsoft.com* z podmiotem = elastycznej Podgląd zadania. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczony, więc skontaktuj się z *SaaSFeedback@microsoft.com* pytania związane z lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców Zarządzanie schematu SaaS

Model podzielonej wielodostępne bazy danych używany w tym przykładzie umożliwia dzierżawcom bazy danych zawiera co najmniej jednego dzierżawcy. W tym przykładzie Eksploruje może używać różnych dzierżawy wielu i jednego dzierżawcy bazy danych, włączanie *hybrydowego* model zarządzania dzierżawy. Zarządzanie zmianami w tych baz danych może być skomplikowane. [Zadania elastyczne](sql-database-elastic-jobs-overview.md) ułatwia administrowanie i zarządzanie dużą liczbą bazy danych. Zadania umożliwiają bezpieczne i niezawodne uruchamiać skrypty języka Transact-SQL jako zadania w odniesieniu do grupy baz danych dzierżawy. Zadania są niezależne od działań użytkownika lub danych wejściowych. Ta metoda może służyć do wdrażania zmiany do schematu lub wspólnych danych referencyjnych, we wszystkich dzierżawców w aplikacji. Zadania elastyczne mogą służyć do obsługi uwierzytelniania golden szablonu kopię bazy danych. Ten szablon służy do tworzenia nowych dzierżaw, zawsze zapewnienia najnowszej schematu i dane referencyjne są używane.

![ekran](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Brak nowej wersji zadania elastyczne, która jest teraz zintegrowany funkcji bazy danych SQL Azure. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ograniczony podglądu obecnie obsługuje tworzenie agenta zadania i T-SQL, aby tworzyć zadania i zarządzać nimi za pomocą programu PowerShell.
> [!NOTE] 
> W tym samouczku korzysta z funkcji usługi baza danych SQL, które są w ograniczonym wersji zapoznawczej (zadania elastycznej bazy danych). Jeśli chcesz zrobić w tym samouczku, podaj identyfikator subskrypcji do SaaSFeedback@microsoft.com z podmiotem = elastycznej Podgląd zadania. Po otrzymaniu potwierdzenia włączenia subskrypcji, Pobierz i zainstaluj polecenia cmdlet najnowszej wersji wstępnej zadania. Ta wersja zapoznawcza jest ograniczony, więc skontaktuj się z SaaSFeedback@microsoft.com pytania związane z lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobieranie kodu źródłowego aplikacji Wingtip biletów SaaS wielodostępne w bazie danych i skryptów

Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium w witrynie Github. Zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Utwórz agenta zadania bazę danych i nowego zadania agenta

Ten samouczek wymaga użycia programu PowerShell można utworzyć zadania agenta w bazie danych i zadania agenta. Takie jak bazy danych MSDB używana przez agenta programu SQL agent zadania używa bazy danych Azure SQL do przechowywania definicje zadań, stan zadania i historii. Po utworzeniu zadania Agenta można tworzyć i natychmiast monitorowania zadań.

1. W **PowerShell ISE**, otwórz *... \\Modułów szkoleniowych\\Zarządzanie schematami\\SchemaManagement.ps1 pokaz*.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt.

*SchemaManagement.ps1 pokaz* skryptu wywołania *SchemaManagement.ps1 Wdróż* skrypt, aby utworzyć bazę danych o nazwie _jobagent_ na serwerze wykazu. Skrypt tworzy następnie agent zadań przekazywanie _jobagent_ bazy danych jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

#### <a name="prepare"></a>Przygotowanie

Bazy danych z każdej dzierżawy zawiera zestaw typów miejsce w **VenueTypes** tabeli. Każdy typ miejscową określa rodzaj zdarzenia, które mogą być hostowane na miejscu. Te typy miejscową odpowiadają obrazy tła, które są widoczne w aplikacji zdarzenia dzierżawy.  W tym ćwiczeniu wdrożenia aktualizacji do wszystkich baz danych, aby dodać dwa typy dodatkowe miejsce: *Racing motocykla* i *klub pływackich*. 

Najpierw sprawdź typy miejscową zawarte w każdej bazie danych dzierżawy. Połącz do jednej z baz danych dzierżawy w programu SQL Server Management Studio (SSMS) i sprawdzić VenueTypes tabeli.  Możesz także zbadać tej tabeli w edytorze zapytań w portalu Azure, dostępne na stronie Baza danych. 

1. Otwórz program SSMS i nawiąż połączenie z serwerem dzierżawy: *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
1. Aby potwierdzić, że *Racing motocykla* i *klub pływackich* **nie są** aktualnie włączone, przejdź do *contosoconcerthall* bazy danych na *tenants1-dpt -&lt;użytkownika&gt;*  serwera i zapytań *VenueTypes* tabeli.



#### <a name="steps"></a>Kroki

Teraz Utwórz zadanie, aby zaktualizować **VenueTypes** tabeli w bazie danych każdego dzierżawcy, dodając dwóch nowych typów miejsce.

Aby utworzyć nowe zadanie, użyć zestawu zadań systemowych procedur składowanych utworzonych w _jobagent_ bazy danych. Procedury składowane zostały utworzone podczas tworzenia zadania agenta.

1. W programie SSMS, nawiąż połączenie z serwerem dzierżawy: tenants1-mt -&lt;użytkownika&gt;. database.windows.net

2. Przejdź do *tenants1* bazy danych.

3. Zapytanie *VenueTypes* tabeli, aby potwierdzić, że *Racing motocykla* i *klub pływackich* nie są jeszcze na liście wyników.

4. Połączyć się z serwerem katalogu, który jest *katalogu-mt -&lt;użytkownika&gt;. database.windows.net*.

5. Połączyć się z _jobagent_ bazy danych na serwerze wykazu.

6. W programie SSMS, otwórz plik *... \\Modułów szkoleniowych\\Zarządzanie schematami\\DeployReferenceData.sql*.

7. Modyfikowanie instrukcji: Ustaw @User = &lt;użytkownika&gt; i zastąp wartość użytkownika używane w przypadku wdrażania aplikacji Wingtip biletów SaaS wielodostępne w bazie danych.

8. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwuj

Sprawdź następujące elementy w *DeployReferenceData.sql* skryptu:

- **SP\_dodać\_docelowej\_grupy** tworzy Nazwa grupy docelowej *DemoServerGroup*, i dodaje docelowy członków do grupy.

- **SP\_dodać\_docelowej\_grupy\_elementu członkowskiego** dodaje następujące elementy:
    - A *serwera* docelowy typ elementu członkowskiego.
        - Jest to *tenants1-mt -&lt;użytkownika&gt;*  serwera, który zawiera bazy danych dzierżawców.
        - W tym serwera obejmuje baz danych dzierżawy, które istnieją w czasie, który wykonuje zadanie.
    - A *bazy danych* docelowy typ elementu członkowskiego dla szablonu bazy danych (*basetenantdb*) który znajduje się na *katalogu-mt -&lt;użytkownika&gt;*  serwera
    - A *bazy danych* docelowy typ elementu członkowskiego, aby uwzględnić *adhocreporting* bazy danych, która jest używana w późniejszym samouczka.

- **SP\_dodać\_zadania** tworzy zadanie o nazwie *wdrożenia danych odwołania*.

- **SP\_dodać\_etap zadania** tworzy etap zadania zawierające tekst polecenia T-SQL, aby zaktualizować tabeli referencyjnej VenueTypes.

- Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Aby przejrzeć wartość stanu, użyj tych zapytań **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało ukończone. Zadanie aktualizacji bazy danych dzierżawców i aktualizuje dwóch dodatkowych baz danych zawierających tabeli referencyjnej.

W programie SSMS, przejdź do bazy danych dzierżawy na *tenants1-mt -&lt;użytkownika&gt;*  serwera. Zapytanie *VenueTypes* tabeli, aby potwierdzić, że *Racing motocykla* i *klub pływackich* są teraz dodane do tabeli. Całkowita liczba typów miejscową musi wzrosnąć przez dwa.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

Tego ćwiczenia tworzy zadanie, aby odbudować indeksu przy użyciu klucza podstawowego tabeli odwołania do wszystkich baz danych dzierżawy. Odbudowywanie indeksu jest typowe bazy danych operacji zarządzania, które mogą zostać uruchomione przez administratora po załadowaniu dużą ilość danych obciążenia, aby zwiększyć wydajność.

1. W programie SSMS, nawiąż połączenie z _jobagent_ bazy danych w *katalogu-mt -&lt;użytkownika&gt;. database.windows.net* serwera.

2. Otwórz w programie SSMS, *... \\Modułów szkoleniowych\\Zarządzanie schematami\\OnlineReindex.sql*.

3. Naciśnij klawisz **F5**, aby uruchomić skrypt.

#### <a name="observe"></a>Obserwuj

Sprawdź następujące elementy w *OnlineReindex.sql* skryptu:

* **SP\_dodać\_zadania** tworzy nowe zadanie o nazwie *Online ponowna indeksacja PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_dodać\_etap zadania** tworzy etap zadania zawierające tekst polecenia T-SQL do aktualizowania indeksu.

* Pozostałe widoków w skrypcie monitorować wykonywania zadania. Aby przejrzeć wartość stanu, użyj tych zapytań **cyklu życia** kolumnę, aby określić, kiedy zadanie zostało ukończone pomyślnie dla wszystkich członków grupy docelowej.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](sql-database-elastic-jobs-overview.md)
* [Tworzenie baz danych skalowanych w poziomie i zarządzanie nimi](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
.
> * Utworzyć agenta zadania do uruchomienia zadań T-SQL przez wiele baz danych
> * Dane referencyjne aktualizacji w wszystkie dzierżawy bazy danych
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie spróbuj [samouczek raportowania Ad hoc](saas-multitenantdb-adhoc-reporting.md) do eksplorowania uruchamianie zapytań rozproszonych we dzierżawcy z bazy danych.

