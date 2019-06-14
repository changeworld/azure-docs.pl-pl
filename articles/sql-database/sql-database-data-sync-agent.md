---
title: Dane synchronizacji agenta dla usługi Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i uruchomić agenta synchronizacji danych do usługi Azure SQL Data Sync na synchronizowanie danych z bazy danych programu SQL Server w środowisku lokalnym
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
ms.date: 12/20/2018
ms.openlocfilehash: adb8917605a00208b328e7fd15f96d28c7838988
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60201667"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizacji danych do usługi Azure SQL Data Sync

Synchronizowanie danych z lokalnych baz danych programu SQL Server, instalując i konfigurując agenta synchronizacji danych do usługi Azure SQL Data Sync. Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz [synchronizowanie danych w wielu bazach danych w chmurze i lokalnych z usługą SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="download-and-install"></a>Pobierz i zainstaluj

Aby pobrać agenta synchronizacji danych, przejdź do [agenta synchronizacji danych Azure SQL](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalacja dyskretna

Aby zainstalować agenta synchronizacji danych w trybie dyskretnym z poziomu wiersza polecenia, wprowadź polecenie podobne do poniższego przykładu. Sprawdź nazwę pliku pliku .msi pobrane i podać własne wartości dla **TARGETDIR** i **SERVICEACCOUNT** argumentów.

- Jeśli nie podano wartości dla **TARGETDIR**, wartość domyślna to `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Jeśli podasz `LocalSystem` jako wartość **SERVICEACCOUNT**, Użyj uwierzytelniania programu SQL Server podczas konfigurowania agenta Aby nawiązać połączenie z lokalnym serwerem SQL.

- Jeśli zostaną podane konto użytkownika domeny lub lokalnego konta użytkownika jako wartość **SERVICEACCOUNT**, należy również podać hasło za pomocą **SERVICEPASSWORD** argumentu. Na przykład `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizowanie danych za pomocą programu SQL Server w środowisku lokalnym

Aby skonfigurować agenta synchronizacji danych, dzięki czemu można synchronizować dane z bazy danych programu SQL Server w środowisku lokalnym jeden lub więcej, zobacz [Dodawanie bazy danych programu SQL Server w środowisku lokalnym](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> Agent synchronizacji danych — często zadawane pytania

### <a name="why-do-i-need-a-client-agent"></a>Dlaczego należy to agent klienta

Usługa SQL Data Sync komunikuje się z bazy danych SQL Server za pośrednictwem agenta klienta. Ta funkcja zabezpieczeń uniemożliwiają bezpośrednią komunikację z bazami danych za zaporą. Gdy usługa SQL Data Sync komunikuje się z agenta, robi to przy użyciu zaszyfrowane połączenia i unikatowy token lub *klucz agenta*. Bazy danych programu SQL Server uwierzytelnienia agenta przy użyciu klucza ciągu i agent połączenia. Ten projekt zapewnia wysoki poziom zabezpieczeń dla Twoich danych.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Ile wystąpień agent lokalny można uruchomić interfejs użytkownika

Można uruchomić tylko jedno wystąpienie interfejsu użytkownika.

### <a name="how-can-i-change-my-service-account"></a>Jak zmienić konto usługi

Po zainstalowaniu agenta klienta, jedynym sposobem, aby zmienić konto usługi jest odinstalowanie go i zainstaluj nowego agenta klienta przy użyciu nowego konta usługi.

### <a name="how-do-i-change-my-agent-key"></a>Jak zmienić mój klucz agenta

Klucz agenta można używać tylko raz przez agenta. Nie można użyć ponownie, gdy należy usunąć, a następnie ponownie zainstaluj nowego agenta ani nie może on być używany przez wielu agentów. Jeśli potrzebujesz utworzyć nowy klucz dla istniejącego agenta, należy się upewnić, że ten sam klucz jest rejestrowane za pomocą agenta klienta i w usłudze SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Jak wycofać to agent klienta

Aby natychmiast unieważnia lub wycofanie agenta, Wygeneruj ponownie klucz w portalu, ale nie przesyłać go w Interfejsie użytkownika agentów. Trwa ponowne generowanie klucza unieważnia poprzedni klucz niezależnie, jeśli agent odpowiedniego online lub offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak przenieść agenta klienta do innego komputera

Jeśli chcesz uruchomić lokalnego agenta za pomocą innego komputera niż aktualnie na, wykonaj następujące czynności:

1. Zainstaluj agenta na wybrany komputer.
2. Zaloguj się do portalu usługi SQL Data Sync, a następnie ponownie wygenerować klucz agenta dla nowego agenta.
3. Korzystanie z interfejsu użytkownika nowy agent, aby przesłać nowy klucz agenta.
4. Zaczekaj, aż agent klienta pobierze listę lokalnych baz danych, które zostały wcześniej zarejestrowane.
5. Podaj poświadczenia bazy danych dla wszystkich baz danych, które wyświetlają jako niedostępny. Te bazy danych musi być dostępny z nowego komputera, na którym jest zainstalowany agent.

## <a name="agent-tshoot"></a> Rozwiązywanie problemów z agenta synchronizacji danych

- [Agent klienta zainstalować, odinstalować lub naprawić kończy się niepowodzeniem](#agent-install)

- [Agent klienta nie działa po anulowaniu dezinstalacji](#agent-uninstall)

- [Moja baza danych nie jest wymienione na liście agenta](#agent-list)

- [Agent klienta nie zaczyna się (błąd 1069)](#agent-start)

- [Nie mogę przesłać klucz agenta](#agent-key)

- [Nie można usunąć agenta klienta z poziomu portalu, jeśli jego skojarzony lokalnej bazy danych jest niedostępny](#agent-delete)

- [Lokalnego agenta synchronizacji aplikacji nie można nawiązać połączenia usługi synchronizacji lokalnych](#agent-connect)

### <a name="agent-install"></a> Agent klienta zainstalować, odinstalować lub naprawić kończy się niepowodzeniem

- **Przyczyna**. Wiele scenariuszy mogą być przyczyną tego błędu. Aby określić konkretną przyczynę tego błędu, sprawdź dzienniki.

- **Rozpoznawanie**. Aby znaleźć określone przyczynę niepowodzenia, należy wygenerować i spójrz na dzienniki Instalatora Windows. Można włączyć rejestrowanie w wierszu polecenia. Na przykład, jeśli jest pobranego pliku instalacyjnego `SQLDataSyncAgent-2.0-x86-ENU.msi`, generowania i przejrzyj pliki dziennika przy użyciu następujących wierszy polecenia:

  - Dla instalacji: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Aby uzyskać odinstalowuje: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Można również włączyć rejestrowanie dla wszystkich instalacji, które są wykonywane przez Instalatora Windows. Artykuł bazy wiedzy Microsoft Knowledge Base [sposobach włączania rejestrowania zdarzeń Instalatora Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) umożliwia rozwiązanie jednego kliknięcia, aby włączyć rejestrowanie dla Instalatora Windows. Umożliwia także lokalizacją dzienników.

### <a name="agent-uninstall"></a> Agent klienta nie działa po anulowaniu dezinstalacji

Agent klienta nie rozwiąże problemu, nawet po zakończeniu możesz anulować jego dezinstalacji.

- **Przyczyna**. Dzieje się tak, ponieważ agent klienta SQL Data Sync nie przechowuje poświadczeń.

- **Rozpoznawanie**. Możesz wypróbować te dwa rozwiązania:

    -   Wprowadź ponownie poświadczenia dla agenta klienta za pomocą apletu services.msc.
    -   Odinstaluj agenta klienta, a następnie zainstalować nową. Pobierz i zainstaluj najnowszą wersję agenta klienta z [Centrum pobierania](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Moja baza danych nie jest wymienione na liście agenta

Gdy spróbujesz dodać istniejącej bazy danych programu SQL Server do grupy synchronizacji, baza danych nie jest wyświetlane na liście agentów.

Te scenariusze mogą być przyczyną tego problemu:

- **Przyczyna**. Grupy synchronizacji i agent klienta znajdują się w różnych centrach danych.

- **Rozpoznawanie**. Agent klienta i grupy synchronizacji musi być w jednym centrum danych. Aby skonfigurować tę funkcjonalność, masz dwie opcje:

    -   Utwórz nowego agenta w centrum danych, w którym znajduje się grupa synchronizacji. Następnie zarejestruj bazy danych za pomocą tego agenta.
    -   Usuń bieżącą grupę synchronizacji. Następnie należy ponownie utworzyć grupy synchronizacji w centrum danych, w którym znajduje się agent.

- **Przyczyna**. Agent klienta listę baz danych nie jest aktualny.

- **Rozpoznawanie**. Zatrzymaj i ponownie uruchom usługę agenta klienta.

    Agent lokalny pobiera listę skojarzonych bazach danych tylko w pierwszym przesyłania klucz agenta. Go nie Pobierz listę skojarzonych bazach danych w kolejnych agentów zgłoszenia kluczy. Bazy danych, które są zarejestrowane w trakcie przenoszenia agenta nie pojawiają się w oryginalnym wystąpieniu agenta.

### <a name="agent-start"></a> Agent klienta nie zaczyna się (błąd 1069)

Dowiedz się, że agent nie jest uruchomiona na komputerze, który jest hostem serwera SQL. Podczas próby ręcznego uruchomienia agenta zobaczysz okno dialogowe, która wyświetla komunikat "Błąd 1069: Usługa nie została uruchomiona z powodu niepowodzenia logowania."

![Okno dialogowe błędu 1069 synchronizacji danych](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Przyczyna**. Prawdopodobną przyczyną wystąpienia tego błędu jest to, że hasło na lokalnym serwerze została zmieniona od czasu utworzenia agenta i hasło agenta.

- **Rozpoznawanie**. Zaktualizuj hasło agenta do swoje bieżące hasło serwera:

  1. Znajdź usługę agenta klienta SQL Data Sync.  
    a. Wybierz pozycję **Uruchom**.  
    b. W polu wyszukiwania wprowadź **services.msc**.  
    c. W wynikach wyszukiwania wybierz **usług**.  
    d. W **usług** okna, przewiń do wpisu dla **agenta synchronizacji danych programu SQL**.  
  1. Kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL**, a następnie wybierz pozycję **zatrzymać**.
  1. Kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL**, a następnie wybierz pozycję **właściwości**.
  1. Na **właściwości agenta synchronizacji danych programu SQL**, wybierz opcję **Zaloguj** kartę.
  1. W **hasło** wprowadź hasło.
  1. W **Potwierdź hasło** pole, wprowadź ponownie hasło.
  1. Wybierz **Zastosuj**, a następnie wybierz pozycję **OK**.
  1. W **usług** okna, kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL** usługi, a następnie kliknij przycisk **Start**.
  1. Zamknij **usług** okna.

### <a name="agent-key"></a> Nie mogę przesłać klucz agenta

Po utworzeniu lub ponownie utworzyć klucz dla agenta, spróbuj przesłać klucz za pośrednictwem aplikacji SqlAzureDataSyncAgent. Przesyłanie nie powiedzie się.

![Okno dialogowe błędu synchronizacji — nie można przesłać klucz agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Wymagania wstępne**. Przed kontynuowaniem sprawdź następujące wymagania wstępne:

  - Usługa SQL Data Sync Windows jest uruchomiona.

  - Konto usługi dla usługi SQL Data Sync Windows ma dostęp do sieci.

  - Wychodząca przez port 1433 jest otwarty w regule zapory lokalnej.

  - Lokalny adres ip jest dodawany do serwera lub reguły zapory bazy danych dla bazy danych metadanych synchronizacji.

- **Przyczyna**. Klucz agenta jednoznacznie identyfikuje każdy agent lokalny. Klucz musi spełniać dwa warunki:

  -   Klucz agenta klienta na serwerze SQL Data Sync i lokalnym komputerze muszą być identyczne.
  -   Klucz agenta klienta może służyć tylko raz.

- **Rozpoznawanie**. Jeśli agenta nie działa, jest to, ponieważ jeden lub oba te warunki nie są spełnione. Aby wyświetlić swoje agenta, aby ponownie działać:

  1. Wygeneruj nowy klucz.
  1. Zastosuj nowy klucz do agenta.

  Aby zastosować nowy klucz na agencie:

  1. W Eksploratorze plików przejdź do katalogu instalacji agenta. Domyślny katalog instalacji to C:\\Program Files (x86)\\programu Microsoft SQL Data Sync.
  1. Kliknij dwukrotnie ikonę podkatalogu bin.
  1. Otwórz aplikację SqlAzureDataSyncAgent.
  1. Wybierz **przesłać klucz agenta**.
  1. W podanym miejscu Wklej klucz ze Schowka.
  1. Kliknij przycisk **OK**.
  1. Zamknij program.

### <a name="agent-delete"></a> Nie można usunąć agenta klienta z poziomu portalu, jeśli jego skojarzony lokalnej bazy danych jest niedostępny

Jeśli lokalny punkt końcowy (czyli bazy danych) zarejestrowana w usłudze SQL Data Sync agenta klienta staje się niedostępny, nie można usunąć agenta klienta.

- **Przyczyna**. Nie można usunąć lokalny agent, ponieważ nieosiągalny bazy danych jest nadal zarejestrowany za pośrednictwem agenta. Podczas próby usunięcia agenta proces usuwania próbuje nawiązać połączenie bazy danych, która kończy się niepowodzeniem.

- **Rozpoznawanie**. Użyj "wymuszonego usunięcia" można usunąć bazy danych jest nieosiągalny.

> [!NOTE]
> Jeśli tabele metadanych synchronizacji pozostają po "wymuszonego usunięcia", użyj `deprovisioningutil.exe` pora to oczyścić.

### <a name="agent-connect"></a> Lokalnego agenta synchronizacji aplikacji nie można nawiązać połączenia usługi synchronizacji lokalnych

- **Rozpoznawanie**. Spróbuj wykonać następujące kroki:

  1. Zakończ działanie aplikacji.  
  1. Otwórz Panel usługi składnika.  
    a. W polu wyszukiwania na pasku zadań, wprowadź **services.msc**.  
    b. W wynikach wyszukiwania, kliknij dwukrotnie **usług**.  
  1. Zatrzymaj **SQL Data Sync** usługi.
  1. Uruchom ponownie **SQL Data Sync** usługi.  
  1. Otwórz ponownie tę aplikację.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Uruchom agenta synchronizacji danych z wiersza polecenia

Za uruchom następujące polecenia agenta synchronizacji danych w wierszu polecenia:

### <a name="ping-the-service"></a>Polecenie ping usługi

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

### <a name="register-a-database"></a>Zarejestruj bazę danych

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Przykłady

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Wyrejestruj bazy danych

Korzystając z tego polecenia można wyrejestrować bazy danych, jej deprovisions bazy danych całkowicie. Jeśli bazy danych należy inne grupy synchronizacji, ta operacja powoduje przerwanie innych grup synchronizacji.

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat SQL Data Sync zobacz następujące artykuły:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [dzienniki monitora SQL Data Sync za pomocą usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)
