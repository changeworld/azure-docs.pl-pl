---
title: Konfigurowanie usługi Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób konfigurowania usługi Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 04/10/2018
ms.openlocfilehash: f1d439d043feb36fba0cc6c9c9d1b5569a4d8182
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166779"
---
# <a name="set-up-sql-data-sync"></a>Skonfiguruj SQL Data Sync
W tym samouczku dowiesz się, jak skonfigurować usługi Azure SQL Data Sync, tworząc grupę synchronizacji hybrydowej, która zawiera wystąpienia usługi Azure SQL Database i programu SQL Server. Nowa grupa synchronizacji jest w pełni skonfigurowane i synchronizuje się zgodnie z harmonogramem, które można ustawić.

Ten samouczek zakłada, że masz co najmniej pewne doświadczenie z usługą SQL Database i programu SQL Server. 

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

Aby uzyskać kompletny przykład programu PowerShell, które pokazują, jak skonfigurować SQL Data Sync, zobacz następujące artykuły:
-   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Krok 1 — Tworzenie grupy synchronizacji

### <a name="locate-the-data-sync-settings"></a>Znajdź ustawienia synchronizacji danych

1.  W przeglądarce przejdź do witryny Azure portal.

2.  W portalu Znajdź bazy danych SQL z pulpitu nawigacyjnego lub ikonę bazy danych SQL, na pasku narzędzi.

    ![Lista baz danych Azure SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Na **baz danych SQL** wybierz istniejącej bazy danych SQL, którą chcesz użyć jako baza danych koncentratora, w celu synchronizacji danych. Zostanie otwarta strona bazy danych SQL.

    Baza danych koncentratora to centralny punkt końcowy topologia synchronizacji, w którym znajduje się wiele punktów końcowych bazy danych grupy synchronizacji. Wszystkie inne bazy danych punkty końcowe w tym samym synchronizacji grupy — czyli wszystkich elementu członkowskiego baz danych — synchronizacji z bazy danych Centrum.

4.  Na stronie bazy danych SQL dla wybranej bazy danych, wybierz **synchronizacji z innymi bazami danych**. Zostanie otwarta strona synchronizacji danych.

    ![Synchronizuj z innych opcji bazy danych](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Utwórz nową grupę synchronizacji

1.  Na stronie synchronizacja danych wybierz **Nowa grupa synchronizacji**. **Nowa grupa synchronizacji** z kroku 1. zostanie otwarta strona **Tworzenie grupy synchronizacji**, wyróżnione. **Tworzenie grupy synchronizacji danych** zostanie otwarta strona również.

2.  Na **Tworzenie grupy synchronizacji danych** wykonaj następujące czynności:

    1.  W **Nazwa grupy synchronizacji** wprowadź nazwę nowej grupy synchronizacji.

    2.  W **bazy danych metadanych synchronizacji** pozycję Wybierz, czy do tworzenia nowej bazy danych (zalecane) lub użyj istniejącej bazy danych.

        > [!NOTE]
        > Firma Microsoft zaleca, aby utworzyć nową, pustą bazę danych do użycia jako baza metadanych synchronizacji. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenia. Ta baza danych jest automatycznie udostępniany jako baza danych metadanych synchronizacji dla wszystkich grup synchronizacji w wybranym regionie. Nie można zmienić bazy danych metadanych synchronizacji lub jego nazwę bez usuwania go.

        Jeśli została wybrana opcja **nową bazę danych**, wybierz opcję **Utwórz nową bazę danych.** **Bazy danych SQL** zostanie otwarta strona. Na **bazy danych SQL** strony, nazwać i skonfigurować nową bazę danych. Następnie wybierz przycisk **OK**.

        Jeśli została wybrana opcja **Użyj istniejącej bazy danych**, wybierz bazę danych z listy.

    3.  W **automatyczna synchronizacja** sekcji, najpierw wybierz **na** lub **poza**.

        Jeśli została wybrana opcja **na**w **częstotliwość synchronizacji** , wprowadź liczbę i wybierz pozycję sekundy, minuty, godziny lub dni.

        ![Określ częstotliwość synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  W **Rozwiązywanie konfliktów** sekcji, wybierz pozycję "Centrum usługi wins" lub "Elementu członkowskiego wins."

        "Usługa wins Centrum" oznacza, że, gdy wystąpi konflikt, dane w bazie danych Centrum zastąpienie powodujące konflikt danych w bazie danych elementu członkowskiego. "Usługa wins elementu członkowskiego" oznacza, że, gdy wystąpi konflikt, dane w bazie danych elementu członkowskiego powoduje zastąpienie powodujące konflikt danych w bazie danych Centrum. 

        ![Określ, jak są rozwiązywane konflikty](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Wybierz **OK** i poczekaj na Nowa grupa synchronizacji może zostać utworzona i wdrożona.

## <a name="step-2---add-sync-members"></a>Krok 2 — Dodawanie elementów członkowskich synchronizacji

Po utworzeniu i wdrożeniu, krok 2. Nowa grupa synchronizacji **Dodawanie elementów członkowskich synchronizacji**, jest wyróżniona w **Nowa grupa synchronizacji** strony.

W **baza danych koncentratora** sekcji, wprowadź poświadczenia istniejącego serwera bazy danych SQL, na którym znajduje się baza danych koncentratora. Nie wprowadzaj *nowe* poświadczenia w tej sekcji.

![Baza danych koncentratora został dodany do grupy synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Dodaj bazę danych Azure SQL Database

W **bazy danych elementów członkowskich** sekcji i opcjonalnie dodać do grupy synchronizacji usługi Azure SQL Database, wybierając **Dodawanie bazy danych Azure**. **Konfigurowanie bazy danych Azure** zostanie otwarta strona.

Na **Konfigurowanie bazy danych Azure** wykonaj następujące czynności:

1.  W **nazwa elementu członkowskiego synchronizacji** , podaj nazwę dla nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od nazwy bazy danych.

2.  W **subskrypcji** wybierz skojarzonej subskrypcji platformy Azure na potrzeby rozliczeń.

3.  W **serwera SQL Azure** wybierz istniejący serwer bazy danych SQL.

4.  W **usługi Azure SQL Database** wybierz istniejącej bazy danych SQL.

5.  W **kierunki synchronizacji** pola, wybierz opcję Synchronizacja dwukierunkowa, do koncentratora lub z Centrum.

    ![Dodawanie nowego elementu członkowskiego synchronizacji bazy danych SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  W **Username** i **hasło** Wprowadź istniejące poświadczenia dla serwera bazy danych SQL, na którym znajduje się bazy danych elementów członkowskich. Nie wprowadzaj *nowe* poświadczenia w tej sekcji.

7.  Wybierz **OK** i poczekaj na nowego elementu członkowskiego synchronizacji, może zostać utworzona i wdrożona.

    ![Dodano nowy element członkowski synchronizacji bazy danych SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Dodawanie bazy danych programu SQL Server w środowisku lokalnym

W **bazy danych elementów członkowskich** sekcji i opcjonalnie Dodaj na lokalnym serwerze SQL Server do grupy synchronizacji, wybierając **Dodawanie lokalnej bazy danych**. **Konfigurować lokalnego** zostanie otwarta strona.

Na **konfigurować lokalnego** wykonaj następujące czynności:

1.  Wybierz **Wybieranie bramy agenta synchronizacji**. **Wybieranie agenta synchronizacji** zostanie otwarta strona.

    ![Wybieranie bramy agenta synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Na **Wybieranie bramy agenta synchronizacji** wybierz opcję użycia istniejącego agenta lub Utwórz nowego agenta.

    Jeśli została wybrana opcja **agentów istniejące**, z listy wybierz istniejącego agenta.

    Jeśli została wybrana opcja **Utwórz nowego agenta**, wykonaj następujące czynności:

    1.  Pobierz oprogramowanie agenta synchronizacji klienta z linku podanego i zainstaluj go na komputerze, na którym znajduje się serwer SQL.
 
        > [!IMPORTANT]
        > Musisz otworzyć wychodzącego portu TCP 1433 w zaporze aby umożliwić agenta klienta komunikacji z serwerem.


    2.  Wprowadź nazwę dla agenta.

    3.  Wybierz **Utwórz i wygeneruj klucz**.

    4.  Klucz agenta należy skopiować do Schowka.
        
        ![Tworzenie nowego agenta synchronizacji.](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Wybierz **OK** zamknąć **Wybieranie agenta synchronizacji** strony.

    6.  Na komputerze programu SQL Server Znajdź i uruchom aplikację klienta agenta synchronizacji.

        ![Dane synchronizacji aplikacji agenta klienta](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  W aplikacji agenta synchronizacji, wybierz **przesłać klucz agenta**. **Konfiguracji bazy danych metadanych synchronizacji** zostanie otwarte okno dialogowe.

    8.  W **konfiguracji bazy danych metadanych synchronizacji** okno dialogowe, Wklej klucz agenta skopiowane z portalu Azure. Udostępniają istniejących poświadczeń dla serwera usługi Azure SQL Database, na którym znajduje się baza danych metadanych. (Jeśli utworzono nową bazę danych metadanych, ta baza danych jest na tym samym serwerze co baza danych koncentratora). Wybierz **OK** i poczekaj na zakończenie konfiguracji.

        ![Wprowadź poświadczenia klucza i serwera agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Jeśli na tym etapie wystąpi błąd zapory, musisz utworzyć regułę zapory na platformie Azure, aby zezwolić na ruch przychodzący z komputerem programu SQL Server. Regułę można utworzyć ręcznie w portalu, ale może okazać się łatwiejsze do jego utworzenia w SQL Server Management Studio (SSMS). W programie SSMS spróbuj połączyć się z bazy danych Centrum na platformie Azure. Wprowadź jego nazwę jako < hub_database_name >. database.windows.net. Aby skonfigurować regułę zapory platformy Azure, postępuj zgodnie z instrukcjami w oknie dialogowym. Następnie wróć do aplikacji klienta agenta synchronizacji.

    9.  W aplikacji agenta synchronizacji klienta, kliknij przycisk **zarejestrować** zarejestrować bazę danych programu SQL Server za pośrednictwem agenta. **Konfiguracja programu SQL Server** zostanie otwarte okno dialogowe.

        ![Dodawanie i Konfigurowanie bazy danych programu SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. W **Konfiguracja programu SQL Server** okno dialogowe, wybierz, czy nawiązać połączenie przy użyciu uwierzytelniania programu SQL Server lub Windows. Jeśli wybrano opcję uwierzytelniania programu SQL Server, wprowadź istniejących poświadczeń. Podaj nazwę programu SQL Server i nazwę bazy danych, które mają być synchronizowane. Wybierz **Testuj połączenie** Aby przetestować ustawienia. Następnie wybierz pozycję **Zapisz**. Zarejestrowane baza danych jest wyświetlana na liście.

        ![Bazy danych programu SQL Server jest obecnie zarejestrowany.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Możesz teraz zamknąć aplikację agenta synchronizacji klienta.

    12. W portalu na **konfigurować lokalnego** wybierz opcję **wybierz bazę danych.** **Wybierz bazę danych** zostanie otwarta strona.

    13. Na **wybierz bazę danych** stronie **nazwa elementu członkowskiego synchronizacji** , podaj nazwę dla nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od nazwy bazy danych. Wybierz bazę danych z listy. W **kierunki synchronizacji** pola, wybierz opcję Synchronizacja dwukierunkowa, do koncentratora lub z Centrum.

        ![Wybierz bazę danych na lokalnym](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Wybierz **OK** zamknąć **wybierz bazę danych** strony. Następnie wybierz pozycję **OK** zamknąć **konfigurować lokalnego** strony, a następnie poczekaj, aż nowego elementu członkowskiego synchronizacji, może zostać utworzona i wdrożona. Na koniec kliknij **OK** zamknąć **Wybieranie elementów członkowskich synchronizacji** strony.

        ![W lokalnej bazie danych dodane do grupy synchronizacji](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Nawiązać połączenia SQL Data Sync i agent lokalny, Dodaj swoją nazwę użytkownika do roli `DataSync_Executor`. Synchronizacja danych tworzy tę rolę w wystąpieniu programu SQL Server.

## <a name="step-3---configure-sync-group"></a>Krok 3 — Konfigurowanie grupy synchronizacji

Po nowych elementów członkowskich grupy synchronizacji są tworzone i wdrażane, krok 3 **Konfigurowanie grupy synchronizacji**, jest wyróżniona w **Nowa grupa synchronizacji** strony.

1.  Na **tabel** strony, wybierz bazę danych z listy elementów członkowskich grupy synchronizacji, a następnie wybierz pozycję **odświeżania schematu**.

2.  Z listy dostępnych tabel wybierz tabele, które mają być synchronizowane.

    ![Wybieranie tabel do zsynchronizowania](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Domyślnie są zaznaczone wszystkie kolumny w tabeli. Jeśli nie chcesz zsynchronizować wszystkie kolumny, należy wyłączyć pole wyboru dla kolumn, które nie mają być synchronizowane. Pamiętaj pozostawić zaznaczone kolumny klucza podstawowego.

    ![Wybieranie pól do zsynchronizowania](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Na koniec wybierz pozycję **Zapisz**.

## <a name="faq-about-setup-and-configuration"></a>Często zadawane pytania dotyczące instalacji i konfiguracji

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Częstotliwość synchronizacji danych można synchronizować dane? 
Minimalna częstotliwość to co pięć minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Czy SQL Data Sync w pełni tworzenie i aprowizowanie tabele?

Jeśli tabele schematu synchronizacji nie są już tworzone w docelowej bazie danych, SQL Data Sync tworzy je z kolumnami, które zostały wybrane. Jednak to zachowanie nie powoduje schematu pełnej rozdzielczości, z następujących powodów:

-   Tylko wybrane kolumny są tworzone w tabeli docelowej. Jeśli niektóre kolumny w tabelach źródłowych nie są częścią grupy synchronizacji, te kolumny nie są udostępnione w tabelach docelowych.

-   Indeksy są tworzone tylko dla wybranych kolumn. Jeśli źródło tabeli indeksu zawiera kolumny, które nie należą do grupy synchronizacji, te indeksy nie są udostępnione w tabelach docelowych.

-   Indeksy w kolumnach typu XML nie są udostępnione.

-   Sprawdź, czy ograniczenia nie są udostępnione.

-   Istniejące wyzwalacze w tabelach źródłowych nie są udostępnione.

-   Widoki i składowane nie są tworzone w docelowej bazie danych.

Ze względu na te ograniczenia zaleca się następujące elementy:
-   W środowiskach produkcyjnych aprowizować schematu pełnej wierności samodzielnie.
-   Wypróbowanie usługi, funkcja automatycznego aprowizowania, SQL Data Sync działa dobrze.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Dlaczego są wyświetlane tabele, które nie zostały utworzone I?  
Synchronizacja danych tworzy te tabele w bazie danych śledzenia zmian. Nie należy usuwać je lub synchronizacja danych zostanie zatrzymane.

### <a name="is-my-data-convergent-after-a-sync"></a>Czy moje dane zbieżne po synchronizacji?

Niekoniecznie. W grupie synchronizacji przy użyciu koncentratora i trzy szprychy, (A, B i C) synchronizacje są Centrum a, Centrum b i Centrum C. W przypadku wprowadzania zmian do bazy danych A *po* koncentratora do synchronizacji, które zmiany są one zapisywane do bazy danych B lub C bazy danych do czasu następnego zadania synchronizacji.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Jak uzyskać zmian schematu w ramach grupy synchronizacji?

Musisz wprowadzić zmiany i ich propagowanie wszystkich schematu ręcznie.
1. Do koncentratora i do wszystkich elementów członkowskich synchronizacji ręcznie replikować zmiany schematu.
2. Aktualizowanie schematu synchronizacji.

**Dodawanie nowych tabel i kolumn**. Nowe tabele i kolumny nie miały wpływu na bieżące synchronizacji. Synchronizacja danych ignoruje nowe tabele i kolumny, do momentu dodania do schematu synchronizacji. Po dodaniu nowych obiektów bazy danych, jest to najlepsze sekwencji do wykonania:
1. Dodaj nowe tabele lub kolumny do koncentratora i do wszystkich elementów członkowskich synchronizacji.
2. Dodaj nowe tabele lub kolumny do schematu synchronizacji.
3. Rozpocznij wstawiania wartości do nowych tabel i kolumn.

**Zmiana typu danych kolumny**. Po zmianie typu danych kolumny istniejącej Data Sync w dalszym ciągu działać tak długo, jak oryginalny typ danych zdefiniowany w schemacie synchronizacji mieści się nowymi wartościami miar. Na przykład, jeśli zmienisz typ do źródłowej bazy danych z **int** do **bigint**, Data Sync w dalszym ciągu działać do momentu Wstaw wartość, która jest zbyt duży dla **int** — typ danych . Aby ukończyć zmianę, ręcznie replikować zmiany schematu, do Centrum, jak i do wszystkich elementów członkowskich synchronizacji, a następnie zaktualizować schematu synchronizacji.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Jak eksportować i importować bazę danych z opcją synchronizacji danych?
Po wyeksportowaniu bazę danych jako `.bacpac` pliku i zaimportować plik, aby utworzyć nową bazę danych, należy wykonać następujące dwie rzeczy korzystać z synchronizacji danych do nowej bazy danych:
1.  Czyszczenie obiektów synchronizacji danych i tabel na **nową bazę danych** przy użyciu [ten skrypt](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Ten skrypt powoduje usunięcie wszystkich wymaganych obiektów synchronizacji danych z bazy danych.
2.  Utwórz ponownie grupę synchronizacji przy użyciu nowej bazy danych. Jeśli nie potrzebujesz już starej grupy synchronizacji, należy go usunąć.

## <a name="faq-about-the-client-agent"></a>Często zadawane pytania dotyczące agenta klienta

### <a name="why-do-i-need-a-client-agent"></a>Dlaczego należy to agent klienta?

Usługa SQL Data Sync komunikuje się z bazy danych SQL Server za pośrednictwem agenta klienta. Ta funkcja zabezpieczeń uniemożliwiają bezpośrednią komunikację z bazami danych za zaporą. Gdy usługa SQL Data Sync komunikuje się z agenta, robi to przy użyciu zaszyfrowane połączenia i unikatowy token lub *klucz agenta*. Bazy danych programu SQL Server uwierzytelnienia agenta przy użyciu klucza ciągu i agent połączenia. Ten projekt zapewnia wysoki poziom zabezpieczeń dla Twoich danych.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Ile wystąpień agent lokalny można uruchomić interfejs użytkownika?

Można uruchomić tylko jedno wystąpienie interfejsu użytkownika.

### <a name="how-can-i-change-my-service-account"></a>Jak zmienić konto usługi?

Po zainstalowaniu agenta klienta, jedynym sposobem, aby zmienić konto usługi jest odinstalowanie go i zainstaluj nowego agenta klienta przy użyciu nowego konta usługi.

### <a name="how-do-i-change-my-agent-key"></a>Jak zmienić mój klucz agenta?

Klucz agenta można używać tylko raz przez agenta. Nie można użyć ponownie, gdy należy usunąć, a następnie ponownie zainstaluj nowego agenta ani nie może on być używany przez wielu agentów. Jeśli potrzebujesz utworzyć nowy klucz dla istniejącego agenta, należy się upewnić, że ten sam klucz jest rejestrowane za pomocą agenta klienta i w usłudze SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Jak wycofać to agent klienta?

Aby natychmiast unieważnia lub wycofanie agenta, Wygeneruj ponownie klucz w portalu, ale nie przesyłać go w Interfejsie użytkownika agentów. Trwa ponowne generowanie klucza unieważnia poprzedni klucz niezależnie, jeśli agent odpowiedniego online lub offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak przenieść agenta klienta do innego komputera?

Jeśli chcesz uruchomić lokalnego agenta za pomocą innego komputera niż aktualnie na, wykonaj następujące czynności:

1. Zainstaluj agenta na wybrany komputer.

2. Zaloguj się do portalu usługi SQL Data Sync, a następnie ponownie wygenerować klucz agenta dla nowego agenta.

3. Korzystanie z interfejsu użytkownika nowy agent, aby przesłać nowy klucz agenta.

4. Zaczekaj, aż agent klienta pobierze listę lokalnych baz danych, które zostały wcześniej zarejestrowane.

5. Podaj poświadczenia bazy danych dla wszystkich baz danych, które wyświetlają jako niedostępny. Te bazy danych musi być dostępny z nowego komputera, na którym jest zainstalowany agent.

## <a name="next-steps"></a>Kolejne kroki
Gratulacje. Utworzono grupę synchronizacji, która obejmuje zarówno w wystąpieniu bazy danych SQL, jak i bazy danych programu SQL Server.

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
