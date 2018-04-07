---
title: Konfigurowanie synchronizacji danych SQL Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak skonfigurować synchronizację danych SQL Azure (wersja zapoznawcza)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 04/01/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 72e0ed535139c088c4235b43a12ea96da080dc8a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-sql-data-sync-preview"></a>Konfigurowanie synchronizacji danych SQL (wersja zapoznawcza)
Z tego samouczka dowiesz się sposobu konfigurowania synchronizacji danych SQL Azure, tworząc grupy synchronizacji hybrydowych, zawierającej wystąpienia zarówno usługi Azure SQL Database i programu SQL Server. Nowa grupa synchronizacji jest w pełni skonfigurowane i synchronizuje się zgodnie z harmonogramem, które można ustawić.

W tym samouczku założono, że co najmniej pewne doświadczenie z bazy danych SQL i programu SQL Server. 

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Preview) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync — wersja zapoznawcza)](sql-database-sync-data.md).

Aby uzyskać pełną przykładów programu PowerShell, które przedstawiają sposób konfigurowania synchronizacji danych SQL, zobacz następujące artykuły:
-   [Synchronizacja między wiele baz danych Azure SQL przy użyciu programu PowerShell](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Krok 1 — Tworzenie grupy synchronizacji

### <a name="locate-the-data-sync-settings"></a>Zlokalizuj ustawienia synchronizacji danych

1.  W przeglądarce przejdź do portalu Azure.

2.  W portalu Znajdź bazy danych SQL z pulpitu nawigacyjnego lub ikonę baz danych na pasku narzędzi.

    ![Lista baz danych Azure SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Na **baz danych SQL** wybierz istniejącej bazy danych SQL, który ma być używany jako baza danych Centrum synchronizacji danych. Zostanie otwarta strona bazy danych SQL.

    Baza danych Centrum jest centralny punkt końcowy topologia synchronizacji, w którym grupy synchronizacji ma wiele punktów końcowych bazy danych. Wszystkie inne bazy danych punkty końcowe w tym samym synchronizacji grupy - oznacza to, że wszystkie elementu członkowskiego bazy danych — synchronizacji z bazy danych Centrum.

4.  Na stronie bazy danych SQL dla wybranej bazy danych wybierz **synchronizacji do innych baz danych**. Zostanie otwarta strona synchronizacji danych.

    ![Synchronizowanie innych opcji bazy danych](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Utwórz nową grupę synchronizacji

1.  Na stronie synchronizacja danych wybierz **nowej grupy synchronizacji**. **Nowej grupy synchronizacji** z kroku 1, zostanie otwarta strona **Utwórz grupę synchronizacji**, zaznaczony. **Tworzenie grupy synchronizacji danych** zostanie otwarta strona również.

2.  Na **Tworzenie grupy synchronizacji danych** wykonaj następujące czynności:

    1.  W **nazwy grupy synchronizacji** wprowadź nazwę nowej grupy synchronizacji.

    2.  W **bazy danych usługi synchronizacji metadanych** sekcji wybierz, czy można utworzyć nową bazę danych (zalecane) lub użyj istniejącej bazy danych.

        > [!NOTE]
        > Firma Microsoft zaleca, aby utworzyć nową, pustą bazę danych do użycia jako bazy danych usługi synchronizacji metadanych. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenia. Ta baza danych jest automatycznie udostępniony jako bazy danych usługi synchronizacji metadanych dla wszystkich grup synchronizacji w wybranym regionie. Nie można zmienić bazy danych usługi synchronizacji metadanych lub jego nazwa bez porzuceniem jej.

        Jeśli została wybrana opcja **nową bazę danych**, wybierz pozycję **Utwórz nową bazę danych.** **Bazy danych SQL** zostanie otwarta strona. Na **bazy danych SQL** strony, nazwy i skonfiguruj nową bazę danych. Następnie wybierz przycisk **OK**.

        Jeśli została wybrana opcja **Użyj istniejącej bazy danych**, wybierz bazę danych z listy.

    3.  W **synchronizacji automatycznej** najpierw wybierz **na** lub **poza**.

        Jeśli została wybrana opcja **na**w **częstotliwości synchronizacji** , wprowadź liczbę z zakresu a następnie wybierz opcję sekundy, minuty, godziny lub dni.

        ![Określ częstotliwość synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  W **rozwiązywania konfliktów** wybierz "Centrum wins" lub "Elementu członkowskiego wins".

        "Usługa wins Centrum" oznacza, że, jeśli występuje konflikt, dane w bazie danych Centrum zastąpiony powodujące konflikt danych w bazie danych elementu członkowskiego. "Usługa wins elementu członkowskiego" oznacza, że, jeśli występuje konflikt, dane w bazie danych elementu członkowskiego zastąpiony powodujące konflikt danych w bazie danych koncentratora. 

        ![Określ, jak są rozwiązywane konflikty](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Wybierz **OK** i poczekaj, aż nową grupę synchronizacji, aby utworzyć i wdrożyć.

## <a name="step-2---add-sync-members"></a>Krok 2 — Dodawanie członków synchronizacji

Po utworzeniu i wdrożeniu, krok 2 nowej grupy synchronizacji **dodawać członków synchronizacji**, zostanie wyróżniona w **nowej grupy synchronizacji** strony.

W **bazy danych Centrum** wprowadź istniejących poświadczeń dla serwera bazy danych SQL, na którym znajduje się baza danych Centrum. Nie wprowadzaj *nowe* poświadczeń w tej sekcji.

![Koncentrator bazy danych został dodany do grupy synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Dodaj bazę danych Azure SQL

W **bazy danych elementów członkowskich** sekcji i opcjonalnie Dodaj do grupy synchronizacji bazy danych SQL Azure, wybierając **dodać bazy danych Azure**. **Konfigurowanie bazy danych Azure** zostanie otwarta strona.

Na **Konfigurowanie bazy danych Azure** wykonaj następujące czynności:

1.  W **nazwa elementu członkowskiego synchronizacji** Podaj nazwę nowego elementu synchronizacji. Ta nazwa różni się od nazwy bazy danych.

2.  W **subskrypcji** wybierz skojarzona subskrypcja platformy Azure na potrzeby rozliczeń.

3.  W **Azure SQL Server** wybierz istniejącego serwera baz danych SQL.

4.  W **bazy danych SQL Azure** wybierz istniejącą bazę danych SQL.

5.  W **kierunki synchronizacji** pola, wybierz opcję synchronizacji dwukierunkowe, do koncentratora lub z Centrum.

    ![Dodawanie nowego elementu członkowskiego synchronizacji bazy danych SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  W **Username** i **hasło** wprowadź istniejących poświadczeń dla serwera bazy danych SQL, na którym znajduje się bazy danych elementów członkowskich. Nie wprowadzaj *nowe* poświadczeń w tej sekcji.

7.  Wybierz **OK** i poczekaj, aż nowy element członkowski synchronizacji utworzyć i wdrożyć.

    ![Dodano nowy element członkowski synchronizacji bazy danych SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Dodaj lokalną bazą danych programu SQL Server

W **bazy danych elementów członkowskich** sekcji i opcjonalnie Dodaj lokalny serwer SQL do grupy synchronizacji, wybierając **Dodaj bazę danych z lokalnego**. **Konfigurowanie lokalnego** zostanie otwarta strona.

Na **Konfigurowanie lokalnego** wykonaj następujące czynności:

1.  Wybierz **wybierz bramę Agent synchronizacji**. **Agent synchronizacji wybierz** zostanie otwarta strona.

    ![Wybierz bramę agent synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Na **wybierz bramę Agent synchronizacji** wybierz, czy użyć istniejącego agenta lub utworzyć nowy agent.

    Jeśli została wybrana opcja **agentów istniejące**, wybierz z listy istniejącego agenta.

    Jeśli została wybrana opcja **Utwórz nowy agent**, wykonaj następujące czynności:

    1.  Pobierz oprogramowanie klienckie synchronizacji agenta z linku podanego i zainstaluj go na komputerze, na którym znajduje się serwer SQL.
 
        > [!IMPORTANT]
        > Należy otworzyć w zaporze, aby umożliwić agenta klienta komunikacji z serwerem wychodzący port TCP 1433.


    2.  Wprowadź nazwę dla agenta.

    3.  Wybierz **tworzenie i generowanie klucza**.

    4.  Klucz agenta należy skopiować do Schowka.
        
        ![Tworzenie nowego agenta synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Wybierz **OK** zamknąć **Agent synchronizacji wybierz** strony.

    6.  Na komputerze serwera SQL Znajdź i uruchom aplikację klienta synchronizacji agenta.

        ![Aplikacja kliencka agenta synchronizacji danych](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  W aplikacji agenta synchronizacji, wybierz **przesłać klucz agenta**. **Synchronizacji metadanych bazy danych konfiguracji** zostanie otwarte okno dialogowe.

    8.  W **synchronizacji metadanych bazy danych konfiguracji** okno dialogowe, Wklej klucz agenta skopiowany z portalu Azure. Udostępniają istniejących poświadczeń dla serwera bazy danych SQL Azure, na którym znajduje się baza danych metadanych. (Jeśli została utworzona nowa baza danych metadanych, ta baza danych jest na tym samym serwerze co baza danych Centrum). Wybierz **OK** i poczekaj, aż do zakończenia konfiguracji.

        ![Wprowadź poświadczenia agenta klucza i serwera](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Jeśli w tym momencie zostanie wyświetlony błąd zapory, należy utworzyć regułę zapory na platformie Azure, aby zezwolić na ruch przychodzący z komputera programu SQL Server. Regułę można utworzyć ręcznie w portalu, ale użytkownik może ułatwić ją utworzyć w programu SQL Server Management Studio (SSMS). W programie SSMS próby nawiązania połączenia z bazą danych Centrum na platformie Azure. Wpisz jej nazwę jako \<hub_database_name\>. database.windows.net. Aby skonfigurować regułę zapory platformy Azure, wykonaj czynności opisane w oknie dialogowym. Następnie wróć do aplikacji agenta klienta synchronizacji.

    9.  W aplikacji klienta synchronizacji agenta, kliknij przycisk **zarejestrować** zarejestrować bazy danych programu SQL Server z agentem. **Konfiguracji serwera SQL** zostanie otwarte okno dialogowe.

        ![Dodaj i skonfiguruj bazę danych programu SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. W **konfiguracji serwera SQL** oknie dialogowym Wybierz, czy nawiązać połączenie przy użyciu uwierzytelniania programu SQL Server lub uwierzytelniania systemu Windows. Jeśli wybrano opcję uwierzytelniania programu SQL Server, wprowadź istniejących poświadczeń. Podaj nazwę serwera SQL i nazwę bazy danych, które mają być synchronizowane. Wybierz **połączenie testowe** Aby przetestować ustawienia. Następnie wybierz pozycję **Zapisz**. Zarejestrowane bazy danych zostanie wyświetlony na liście.

        ![Baza danych SQL Server zostanie zarejestrowany](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Można teraz zamknąć aplikacji agenta klienta synchronizacji.

    12. W portalu na **Konfigurowanie lokalnego** wybierz pozycję **wybierz bazę danych.** **Wybierz bazę danych** zostanie otwarta strona.

    13. Na **wybierz bazę danych** strony w **nazwa elementu członkowskiego synchronizacji** Podaj nazwę nowego elementu synchronizacji. Ta nazwa różni się od nazwy bazy danych. Wybierz bazę danych z listy. W **kierunki synchronizacji** pola, wybierz opcję synchronizacji dwukierunkowe, do koncentratora lub z Centrum.

        ![Wybierz bazę danych na lokalnym](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Wybierz **OK** zamknąć **wybierz bazę danych** strony. Następnie wybierz **OK** zamknąć **Konfigurowanie lokalnego** strony i poczekaj, aż nowy element członkowski synchronizacji utworzyć i wdrożyć. Na koniec kliknij **OK** zamknąć **Wybierz członków synchronizacji** strony.

        ![W lokalnej bazie danych dodane do grupy synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Aby połączyć się synchronizacja danych SQL i agent lokalny, Dodaj nazwę użytkownika do roli `DataSync_Executor`. Synchronizacja danych tworzy tę rolę w wystąpieniu programu SQL Server.

## <a name="step-3---configure-sync-group"></a>Krok 3 — Konfigurowanie synchronizacji grupy

Po nowych członków grupy synchronizacji są tworzone i wdrażane, krok 3 **Konfigurowanie synchronizacji grupy**, zostanie wyróżniona w **nowej grupy synchronizacji** strony.

1.  Na **tabel** , wybierz bazę danych z listy członków grupy synchronizacji, a następnie wybierz **schematu odświeżania**.

2.  Z listy dostępnych tabel wybierz tabele, które mają być synchronizowane.

    ![Wybieranie tabel do zsynchronizowania](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Domyślnie wybrane są wszystkie kolumny w tabeli. Jeśli nie chcesz zsynchronizować wszystkie kolumny, wyłącz pole wyboru dla kolumn, które nie mają być synchronizowane. Pamiętaj pozostawić wybrana kolumna klucza podstawowego.

    ![Wybieranie pól do zsynchronizowania](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Na koniec wybierz **zapisać**.

## <a name="faq-about-setup-and-configuration"></a>Często zadawane pytania dotyczące instalacji i konfiguracji

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Częstotliwość synchronizacji danych można synchronizować dane? 
Minimalna częstotliwość wynosi co pięć minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Synchronizacja danych SQL pełni tworzenie i przydzielanie tabele?

Jeśli w tabelach schematu synchronizacji nie są już tworzone w docelowej bazie danych, synchronizacja danych SQL (wersja zapoznawcza) powoduje utworzenie z kolumnami, które zostały wybrane. Jednak to zachowanie nie powoduje schematu pełnej rozdzielczości, z następujących powodów:

-   Tylko wybrane kolumny są tworzone w tabeli docelowej. Jeśli niektóre kolumny w tabeli źródłowej nie są częścią grupy synchronizacji, nie są udostępnione te kolumny w tabeli docelowej.

-   Indeksy są tworzone tylko dla wybranych kolumn. Indeks tabeli źródła zawiera kolumny, które nie są częścią grupy synchronizacji, te indeksy nie są udostępnione w tabeli docelowej.

-   Indeksy w kolumnach typu XML nie są udostępnione.

-   Sprawdź, czy ograniczenia nie są udostępnione.

-   Istniejące wyzwalacze w tabelach źródła nie są udostępnione.

-   Widoki i procedury składowane nie są tworzone w docelowej bazie danych.

Z powodu tych ograniczeń zaleca się następujących czynności:
-   W środowiskach produkcyjnych udostępnić schematu pełnej wierności samodzielnie.
-   Dla wypróbowaniem usługi, funkcja automatycznego inicjowania obsługi synchronizacji danych SQL (wersja zapoznawcza) działa dobrze.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Dlaczego widzę tabel, które I nie może utworzyć?  
Synchronizacja danych tworzy tabele w bazie danych śledzenia zmian. Nie należy usuwać je lub synchronizacji danych przestanie działać.

### <a name="is-my-data-convergent-after-a-sync"></a>Moje dane jest zbieżne po synchronizacji?

Niekoniecznie. Grupy synchronizacji przy użyciu koncentratora i trzy partnerzy (A, B i C) synchronizacje są koncentratora A, Centrum b i Centrum c. Jeśli zmiany do bazy danych A *po* koncentratora do synchronizacji, zmiany nie zapisane bazy danych B i C bazy danych do czasu następnego zadania synchronizacji.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Jak uzyskać zmiany schematu do grupy synchronizacji?

Należy ręcznie wykonać zmiany schematu.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Jak wyeksportować i zaimportować bazę danych z opcją synchronizacji danych?
Po wyeksportowaniu bazy danych jako `.bacpac` pliku i zaimportować plik, aby utworzyć nową bazę danych, należy wykonać następujące czynności dwa na synchronizację danych do nowej bazy danych:
1.  Czyszczenie obiektów synchronizacji danych i tabel na **nową bazę danych** za pomocą [ten skrypt](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Ten skrypt powoduje usunięcie wszystkich wymaganych obiektów synchronizacji danych z bazy danych.
2.  Utwórz ponownie grupę synchronizacji z nową bazę danych. Jeśli stary grupy synchronizacji nie jest już potrzebny, usuń go.

## <a name="faq-about-the-client-agent"></a>Często zadawane pytania dotyczące agenta klienta

### <a name="why-do-i-need-a-client-agent"></a>Dlaczego muszę agenta klienta?

Usługa synchronizacji danych SQL (wersja zapoznawcza) komunikuje się z bazy danych programu SQL Server za pośrednictwem agenta klienta. Ta funkcja zabezpieczeń uniemożliwiają bezpośrednią komunikację z bazami danych za zaporą. Po usługę synchronizacji danych SQL (wersja zapoznawcza) komunikuje się z agentem, tak więc przy użyciu szyfrowane połączeń i token unikatowy lub *klucz agenta*. Bazy danych programu SQL Server uwierzytelnienia agenta przy użyciu klucza ciągu i agent połączenia. Ten projekt zapewnia wysoki poziom zabezpieczeń danych.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Ile wystąpienia lokalnego agenta interfejsu użytkownika można uruchomić?

Można uruchomić tylko jedno wystąpienie interfejsu użytkownika.

### <a name="how-can-i-change-my-service-account"></a>Jak zmienić konto usługi?

Po zainstalowaniu agenta klienta, jedynym sposobem, aby zmienić konto usługi jest Odinstaluj ją i zainstaluj nowego agenta klienta z nowego konta usługi.

### <a name="how-do-i-change-my-agent-key"></a>Jak zmienić mój klucz agenta?

Klucz agenta można tylko raz przez agenta. Nie można ponownie użyć, gdy należy usunąć, a następnie ponownie zainstaluj nowego agenta nie może być on używany przez wielu agentów. Jeśli musisz utworzyć nowy klucz dla istniejącego agenta, należy się upewnić, że ten sam klucz jest rejestrowana z agentem klienta i usługi synchronizacji danych SQL (wersja zapoznawcza).

### <a name="how-do-i-retire-a-client-agent"></a>Jak wycofanie agenta klienta?

Aby natychmiast unieważnia lub wycofanie agenta, ponownie wygenerować klucz jej w portalu, ale nie przesyłać go w Interfejsie użytkownika agenta. Trwa ponowne generowanie klucza unieważnia poprzedniego klucza niezależnie, jeśli agent odpowiedniego online lub offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak przenieść agenta klienta do innego komputera?

Jeśli chcesz uruchomić lokalnego agenta z innego komputera niż aktualnie znajduje się na, wykonaj następujące czynności:

1. Zainstaluj agenta na wybrany komputer.

2. Zaloguj się do portalu synchronizacji danych SQL (wersja zapoznawcza) i ponownie wygenerować klucz agenta dla nowego agenta.

3. Użyj nowego agenta interfejsu użytkownika, aby przesłać nowy klucz agenta.

4. Zaczekaj, aż agenta klienta pobiera listę lokalnych baz danych, które zostały wcześniej zarejestrowane.

5. Podaj poświadczenia bazy danych dla wszystkich baz danych zawierających jako niedostępny. Te bazy danych musi być dostępny z nowym komputerze, na którym jest zainstalowany agent.

## <a name="next-steps"></a>Kolejne kroki
Gratulacje. Utworzono grupę synchronizacji, która zawiera zarówno wystąpienie bazy danych SQL, jak i bazy danych programu SQL Server.

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)
-   [Monitor synchronizacji danych Azure SQL z analizy dzienników](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:
    -   [Synchronizacja między wiele baz danych Azure SQL przy użyciu programu PowerShell](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
