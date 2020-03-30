---
title: Agent synchronizacji danych dla synchronizacji danych SQL
description: Dowiedz się, jak zainstalować i uruchomić agenta synchronizacji danych dla usługi Azure SQL Data Sync w celu synchronizacji danych z lokalnymi bazami danych programu SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 6d0a728401ac9f0156cc8fa913ce486bb577c6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825171"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizacji danych dla synchronizacji danych SQL usługi Azure

Synchronizowanie danych z lokalnymi bazami danych programu SQL Server przez zainstalowanie i skonfigurowanie agenta synchronizacji danych dla usługi Azure SQL Data Sync. Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz [Synchronizowanie danych w wielu chmurach i lokalnych bazach danych za pomocą synchronizacji danych SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="download-and-install"></a>Pobieranie i instalowanie

Aby pobrać agenta synchronizacji danych, przejdź do [programu SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalacja po cichu

Aby dyskretnie zainstalować agenta synchronizacji danych w wierszu polecenia, wprowadź polecenie podobne do poniższego przykładu. Sprawdź nazwę pliku pobranego .msi i podaj własne wartości dla argumentów **TARGETDIR** i **SERVICEACCOUNT.**

- Jeśli nie podasz wartości **targetdir,** wartością `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`domyślną jest .

- Jeśli podasz `LocalSystem` jako wartość **SERVICEACCOUNT,** użyj uwierzytelniania PROGRAMU SQL Server podczas konfigurowania agenta do łączenia się z lokalnym programem SQL Server.

- Jeśli podasz konto użytkownika domeny lub konto użytkownika lokalnego jako wartość **SERVICEACCOUNT,** musisz również podać hasło z argumentem **SERVICEPASSWORD.** Na przykład `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizowanie danych z programem SQL Server lokalnie

Aby skonfigurować agenta synchronizacji danych, aby można było synchronizować dane z co najmniej jedną lokalną bazą danych programu SQL Server, zobacz [Dodawanie lokalnej bazy danych programu SQL Server](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>— często zadawane pytania dotyczące agenta synchronizacji danych

### <a name="why-do-i-need-a-client-agent"></a>Dlaczego potrzebuję agenta klienta

Usługa synchronizacji danych SQL komunikuje się z bazami danych programu SQL Server za pośrednictwem agenta klienta. Ta funkcja zabezpieczeń zapobiega bezpośredniej komunikacji z bazami danych za zaporą. Gdy usługa SQL Data Sync komunikuje się z agentem, robi to przy użyciu połączeń szyfrowanych i unikatowego klucza tokenu lub *agenta.* Bazy danych programu SQL Server uwierzytelniają agenta przy użyciu ciągu połączenia i klucza agenta. Ten projekt zapewnia wysoki poziom bezpieczeństwa danych.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Ile wystąpień interfejsu użytkownika lokalnego agenta można uruchomić

Można uruchomić tylko jedno wystąpienie interfejsu użytkownika.

### <a name="how-can-i-change-my-service-account"></a>Jak mogę zmienić konto usługi

Po zainstalowaniu agenta klienta jedynym sposobem zmiany konta usługi jest odinstalowanie go i zainstalowanie nowego agenta klienta z nowym kontem usługi.

### <a name="how-do-i-change-my-agent-key"></a>Jak zmienić klucz agenta

Klucz agenta może być używany tylko raz przez agenta. Nie można go ponownie użyć po usunięciu, a następnie ponownie zainstalować nowego agenta, ani nie może być używany przez wielu agentów. Jeśli chcesz utworzyć nowy klucz dla istniejącego agenta, musisz mieć pewność, że ten sam klucz jest rejestrowany z agentem klienta i z usługą SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Jak wycofać agenta klienta

Aby natychmiast unieważnić lub wycofać agenta, ponownie wygeneruj jego klucz w portalu, ale nie przesyłaj go w interfejsie użytkownika agenta. Ponowne generowanie klucza unieważnia poprzedni klucz, niezależnie od tego, czy odpowiedni agent jest w trybie online lub offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak przenieść agenta klienta na inny komputer

Jeśli chcesz uruchomić agenta lokalnego z innego komputera niż jest on obecnie włączony, wykonaj następujące czynności:

1. Zainstaluj agenta na żądanym komputerze.
2. Zaloguj się do portalu synchronizacji danych SQL i ponownie wygeneruj klucz agenta dla nowego agenta.
3. Użyj interfejsu użytkownika nowego agenta, aby przesłać nowy klucz agenta.
4. Poczekaj, aż agent klienta pobierze listę lokalnych baz danych, które zostały zarejestrowane wcześniej.
5. Podaj poświadczenia bazy danych dla wszystkich baz danych, które są wyświetlane jako nieosiągalne. Te bazy danych muszą być dostępne z nowego komputera, na którym agent jest zainstalowany.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Rozwiązywanie problemów z agentem synchronizacji danych

- [Instalacja, odinstalowanie lub naprawa agenta klienta nie powiedzie się](#agent-install)

- [Agent klienta nie działa po anulowaniu odinstalowania](#agent-uninstall)

- [Moja baza danych nie znajduje się na liście agentów](#agent-list)

- [Agent klienta nie uruchamia się (błąd 1069)](#agent-start)

- [Nie mogę przesłać klucza agenta](#agent-key)

- [Nie można usunąć agenta klienta z portalu, jeśli skojarzona z nim lokalna baza danych jest nieosiągalna](#agent-delete)

- [Aplikacja Local Sync Agent nie może połączyć się z usługą synchronizacji lokalnej](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>Instalacja, odinstalowanie lub naprawa agenta klienta nie powiedzie się

- **Przyczyna**. Wiele scenariuszy może spowodować ten błąd. Aby ustalić konkretną przyczynę tego błędu, spójrz na dzienniki.

- **Rozdzielczość**. Aby znaleźć konkretną przyczynę błędu, wygeneruj i przyjrzyj się dziennikom Instalatora Windows. Rejestrowanie można włączyć w wierszu polecenia. Na przykład, jeśli pobrany `SQLDataSyncAgent-2.0-x86-ENU.msi`plik instalacyjny jest , generowanie i badanie plików dziennika przy użyciu następujących wierszy polecenia:

  - W przypadku instalacji:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - W przypadku odinstalowywania:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Można również włączyć rejestrowanie dla wszystkich instalacji wykonywanych przez Instalatora Windows. Artykuł z bazy wiedzy Microsoft Knowledge Base [Jak włączyć rejestrowanie Instalatora Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) zapewnia rozwiązanie jednym kliknięciem, aby włączyć rejestrowanie dla Instalatora Windows. Zapewnia również lokalizację dzienników.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>Agent klienta nie działa po anulowaniu odinstalowania

Agent klienta nie działa, nawet po anulowaniu jego dezinstalacji.

- **Przyczyna**. Dzieje się tak, ponieważ agent klienta synchronizacji danych SQL nie przechowuje poświadczeń.

- **Rozdzielczość**. Możesz wypróbować te dwa rozwiązania:

    -   Użyj services.msc, aby ponownie w ykorzystać z poświadczeń agenta klienta.
    -   Odinstaluj tego agenta klienta, a następnie zainstaluj nowy. Pobierz i zainstaluj najnowszego agenta klienta z [Download Center](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Moja baza danych nie znajduje się na liście agentów

Podczas próby dodania istniejącej bazy danych programu SQL Server do grupy synchronizacji baza danych nie jest wyświetlana na liście agentów.

Te scenariusze mogą powodować ten problem:

- **Przyczyna**. Agent klienta i grupa synchronizacji znajdują się w różnych centrach danych.

- **Rozdzielczość**. Agent klienta i grupa synchronizacji muszą znajdować się w tym samym centrum danych. Aby to skonfigurować, dostępne są dwie opcje:

    -   Utwórz nowego agenta w centrum danych, w którym znajduje się grupa synchronizacji. Następnie zarejestruj bazę danych za pomocą tego agenta.
    -   Usuń bieżącą grupę synchronizacji. Następnie ponownie utwórz grupę synchronizacji w centrum danych, w którym znajduje się agent.

- **Przyczyna**. Lista baz danych agenta klienta nie jest aktualna.

- **Rozdzielczość**. Zatrzymaj, a następnie uruchom ponownie usługę agenta klienta.

    Agent lokalny pobiera listę skojarzonych baz danych tylko przy pierwszym przesłaniu klucza agenta. Nie pobiera listy skojarzonych baz danych w kolejnych przesłaniach klucza agenta. Bazy danych, które są zarejestrowane podczas przenoszenia agenta nie są wyświetlane w oryginalnym wystąpieniu agenta.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Agent klienta nie uruchamia się (błąd 1069)

Można odkryć, że agent nie jest uruchomiony na komputerze, który obsługuje SQL Server. Podczas próby ręcznego uruchomienia agenta, zostanie wyświetlone okno dialogowe, w którym wyświetlany jest komunikat "Błąd 1069: Usługa nie została uruchomiona z powodu błędu logowania".

![Okno dialogowe Błąd synchronizacji danych 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Przyczyna**. Prawdopodobną przyczyną tego błędu jest to, że hasło na serwerze lokalnym uległo zmianie od czasu utworzenia hasła agenta i agenta.

- **Rozdzielczość**. Zaktualizuj hasło agenta do bieżącego hasła serwera:

  1. Znajdź usługę agenta klienta synchronizacji danych SQL.  
    a. Wybierz pozycję **Uruchom**.  
    b. W polu wyszukiwania wpisz **services.msc**.  
    d. W wynikach wyszukiwania wybierz pozycję **Usługi**.  
    d. W oknie **Usługi** przewiń do wpisu dla **agenta synchronizacji danych SQL**.  
  1. Kliknij prawym przyciskiem myszy **pozycję Agent synchronizacji danych SQL,** a następnie wybierz polecenie **Zatrzymaj**.
  1. Kliknij prawym przyciskiem myszy **pozycję Agent synchronizacji danych SQL,** a następnie wybierz polecenie **Właściwości**.
  1. W **obszarze Właściwości agenta synchronizacji danych SQL**wybierz kartę **Zaloguj.**
  1. W polu **Hasło** wprowadź hasło.
  1. W polu **Potwierdź hasło** ponownie wejdź ponownie.
  1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  1. W oknie **Usługi** kliknij prawym przyciskiem myszy usługę **Agent synchronizacji danych SQL,** a następnie kliknij polecenie **Start**.
  1. Zamknij okno **Usługi.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Nie mogę przesłać klucza agenta

Po utworzeniu lub ponownym utworzeniu klucza dla agenta, spróbuj przesłać klucz za pośrednictwem aplikacji SqlAzureDataSyncAgent. Zgłoszenie nie zostanie zakończone.

![Okno dialogowe Błąd synchronizacji — nie można przesłać klucza agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Wymagania wstępne**. Przed kontynuowaniem sprawdź następujące wymagania wstępne:

  - Usługa SQL Data Sync Windows jest uruchomiona.

  - Konto usługi SQL Data Sync usługi Windows ma dostęp do sieci.

  - Wychodzący port 1433 jest otwarty w lokalnej regule zapory.

  - Lokalne adresy IP są dodawane do reguły zapory serwera lub bazy danych dla bazy danych metadanych synchronizacji.

- **Przyczyna**. Klucz agenta jednoznacznie identyfikuje każdego agenta lokalnego. Klucz musi spełniać dwa warunki:

  -   Klucz agenta klienta na serwerze synchronizacji danych SQL i komputerze lokalnym musi być identyczny.
  -   Klucz agenta klienta można użyć tylko raz.

- **Rozdzielczość**. Jeśli agent nie działa, to dlatego, że jeden lub oba te warunki nie są spełnione. Aby ponownie zachęcić agenta do pracy:

  1. Wygeneruj nowy klucz.
  1. Zastosuj nowy klucz do agenta.

  Aby zastosować nowy klucz do agenta:

  1. W Eksploratorze plików przejdź do katalogu instalacji agenta. Domyślnym katalogiem instalacyjnym jest C:\\Program Files (x86)\\Microsoft SQL Data Sync.
  1. Kliknij dwukrotnie podkatalog pojemnika.
  1. Otwórz aplikację SqlAzureDataSyncAgent.
  1. Wybierz **pozycję Prześlij klucz agenta**.
  1. W podanym miejscu wklej klucz ze schowka.
  1. Kliknij przycisk **OK**.
  1. Zamknij program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>Nie można usunąć agenta klienta z portalu, jeśli skojarzona z nim lokalna baza danych jest nieosiągalna

Jeśli lokalny punkt końcowy (czyli baza danych), który jest zarejestrowany za pomocą agenta klienta synchronizacji danych SQL staje się nieosiągalny, agent klienta nie można usunąć.

- **Przyczyna**. Nie można usunąć agenta lokalnego, ponieważ nieosiągalna baza danych jest nadal zarejestrowana u agenta. Podczas próby usunięcia agenta, proces usuwania próbuje dotrzeć do bazy danych, która nie powiedzie się.

- **Rozdzielczość**. Użyj opcji "wymuś usunięcie", aby usunąć nieosiągalną bazę danych.

> [!NOTE]
> Jeśli tabele metadanych synchronizacji pozostaną po "wymuś usunięcie", użyj `deprovisioningutil.exe` ich do ich usunięcia.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Aplikacja Local Sync Agent nie może połączyć się z usługą synchronizacji lokalnej

- **Rozdzielczość**. Spróbuj wykonać następujące kroki:

  1. Zamknij aplikację.  
  1. Otwórz Panel Usług składowych.  
    a. W polu wyszukiwania na pasku zadań wprowadź **plik services.msc**.  
    b. W wynikach wyszukiwania kliknij dwukrotnie pozycję **Usługi**.  
  1. Zatrzymaj usługę **SQL Data Sync.**
  1. Uruchom ponownie usługę **SQL Data Sync.**  
  1. Otwórz ponownie tę aplikację.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Uruchamianie agenta synchronizacji danych z wiersza polecenia

Z wiersza polecenia można uruchomić następujące polecenia Agenta synchronizacji danych:

### <a name="ping-the-service"></a>Ping usługi

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Wyświetlanie zarejestrowanych baz danych

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Prześlij klucz agenta

#### <a name="usage"></a>Sposób użycia

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Rejestrowanie bazy danych

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Przykłady

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Wyrejestrowywę bazy danych

Gdy używasz tego polecenia do wyrejestrowania bazy danych, całkowicie usuwa aprovisions bazy danych. Jeśli baza danych uczestniczy w innych grupach synchronizacji, ta operacja przerywa inne grupy synchronizacji.

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Aktualizowanie poświadczeń

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Przykłady

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz następujące artykuły:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)
