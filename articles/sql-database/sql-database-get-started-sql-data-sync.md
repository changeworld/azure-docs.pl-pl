---
title: Konfigurowanie synchronizacji danych
description: W tym samouczku pokazano, jak skonfigurować usługę Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268966"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Samouczek: Konfigurowanie synchronizacji danych SQL między bazą danych SQL Azure a lokalnym programem SQL Server

W tym samouczku dowiesz się, jak skonfigurować synchronizację danych SQL Azure, tworząc grupę synchronizacji zawierającą zarówno usługi Azure SQL Database, jak i wystąpienia programu SQL Server. Grupa synchronizacji jest skonfigurowana na zamówienie i synchronizuje się zgodnie z ustawionym harmonogramem.

Samouczek zakłada, że masz co najmniej pewne wcześniejsze doświadczenie z bazą danych SQL i programem SQL Server.

Aby uzyskać omówienie synchronizacji danych SQL, zobacz [Synchronizowanie danych w chmurze i lokalnych bazach danych za pomocą usługi Azure SQL Data Sync](sql-database-sync-data.md).

W przypadku przykładów programu PowerShell dotyczących konfigurowania synchronizacji danych SQL zobacz [Jak synchronizować między bazami danych SQL platformy Azure](scripts/sql-database-sync-data-between-sql-databases.md) lub [bazą danych SQL Azure i lokalną bazą danych programu SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="create-sync-group"></a>Tworzenie grupy synchronizacji

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć bazę danych SQL. Wyszukaj i wybierz **bazy danych SQL**.

    ![Wyszukiwanie baz danych SQL, witryna Microsoft Azure portal](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Wybierz bazę danych, której chcesz użyć jako centrowej bazy danych do synchronizacji danych.

    ![Wybierz z listy bazy danych SQL, witrynę Microsoft Azure portal](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > Baza danych centrum jest centralnym punktem końcowym topologii synchronizacji, w którym grupa synchronizacji ma wiele punktów końcowych bazy danych. Wszystkie inne bazy danych elementów członkowskich z punktami końcowymi w grupie synchronizacji, synchronizacji z bazy danych centrum.

1. W menu **bazy danych SQL** dla wybranej bazy danych wybierz polecenie **Synchronizuj z innymi bazami danych**.

    ![Synchronizacja z innymi bazami danych, bazą danych SQL, witryną Microsoft Azure](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. Na stronie **Synchronizuj z innymi bazami danych** wybierz pozycję **Nowa grupa synchronizacji**. Zostanie otwarta strona **Nowa grupa synchronizacji** z **wyróżnioną wyróżnioną grupą Tworzenie synchronizacji (krok 1).**

   ![Ustawienia kroku 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na stronie **Tworzenie grupy synchronizacji danych** zmień następujące ustawienia:

   | Ustawienie                        | Opis |
   | ------------------------------ | ------------------------------------------------- |
   | **Nazwa grupy synchronizacji** | Wprowadź nazwę nowej grupy synchronizacji. Ta nazwa różni się od nazwy samej bazy danych. |
   | **Baza danych metadanych synchronizacji** | Wybierz, aby utworzyć bazę danych (zalecane) lub użyć istniejącej bazy danych.<br/><br/>Jeśli wybierzesz **pozycję Nowa baza danych**, wybierz pozycję **Utwórz nową bazę danych.** Następnie na stronie **Baza danych SQL** nazwij i skonfiguruj nową bazę danych i wybierz przycisk **OK**.<br/><br/>Jeśli wybierzesz **użyj istniejącej bazy danych,** wybierz bazę danych z listy. |
   | **Automatyczna synchronizacja** | Wybierz **włącz** lub **wyłącz .**<br/><br/>Jeśli w sekcji **Częstotliwość synchronizacji** wybierzesz opcję **Włączone**, wprowadź liczbę i wybierz pozycję **Sekundy, Minuty,** **Godziny**lub **Dni.** **Seconds** |
   | **Konfliktów** | Wybierz **opcję Wygrana centrum** lub **Wygrana członka**.<br/><br/>**Wygrana koncentratora** oznacza, że w przypadku wystąpienia konfliktów dane w bazie danych centrum zastępują dane powodujące konflikt w bazie danych elementów członkowskich.<br/><br/>**Wygrana elementu członkowskiego** oznacza, gdy występują konflikty, dane w bazie danych elementów członkowskich zastępuje dane powodujące konflikt w bazie danych centrum. |

   > [!NOTE]
   > Firma Microsoft zaleca utworzenie nowej, pustej bazy danych do użycia jako **baza danych metadanych synchronizacji**. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenie. Ta baza danych jest udostępniana jako **baza danych metadanych synchronizacji** dla wszystkich grup synchronizacji w wybranym regionie i nie można zmienić bazy danych ani jej nazwy bez usunięcia wszystkich grup synchronizacji i agentów synchronizacji w regionie.

   Wybierz **przycisk OK** i poczekaj na utworzenie i wdrożenie grupy synchronizacji.

## <a name="add-sync-members"></a>Dodawanie członków synchronizacji

Po utworzeniu i wdrożeniu nowej grupy synchronizacji wyróżniona zostanie wyróżniona wyróżniona **wyróżniona** dodatkowa **grupa synchronizacji (krok 2).**

W sekcji **Baza danych centrum** wprowadź istniejące poświadczenia dla serwera bazy danych SQL, na którym znajduje się baza danych centrum. Nie wprowadzaj *nowych* poświadczeń w tej sekcji.

![Ustawienia kroku 2](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Aby dodać bazę danych SQL platformy Azure

W sekcji **Baza danych członków** opcjonalnie dodaj bazę danych SQL Azure do grupy synchronizacji, wybierając pozycję Dodaj **bazę danych SQL Azure**. Zostanie otwarta strona **Konfigurowanie bazy danych SQL platformy Azure.**

  ![Krok 2 - konfigurowanie bazy danych](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na stronie **Konfigurowanie bazy danych SQL platformy Azure** zmień następujące ustawienia:

  | Ustawienie                       | Opis |
  | ----------------------------- | ------------------------------------------------- |
  | **Nazwa członka synchronizacji** | Podaj nazwę nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od samej nazwy bazy danych. |
  | **Subskrypcja** | Wybierz skojarzoną subskrypcję platformy Azure na potrzeby rozliczeń. |
  | **Azure SQL Server** | Wybierz istniejący serwer bazy danych SQL. |
  | **Azure SQL Database** | Wybierz istniejącą bazę danych SQL. |
  | **Wskazówki dojazdu do synchronizacji** | Wybierz **opcję Synchronizacja dwukierunkowa**, **Do koncentratora**lub **Z centrum**. |
  | **Nazwa użytkownika** i **hasło** | Wprowadź istniejące poświadczenia dla serwera bazy danych SQL, na którym znajduje się baza danych elementów członkowskich. Nie wprowadzaj *nowych* poświadczeń w tej sekcji. |

  Wybierz **przycisk OK** i poczekaj na utworzenie i wdrożenie nowego elementu członkowskiego synchronizacji.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Aby dodać lokalną bazę danych programu SQL Server

W sekcji **Baza danych członków** opcjonalnie dodaj lokalny program SQL Server do grupy synchronizacji, wybierając pozycję Dodaj lokalną **bazę danych**. Zostanie otwarta strona **Konfigurowanie lokalnego,** na której można wykonać następujące czynności:

1. Wybierz **pozycję Wybierz bramę agenta synchronizacji**. Zostanie otwarta strona **Wybierz agenta synchronizacji.**

   ![Tworzenie agenta synchronizacji](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na stronie **Wybierz agenta synchronizacji** wybierz, czy ma być używany istniejący agent, czy agent.

   Jeśli wybierzesz **istniejący agent**, wybierz istniejącego agenta z listy.

   Jeśli wybierzesz **pozycję Utwórz nowego agenta,** wykonaj następujące czynności:

   1. Pobierz agenta synchronizacji danych z dostarczonego łącza i zainstaluj go na komputerze, na którym znajduje się program SQL Server. Agent można również pobrać bezpośrednio z [programu SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Musisz otworzyć wychodzący port TCP 1433 w zaporze, aby agent klienta mógł komunikować się z serwerem.

   1. Wprowadź nazwę agenta.

   1. Wybierz **pozycję Utwórz i wygeneruj klucz** i skopiuj klucz agenta do schowka.

   1. Wybierz **przycisk OK,** aby zamknąć stronę **Wybierz agenta synchronizacji.**

1. Na komputerze z programem SQL Server znajdź i uruchom aplikację Agent synchronizacji klienta.

   ![Aplikacja agenta klienta synchronizacji danych](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. W aplikacji agenta synchronizacji wybierz pozycję **Prześlij klucz agenta**. Zostanie otwarte okno dialogowe **Konfiguracja bazy danych metadanych synchronizacji.**

    1. W oknie dialogowym **Konfiguracja bazy danych metadanych synchronizacji** wklej klucz agenta skopiowany z witryny Azure portal. Podaj również istniejące poświadczenia dla serwera bazy danych SQL Azure, na którym znajduje się baza danych metadanych. (Jeśli utworzono bazę danych metadanych, ta baza danych znajduje się na tym samym serwerze co baza danych centrum).) Wybierz **przycisk OK** i poczekaj na zakończenie konfiguracji.

        ![Wprowadzanie klucza agenta i poświadczeń serwera](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Jeśli wystąpi błąd zapory, utwórz regułę zapory na platformie Azure, aby zezwolić na ruch przychodzący z komputera programu SQL Server. Regułę można utworzyć ręcznie w portalu lub w programie SQL Server Management Studio (SSMS). W systemie SSMS połącz się z bazą danych centrum na platformie Azure, wprowadzając jej nazwę jako <hub_database_name>.database.windows.net.

    1. Wybierz **pozycję Zarejestruj,** aby zarejestrować bazę danych programu SQL Server u agenta. Zostanie otwarte okno dialogowe **Konfiguracja programu SQL Server.**

        ![Dodawanie i konfigurowanie bazy danych programu SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. W oknie dialogowym **Konfiguracja programu SQL Server** wybierz opcję łączenia się przy użyciu uwierzytelniania programu SQL Server lub uwierzytelniania systemu Windows. Jeśli wybierzesz uwierzytelnianie programu SQL Server, wprowadź istniejące poświadczenia. Podaj nazwę programu SQL Server i nazwę bazy danych, którą chcesz zsynchronizować, i wybierz **opcję Testuj połączenie,** aby przetestować ustawienia. Następnie wybierz pozycję **Zapisz,** a zarejestrowana baza danych pojawi się na liście.

        ![Baza danych programu SQL Server jest teraz zarejestrowana](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zamknij aplikację Agent synchronizacji klienta.

1. W portalu na stronie **Konfigurowanie lokalnego** wybierz pozycję **Wybierz bazę danych**.

1. Na stronie **Wybierz bazę danych** w polu **Synchronizuj nazwę członka** podaj nazwę nowego członka synchronizacji. Ta nazwa różni się od nazwy samej bazy danych. Wybierz bazę danych z listy. W polu **Wskazówki dojazdu synchronizacji** wybierz pozycję **Synchronizacja dwukierunkowa**, **Do koncentratora**lub **Z centrum**.

    ![Wybierz lokalną bazę danych](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Wybierz **przycisk OK,** aby zamknąć stronę **Wybierz bazę danych.** Następnie wybierz **przycisk OK,** aby zamknąć stronę **Konfiguruj lokalnie** i poczekać na utworzenie i wdrożenie nowego elementu członkowskiego synchronizacji. Na koniec wybierz przycisk **OK,** aby zamknąć stronę **Wybierz członków synchronizacji.**

> [!NOTE]
> Aby połączyć się z synchronizacją danych SQL i agentem lokalnym, dodaj nazwę użytkownika do *roli DataSync_Executor*. Synchronizacja danych tworzy tę rolę w wystąpieniu programu SQL Server.

## <a name="configure-sync-group"></a>Konfigurowanie grupy synchronizacji

Po utworzeniu i wdrożeniu nowych członków grupy synchronizacji na stronie **Nowa grupa synchronizacji** wyróżniona zostanie **wyróżniona grupa Konfiguruj (krok 3).**

![Ustawienia kroku 3](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na stronie **Tabele** wybierz bazę danych z listy członków grupy synchronizacji i wybierz pozycję **Odśwież schemat**.

1. Z listy wybierz tabele, które chcesz zsynchronizować. Domyślnie zaznaczone są wszystkie kolumny, więc wyłącz pole wyboru dla kolumn, których nie chcesz synchronizować. Pamiętaj, aby pozostawić zaznaczoną kolumnę klucza podstawowego.

1. Wybierz **pozycję Zapisz**.

1. Domyślnie bazy danych nie są synchronizowane do zaplanowanego lub uruchomionego ręcznie. Aby uruchomić synchronizację ręczną, przejdź do bazy danych SQL w witrynie Azure Portal, wybierz **pozycję Synchronizuj z innymi bazami danych**i wybierz grupę synchronizacji. Zostanie otwarta strona **Synchronizacja danych.** Wybierz pozycję **Synchronizuj**.

    ![Synchronizacja ręczna](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Najczęściej zadawane pytania

**Jak często synchronizacja danych może synchronizować moje dane?**

Minimalny czas trwania między synchronizacjami wynosi pięć minut.

**Czy synchronizacja danych SQL w pełni tworzy tabele?**

Jeśli w docelowej bazie danych brakuje tabel schematu synchronizacji, usługa SQL Data Sync tworzy je z wybranymi kolumnami. Nie powoduje to jednak współuprawienia schematu z następujących powodów:

- W tabeli docelowej są tworzone tylko wybrane kolumny. Niewybrane kolumny są ignorowane.
- W tabeli docelowej są tworzone tylko wybrane indeksy kolumn. Indeksy niewybranych kolumn są ignorowane.
- Indeksy w kolumnach typu XML nie są tworzone.
- Ograniczenia CHECK nie są tworzone.
- Wyzwalacze w tabelach źródłowych nie są tworzone.
- Widoki i procedury przechowywane nie są tworzone.

Ze względu na te ograniczenia zalecamy następujące rozwiązania:

- W środowiskach produkcyjnych utwórz schemat pełnej wierności samodzielnie.
- Podczas eksperymentowania z usługą należy użyć funkcji automatycznego inicjowania obsługi administracyjnej.

**Dlaczego widzę tabele, które nie zostały utworzone?**

Synchronizacja danych tworzy dodatkowe tabele w bazie danych do śledzenia zmian. Nie usuwaj tych danych lub synchronizacja danych przestaje działać.

**Czy moje dane są zbieżne po synchronizacji?**

Niekoniecznie. Weź grupę synchronizacji z koncentratorem i trzema szprychami (A, B i C), gdzie synchronizacja to Koncentrator do A, Koncentrator do B i Koncentrator do C. Jeśli po zakończeniu synchronizacji z koncentratorem na A zostanie wniesienie zmiany do bazy danych *A,* zmiana ta nie zostanie zapisana w bazie danych B lub c bazy danych do następnego zadania synchronizacji.

**Jak wprowadzić zmiany schematu w grupę synchronizacji?**

Ręczne wprowadzanie i propagowanie wszystkich zmian schematu.

1. Replikuj zmiany schematu ręcznie do centrum i do wszystkich elementów członkowskich synchronizacji.
1. Zaktualizuj schemat synchronizacji.

W przypadku dodawania nowych tabel i kolumn:

Nowe tabele i kolumny nie mają wpływu na bieżącą synchronizację, a synchronizacja danych ignoruje je, dopóki nie są dodawane do schematu synchronizacji. Podczas dodawania nowych obiektów bazy danych należy postępować zgodnie z kolejnością:

1. Dodaj nowe tabele lub kolumny do centrum i do wszystkich członków synchronizacji.
1. Dodaj nowe tabele lub kolumny do schematu synchronizacji.
1. Rozpocznij wstawianie wartości do nowych tabel i kolumn.

Aby zmienić typ danych kolumny:

Po zmianie typu danych istniejącej kolumny synchronizacja danych będzie działać tak długo, jak długo nowe wartości pasują do oryginalnego typu danych zdefiniowanego w schemacie synchronizacji. Na przykład jeśli zmienisz typ w źródłowej bazie danych z **int na** **bigint,** synchronizacja danych będzie nadal działać, dopóki nie wstawisz wartości zbyt dużej dla typu danych **int.** Aby zakończyć zmianę, należy ręcznie replikować zmianę schematu do centrum i do wszystkich członków synchronizacji, a następnie zaktualizować schemat synchronizacji.

**Jak wyeksportować i zaimportować bazę danych za pomocą synchronizacji danych?**

Po wyeksportowaniu bazy danych jako pliku *bacpac* i zaimportowaniu pliku w celu utworzenia bazy danych wykonaj następujące czynności, aby użyć synchronizacji danych w nowej bazie danych:

1. Wyczyść obiekty synchronizacji danych i dodatkowe tabele w nowej bazie danych za pomocą [tego skryptu](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skrypt usuwa wszystkie wymagane obiekty synchronizacji danych z bazy danych.
1. Ponownie utworzyć grupę synchronizacji z nową bazą danych. Jeśli nie potrzebujesz już starej grupy synchronizacji, usuń ją.

**Gdzie mogę znaleźć informacje na temat agenta klienta?**

Aby uzyskać często zadawane pytania dotyczące agenta klienta, zobacz [Często zadawane pytania dotyczące agenta](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Następne kroki

Gratulacje. Utworzono grupę synchronizacji, która zawiera zarówno wystąpienie bazy danych SQL, jak i bazę danych programu SQL Server.

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

- [Agent synchronizacji danych dla synchronizacji danych SQL usługi Azure](sql-database-data-sync-agent.md)
- [Najważniejsze wskazówki](sql-database-best-practices-data-sync.md) i [jak rozwiązywać problemy z synchronizacją danych SQL usługi Azure](sql-database-troubleshoot-data-sync.md)
- [Monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
- [Aktualizowanie schematu synchronizacji za pomocą programu Transact-SQL](sql-database-update-sync-schema.md) lub [programu PowerShell](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
