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
ms.reviewer: carlrab
manager: craigg
ms.date: 01/14/2019
ms.openlocfilehash: 82b85ffd685df52e702db15e5a5b57a53a3b4f64
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486006"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Samouczek: Konfigurowanie synchronizacji danych programu SQL między usługi Azure SQL Database i programu SQL Server w środowisku lokalnym

W tym samouczku dowiesz się, jak skonfigurować usługi Azure SQL Data Sync, tworząc grupę synchronizacji, która zawiera wystąpienia usługi Azure SQL Database i programu SQL Server. Grupy synchronizacji jest niestandardowe, skonfigurowane i synchronizuje się zgodnie z harmonogramem, które można ustawić.

W samouczku przyjęto założenie, że masz co najmniej pewne doświadczenie z bazą danych SQL i programu SQL Server.

Aby zapoznać się z omówieniem SQL Data Sync, zobacz [synchronizowanie danych w bazach danych w chmurze i lokalnych przy użyciu usługi Azure SQL Data Sync](sql-database-sync-data.md).

Przykłady programu PowerShell dotyczące sposobu konfigurowania SQL Data Sync, zobacz [synchronizacji między bazami danych Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md) lub [usługi Azure SQL Database i lokalną bazą danych programu SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="create-sync-group"></a>Tworzenie grupy synchronizacji

1. W przeglądarce przejdź do witryny Azure portal. Znajdź bazy danych SQL z pulpitu nawigacyjnego lub, wybierz **baz danych SQL** ikonę na pasku narzędzi i na **baz danych SQL** wybierz bazę danych, którą chcesz użyć jako baza danych koncentratora, w celu synchronizacji danych.

    > [!NOTE]
    > Centrum bazy danych centralnej punktu końcowego topologią synchronizacji, w którym znajduje się wiele punktów końcowych bazy danych grupy synchronizacji. Synchronizuj wszystkie inne Członkowskie bazy danych z punktami końcowymi w grupie synchronizacji z bazy danych Centrum.

1. Na **bazy danych SQL** strony dla wybranej bazy danych, wybierz opcję **synchronizacji z innymi bazami danych**.

    ![Synchronizuj z innych opcji bazy danych](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. Na **synchronizacji z innymi bazami danych** wybierz opcję **Nowa grupa synchronizacji**. **Nowa grupa synchronizacji** zostanie otwarta strona z **Tworzenie grupy synchronizacji (krok 1)** wyróżnione.

   ![Ustawienia w kroku 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na **Tworzenie grupy synchronizacji danych** strony, zmienić następujące ustawienia:

   | Ustawienie                        | Opis |
   | ------------------------------ | ------------------------------------------------- |
   | **Nazwa grupy synchronizacji** | Wprowadź nazwę dla nowej grupy synchronizacji. Ta nazwa różni się od nazwy bazy danych. |
   | **Baza danych metadanych synchronizacji** | Wybierz, aby utworzyć bazę danych (zalecane) lub użyj istniejącej bazy danych.<br/><br/>Jeśli wybierzesz **nową bazę danych**, wybierz opcję **Utwórz nową bazę danych.** Następnie na **bazy danych SQL** strony, nazwę i skonfiguruj nową bazę danych i wybierz **OK**.<br/><br/>Jeśli wybierzesz **Użyj istniejącej bazy danych**, wybierz bazę danych z listy. |
   | **Automatyczna synchronizacja** | Wybierz **na** lub **poza**.<br/><br/>Jeśli wybierzesz **na**, wprowadź liczbę i wybierz **sekund**, **minut**, **godzin**, lub **dni** w **Częstotliwość synchronizacji** sekcji. |
   | **Rozwiązywanie konfliktów** | Wybierz **okno koncentratora** lub **okno elementu członkowskiego**.<br/><br/>**Okno koncentratora** oznacza, że gdy występują konflikty, dane z bazy danych Centrum zastąpienie powodujące konflikt danych w bazie danych elementu członkowskiego.<br/><br/>**Okno elementu członkowskiego** oznacza, że gdy występują konflikty, dane w bazie danych elementu członkowskiego powoduje zastąpienie powodujące konflikt danych w bazie danych Centrum. |

   > [!NOTE]
   > Firma Microsoft zaleca, aby utworzyć nową, pustą bazę danych do użycia jako **bazy danych metadanych synchronizacji**. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenia. Ta baza danych jest udostępniany jako **bazy danych metadanych synchronizacji** dla wszystkich grup synchronizacji w wybranym regionie i nie można zmienić bazy danych lub jego nazwę bez usuwania wszystkich grup synchronizacji i agenci synchronizacji w regionie.

   Wybierz **OK** i poczekaj na grupy synchronizacji, może zostać utworzona i wdrożona.

## <a name="add-sync-members"></a>Dodawanie elementów członkowskich synchronizacji

Po utworzeniu i wdrożeniu, Nowa grupa synchronizacji **Dodawanie elementów członkowskich synchronizacji (krok 2)** jest wyróżniona na **Nowa grupa synchronizacji** strony.

W **baza danych koncentratora** sekcji, wprowadź poświadczenia istniejącego serwera bazy danych SQL, na którym znajduje się baza danych koncentratora. Nie wprowadzaj *nowe* poświadczenia w tej sekcji.

![Krok 2 ustawienia](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Aby dodać bazy danych SQL Azure

W **bazy danych elementów członkowskich** sekcji i opcjonalnie dodać do grupy synchronizacji usługi Azure SQL Database, wybierając **Dodawanie usługi Azure SQL Database**. **Konfigurowanie usługi Azure SQL Database** zostanie otwarta strona.

  ![Krok 2 — Konfigurowanie bazy danych](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na **Konfigurowanie usługi Azure SQL Database** strony, zmienić następujące ustawienia:

  | Ustawienie                       | Opis |
  | ----------------------------- | ------------------------------------------------- |
  | **Nazwa elementu członkowskiego synchronizacji** | Podaj nazwę dla nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od sama nazwa bazy danych. |
  | **Subskrypcja** | Wybierz skojarzonej subskrypcji platformy Azure na potrzeby rozliczeń. |
  | **Azure SQL Server** | Wybierz istniejący serwer bazy danych SQL. |
  | **Azure SQL Database** | Wybierz istniejącą bazę danych SQL. |
  | **Kierunki synchronizacji** | Wybierz **Synchronizacja dwukierunkowa**, **Centrum**, lub **z Centrum**. |
  | **Nazwa użytkownika** i **hasła** | Wprowadź poświadczenia istniejącego serwera bazy danych SQL, na którym znajduje się bazy danych elementów członkowskich. Nie wprowadzaj *nowe* poświadczenia w tej sekcji. |

  Wybierz **OK** i poczekaj na nowego elementu członkowskiego synchronizacji, może zostać utworzona i wdrożona.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Aby dodać bazę danych programu SQL Server w środowisku lokalnym

W **bazy danych elementów członkowskich** sekcji i opcjonalnie Dodaj na lokalnym serwerze SQL Server do grupy synchronizacji, wybierając **Dodawanie lokalnej bazy danych**. **Konfigurować lokalnego** stronie zostanie otwarta, gdzie można wykonać następujące czynności:

1. Wybierz **Wybieranie bramy agenta synchronizacji**. **Wybieranie agenta synchronizacji** zostanie otwarta strona.

   ![Tworzenie agenta synchronizacji](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na **wybierz agenta synchronizacji** wybierz opcję użycia istniejącego agenta lub Utwórz agenta.

   Jeśli wybierzesz **agentów istniejące**, z listy wybierz istniejącego agenta.

   Jeśli wybierzesz **Utwórz nowego agenta**, wykonaj następujące czynności:

   1. Pobierz agenta synchronizacji danych z linku podanego i zainstaluj go na komputerze, na którym znajduje się serwer SQL. Możesz również pobrać agenta bezpośrednio z [agenta synchronizacji danych Azure SQL](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Musisz otworzyć wychodzącego portu TCP 1433 w zaporze aby umożliwić agenta klienta komunikacji z serwerem.

   1. Wprowadź nazwę dla agenta.

   1. Wybierz **Utwórz i wygeneruj klucz** i skopiuj klucz agenta do Schowka.

   1. Wybierz **OK** zamknąć **Wybieranie agenta synchronizacji** strony.

1. Na komputerze programu SQL Server Znajdź i uruchom aplikację klienta agenta synchronizacji.

   ![Dane synchronizacji aplikacji agenta klienta](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. W aplikacji agenta synchronizacji, wybierz **przesłać klucz agenta**. **Konfiguracji bazy danych metadanych synchronizacji** zostanie otwarte okno dialogowe.

    1. W **konfiguracji bazy danych metadanych synchronizacji** okno dialogowe, Wklej klucz agenta skopiowane z portalu Azure. Udostępniają istniejących poświadczeń dla serwera usługi Azure SQL Database, na którym znajduje się baza danych metadanych. (Jeśli utworzono bazę danych metadanych, ta baza danych jest na tym samym serwerze co baza danych koncentratora). Wybierz **OK** i poczekaj na zakończenie konfiguracji.

        ![Wprowadź poświadczenia klucza i serwera agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Jeśli wystąpi błąd zapory, należy utworzyć regułę zapory na platformie Azure, aby zezwolić na ruch przychodzący z komputerem programu SQL Server. Regułę można utworzyć ręcznie w portalu lub w SQL Server Management Studio (SSMS). W programie SSMS, połączenia z bazą centrum danych na platformie Azure, wprowadzając jego nazwę jako < hub_database_name >. database.windows.net.

    1. Wybierz **zarejestrować** zarejestrować bazę danych programu SQL Server za pośrednictwem agenta. **Konfiguracja programu SQL Server** zostanie otwarte okno dialogowe.

        ![Dodawanie i Konfigurowanie bazy danych programu SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. W **Konfiguracja programu SQL Server** okna dialogowego wybierz opcję połączenia przy użyciu uwierzytelniania programu SQL Server lub Windows. Jeśli wybierzesz opcję uwierzytelniania programu SQL Server, wprowadź istniejących poświadczeń. Podaj nazwę programu SQL Server i nazwę bazy danych, które chcesz synchronizować, a następnie wybierz pozycję **Testuj połączenie** Aby przetestować ustawienia. Następnie wybierz pozycję **Zapisz** i pojawia się na liście zarejestrowanych bazy danych.

        ![Bazy danych programu SQL Server jest obecnie zarejestrowany.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zamknij klienta synchronizacji aplikacji agenta.

1. W portalu na **konfigurować lokalnego** wybierz opcję **wybierz bazę danych**.

1. Na **wybierz bazę danych** stronie **nazwa elementu członkowskiego synchronizacji** , podaj nazwę dla nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od nazwy bazy danych. Wybierz bazę danych z listy. W **kierunki synchronizacji** pól, zaznacz **Synchronizacja dwukierunkowa**, **do Centrum**, lub **z Centrum**.

    ![Wybierz bazę danych na lokalnym](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Wybierz **OK** zamknąć **wybierz bazę danych** strony. Następnie wybierz pozycję **OK** zamknąć **konfigurować lokalnego** strony, a następnie poczekaj, aż nowego elementu członkowskiego synchronizacji, może zostać utworzona i wdrożona. Na koniec wybierz pozycję **OK** zamknąć **Wybieranie elementów członkowskich synchronizacji** strony.

> [!NOTE]
> Nawiązać połączenia SQL Data Sync i agent lokalny, Dodaj swoją nazwę użytkownika do roli *DataSync_Executor*. Synchronizacja danych tworzy tę rolę w wystąpieniu programu SQL Server.

## <a name="configure-sync-group"></a>Konfigurowanie grupy synchronizacji

Po nowych elementów członkowskich grupy synchronizacji są tworzone i wdrażane, **Konfigurowanie grupy synchronizacji (krok 3)** zostanie wyróżniony kolorem **Nowa grupa synchronizacji** strony.

![Krok 3 ustawienia](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na **tabel** strony, wybierz bazę danych z listy elementów członkowskich grupy synchronizacji i wybierz **odświeżania schematu**.

1. Z listy wybierz tabele, które mają być synchronizowane. Domyślnie wszystkie kolumny są zaznaczone, więc wyłącz pole wyboru dla kolumny, które nie mają być synchronizowane. Pamiętaj pozostawić zaznaczone kolumny klucza podstawowego.

1. Wybierz pozycję **Zapisz**.

1. Domyślnie baz danych nie jest zsynchronizowana, dopóki według harmonogramu lub ręcznie uruchamiać. Aby uruchomić ręczną synchronizację, przejdź do usługi SQL database w witrynie Azure portal, wybierz opcję **synchronizacji z innymi bazami danych**, a następnie wybierz grupę synchronizacji. **Data Sync** zostanie otwarta strona. Wybierz pozycję **Synchronizuj**.

    ![Ręczną synchronizację](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Często zadawane pytania

**Częstotliwość synchronizacji danych można synchronizować dane?**

Minimalny czas między synchronizacjami wynosi pięć minut.

**SQL Data Sync w pełni tworzy tabele?**

Jeśli tabele schematu synchronizacji w docelowej bazie danych SQL Data Sync utworzone przy użyciu wybranych kolumn. Jednak to nie spowodować schematu pełnej wierności z następujących powodów:

- Tylko te kolumny, którą wybierzesz są tworzone w tabeli docelowej. Nie wybrano kolumn są ignorowane.
- Tylko wybrane kolumny indeksy są tworzone w tabeli docelowej. Nie wybrano kolumn, aby uzyskać te indeksy są ignorowane.
- Indeksy w kolumnach typu XML nie są tworzone.
- Ograniczenia CHECK nie są tworzone.
- Wyzwalacze w tabelach źródłowych nie są tworzone.
- Nie tworzy się widoków i procedur składowanych.

Ze względu na te ograniczenia zaleca się następujące elementy:

- W środowiskach produkcyjnych utworzyć schemat pełnej wierności samodzielnie.
- Eksperymentowanie w usłudze, użyj funkcji automatycznej aprowizacji.

**Dlaczego są wyświetlane tabele, które nie jest utworzona?**

Synchronizacja danych tworzy dodatkowe tabele w bazie danych śledzenie zmian. Nie należy usuwać je lub synchronizacja danych zostanie zatrzymane.

**Czy moje dane zbieżne po synchronizacji?**

Niekoniecznie. Wykonaj grupa synchronizacji o gwiazdy trzy (A, B i C), gdzie synchronizacji znajdują się w Centrum a, Centrum b i Centrum C. W przypadku wprowadzania zmian do bazy danych A *po* koncentratora do synchronizacji, które zmiany nie jest zapisywane z bazami danych B lub C, aż do następnego zadania synchronizacji.

**Jak uzyskać zmian schematu w ramach grupy synchronizacji?**

Wprowadź i propagowania wszystkich zmian schematu ręcznie.

1. Do koncentratora i do wszystkich elementów członkowskich synchronizacji ręcznie replikować zmiany schematu.
1. Aktualizowanie schematu synchronizacji.

Aby dodać nowe tabele i kolumny:

Nowe tabele i kolumny nie miały wpływu na bieżące synchronizacji i synchronizacji danych będzie je ignorować, dopóki nie są one dodawane do schematu synchronizacji. Podczas dodawania nowych obiektów bazy danych, należy stosować się do sekwencji:

1. Dodaj nowe tabele lub kolumny do koncentratora i do wszystkich elementów członkowskich synchronizacji.
1. Dodaj nowe tabele lub kolumny do schematu synchronizacji.
1. Rozpocznij, wstawiania wartości do nowych tabel i kolumn.

Aby uzyskać zmiana typu danych kolumny:

Po zmianie typu danych kolumny istniejącej Data Sync w dalszym ciągu działać tak długo, jak oryginalny typ danych zdefiniowany w schemacie synchronizacji mieści się nowymi wartościami miar. Na przykład, jeśli zmienisz typ do źródłowej bazy danych z **int** do **bigint**, Data Sync w dalszym ciągu działać do momentu wstawienia wartości zbyt duże, aby **int** typu danych. Aby dokonać zmiany, zmiany schematu ręcznie do koncentratora i replikować do wszystkich elementów członkowskich synchronizacji, a następnie zaktualizować schematu synchronizacji.

**Jak eksportować i importować bazę danych z opcją synchronizacji danych?**

Po wyeksportowaniu bazę danych jako *bacpac* pliku i zaimportować plik, aby utworzyć bazę danych, wykonaj następujące polecenie, aby korzystać z synchronizacji danych do nowej bazy danych:

1. Czyszczenie obiektów Data Sync i dodatkowe tabele dla nowej bazy danych przy użyciu [ten skrypt](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skrypt spowoduje usunięcie wszystkich wymaganych obiektów synchronizacji danych z bazy danych.
1. Utwórz ponownie grupę synchronizacji przy użyciu nowej bazy danych. Jeśli nie potrzebujesz już starej grupy synchronizacji, należy go usunąć.

**Gdzie można znaleźć informacji na temat agenta klienta?**

Często zadawane pytania dotyczące agenta klienta, zobacz [agenta — często zadawane pytania](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Kolejne kroki

Gratulacje. Utworzono grupę synchronizacji, która obejmuje zarówno w wystąpieniu bazy danych SQL, jak i bazy danych programu SQL Server.

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

- [Agent synchronizacji danych do usługi Azure SQL Data Sync](sql-database-data-sync-agent.md)
- [Najlepsze praktyki](sql-database-best-practices-data-sync.md) i [sposób rozwiązywania problemów z usługą Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
- [Monitorowanie SQL Data Sync za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
- [Aktualizowanie schematu synchronizacji za pomocą instrukcji języka Transact-SQL](sql-database-update-sync-schema.md) lub [programu PowerShell](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
