---
title: Rozwiązywanie problemów z usługi Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe problemy z usługą Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 8b86103f76e2425acea1b4f7c590204f0a893510
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484432"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Rozwiązywanie problemów z usługą SQL Data Sync

W tym artykule opisano, jak rozwiązywać problemy z znanych problemów z usługą Azure SQL Data Sync. W przypadku rozwiązywania problemu, jest ona udostępniana w tym miejscu.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="sync-issues"></a>Problemy z synchronizacją

- [Synchronizacja nie powiedzie się w portalu interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta](#sync-fails)

- [Moje grupy synchronizacji jest zablokowany w stanie przetwarzania](#sync-stuck)

- [Widzę błędnych danych w tabeli](#sync-baddata)

- [Widzę niespójność danych klucza podstawowego po pomyślnej synchronizacji](#sync-pkdata)

- [Czy mogę zobaczyć znaczne pogorszenie wydajności](#sync-perf)

- [Pojawia się następujący komunikat: "Nie można wstawić wartości NULL w kolumnie <column>. Kolumna nie dopuszcza wartości null." Co to znaczy, i jak to naprawić?](#sync-nulls)

- [Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, kiedy te same dane jest synchronizowana w wielu grupach synchronizacji i ulega ciągłym zmianom w wyniku?](#sync-circ)

### <a name="sync-fails"></a> Synchronizacja nie powiedzie się w portalu interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta

Synchronizacja nie powiedzie się w portalu SQL Data Sync interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta. Na komputerze lokalnym, w którym jest uruchomiony agent zostaną wyświetlone błędy System.IO.IOException w dzienniku zdarzeń. Błędy Załóżmy, że na dysku jest za mało miejsca.

- **Przyczyna**. Na dysku jest za mało miejsca.

- **Rozpoznawanie**. Utwórz więcej miejsca na dysku, na którym znajduje się katalogu % TEMP %.

### <a name="sync-stuck"></a> Moje grupy synchronizacji jest zablokowany w stanie przetwarzania

Grupą synchronizacji w SQL Data Sync był w stanie przetwarzania przez długi czas. Nie odpowiada na **zatrzymać** polecenia i dzienniki przedstawiają żadnych nowych wpisów.

Dowolne z następujących warunków może prowadzić do grupy synchronizacji jest zablokowana w stanie przetwarzania:

- **Przyczyna**. Agent klienta jest w trybie offline

- **Rozpoznawanie**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozpoznawanie**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:

    1. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.
    1. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Usługa SQL Data Sync jest zatrzymana.

- **Rozpoznawanie**. Uruchom ponownie usługę SQL Data Sync.

    1. W **Start** menu, wyszukaj **usług**.
    1. W wynikach wyszukiwania wybierz **usług**.
    1. Znajdź **SQL Data Sync** usługi.
    1. Jeśli stan usługi **zatrzymane**, kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz **Start**.

> [!NOTE]
> Jeśli powyższych informacji nie wychodzenia grupy synchronizacji stan przetwarzania, Microsoft Support zresetować stan grupy synchronizacji. Aby mieć swój status grupy synchronizacji resetowania, w [forum usługi Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), Utwórz wpis. We wpisie obejmują identyfikator subskrypcji i identyfikator grupy synchronizacji dla grupy, która powinna zostać zresetowana. Inżynier firmy Microsoft Support odpowie na ogłoszenie i powiadomi Cię o tym, kiedy stan został zresetowany.

### <a name="sync-baddata"></a> Widzę błędnych danych w tabeli

Jeśli tabele mają taką samą nazwę, ale które są ze schematów innej bazy danych są uwzględnione w synchronizacji, zostanie wyświetlony błędne dane w tabelach po synchronizacji.

- **Przyczyna**. SQL Data Sync procesie inicjowania obsługi używa tych samych tabelach śledzenia dla tabel, które mają taką samą nazwę, ale które są w różnych schematach. W związku z tym zmiany z obu tabel są uwzględniane w tej samej tabeli śledzenia. Powoduje to zmiany błędne dane podczas synchronizacji.

- **Rozpoznawanie**. Upewnij się, że nazwy tabel, które są zaangażowane w synchronizacji są różne, nawet jeśli tabele należą do różnych schematów w bazie danych.

### <a name="sync-pkdata"></a> Widzę niespójność danych klucza podstawowego po pomyślnej synchronizacji

Synchronizacji zostanie zgłoszone jako operacja się powiedzie i dziennik nie zawiera żadnych wierszy nie powiodło się lub pominięta, ale można zaobserwować, że danymi klucza podstawowego jest niespójny między bazami danych w grupie synchronizacji.

- **Przyczyna**. Ten wynik jest celowe. Zmiany w dowolnej kolumny klucza podstawowego spowodować niespójność danych w wierszach, w którym klucz podstawowy został zmieniony.

- **Rozpoznawanie**. Aby uniknąć tego problemu, upewnij się, że żadne dane w kolumnie klucza podstawowego jest zmieniany. Aby rozwiązać ten problem, po zdarzeniu, należy usunąć wiersza, który ma niespójne dane ze wszystkich punktów końcowych w grupie synchronizacji. Następnie można ponownie wstawić wiersza.

### <a name="sync-perf"></a> Czy mogę zobaczyć znaczne pogorszenie wydajności

Wydajność ulega znacznej degradacji, prawdopodobnie w punkcie, gdzie jeszcze nie można otworzyć Interfejs użytkownika usługi Data Sync.

- **Przyczyna**. Najbardziej prawdopodobną przyczyną jest pętlą synchronizacji. Pętla synchronizacji występuje po grupie synchronizacji przy synchronizacji A wyzwala grupy synchronizacji przez synchronizacji B, który następnie wyzwala A. grupy synchronizacji przy synchronizacji Rzeczywistych sytuacji może być bardziej skomplikowane i może obejmować więcej niż dwie grupy synchronizacji w pętli. Problem polega na to cykliczne wyzwolenie synchronizacji, która jest spowodowany przez grupy synchronizacji nakładających się wzajemnie.

- **Rozpoznawanie**. Najlepsze poprawka jest zapobieganie. Upewnij się, że nie masz odwołania cykliczne w grupach synchronizacji. Nie można zsynchronizować wszystkie wiersze, która jest synchronizowana według jedną grupę synchronizacji przez inną grupę synchronizacji.

### <a name="sync-nulls"></a> Pojawia się następujący komunikat: "Nie można wstawić wartości NULL w kolumnie <column>. Kolumna nie dopuszcza wartości null." Co to znaczy, i jak to naprawić? 
Ten komunikat o błędzie wskazuje, że wystąpił jeden z dwóch następujących problemów:
-  Tabela nie ma klucza podstawowego. Aby rozwiązać ten problem, należy dodać klucz podstawowy do wszystkich tabel, które synchronizowania.
-  Brak klauzuli WHERE w instrukcji CREATE INDEX. Synchronizacja danych nie obsługuje tego warunku. Aby rozwiązać ten problem, usuń klauzulę WHERE, lub ręcznie wprowadzić zmiany do wszystkich baz danych. 
 
### <a name="sync-circ"></a> Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, kiedy te same dane jest synchronizowana w wielu grupach synchronizacji i ulega ciągłym zmianom w wyniku?
Synchronizacja danych nie obsługuje odwołań cyklicznych. Należy ich unikać. 

## <a name="client-agent-issues"></a>Problemy dotyczące agenta klienta

Aby rozwiązać problemy z agenta klienta, zobacz [wystawia Rozwiązywanie problemów z agenta synchronizacji danych](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemy dotyczące instalacji i konserwacji

- [Otrzymuję komunikat "dysk mało miejsca na"](#setup-space)

- [Nie można usunąć Moja grupa synchronizacji](#setup-delete)

- [I nie można wyrejestrować z lokalnej bazy danych programu SQL Server](#setup-unreg)

- [I nie ma wystarczających uprawnień do uruchamiania usług systemowych](#setup-perms)

- [Bazy danych ma stan "Przestarzałe"](#setup-date)

- [Grupa synchronizacji ma stan "Przestarzałe"](#setup-date2)

- [Nie można usunąć grupy synchronizacji w ciągu trzech minut odinstalowanie lub zatrzymanie agenta](#setup-delete2)

- [Co się stanie, gdy przywrócić utracony lub uszkodzony bazy danych?](#setup-restore)

### <a name="setup-space"></a> Otrzymuję komunikat "dysk mało miejsca na"

- **Przyczyna**. Może zostać wyświetlony komunikat "dysk wolne miejsce", jeśli pozostawieniu plików, które muszą zostać usunięte. Może to być spowodowane przez oprogramowanie antywirusowe lub pliki są otwarte w przypadku usunięcia próbą wykonania operacji.

- **Rozpoznawanie**. Ręcznie usuń synchronizować pliki, które znajdują się w folderze % temp % (`del \*sync\* /s`). Następnie można usunąć podkatalogi w folderze % temp %.

> [!IMPORTANT]
> Nie usuwaj żadnych plików, w trakcie synchronizacji.

### <a name="setup-delete"></a> Nie można usunąć Moja grupa synchronizacji

Próba usunięcia grupy synchronizacji kończy się niepowodzeniem. Dowolne z poniższych scenariuszy może spowodować awarię, aby usunąć grupę synchronizacji:

- **Przyczyna**. Agent klienta jest w trybie offline.

- **Rozpoznawanie**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozpoznawanie**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:  
    a. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Baza danych jest w trybie offline.

- **Rozpoznawanie**. Upewnij się, że baz danych SQL i baz danych programu SQL Server wszystkie online.

- **Przyczyna**. Grupy synchronizacji jest aprowizowania lub synchronizowanie.

- **Rozpoznawanie**. Poczekaj na zakończenie procesu aprowizacji lub synchronizacji, a następnie ponów próbę usuwania grupy synchronizacji.

### <a name="setup-unreg"></a> I nie można wyrejestrować z lokalnej bazy danych programu SQL Server

- **Przyczyna**. Najprawdopodobniej chcesz wyrejestrować bazy danych, która została już usunięta.

- **Rozpoznawanie**. Aby wyrejestrować lokalnej bazy danych programu SQL Server, wybierz bazę danych, a następnie wybierz pozycję **wymusić usunięcie**.

  Jeśli ta operacja nie powiedzie się usunąć bazę danych z grupy synchronizacji:

  1. Zatrzymaj i ponownie uruchom usługę hosta agenta klienta:  
    a. Wybierz **Start** menu.  
    b. W polu wyszukiwania wprowadź **services.msc**.  
    c. W **programy** sekcji wyszukiwanie wyników okienku kliknij dwukrotnie **usług**.  
    d. Kliknij prawym przyciskiem myszy **SQL Data Sync** usługi.  
    e. Jeśli usługa jest uruchomiona, zatrzymaj ją.  
    f. Kliknij prawym przyciskiem myszy usługę, a następnie wybierz **Start**.  
    g. Sprawdź, czy baza danych jest nadal zarejestrowany. Jeśli nie jest już zarejestrowany, wszystko będzie gotowe. W przeciwnym razie przejdź do następnego kroku.
  1. Otwórz aplikację agenta klienta (SqlAzureDataSyncAgent).
  1. Wybierz **edytowanie poświadczeń**, a następnie wprowadź poświadczenia dla bazy danych.
  1. Przejdź do wyrejestrowania.

### <a name="setup-perms"></a> I nie ma wystarczających uprawnień do uruchamiania usług systemowych

- **Przyczyna**. Ten błąd występuje w dwóch sytuacjach:
  -   Nazwa użytkownika i/lub hasło są niepoprawne.
  -   Określone konto użytkownika nie ma wystarczających uprawnień, aby zalogować się jako usługa.

- **Rozpoznawanie**. Przyznanie poświadczeń log na as-a-service, aby konto użytkownika:

  1. Przejdź do **Start** > **Panel sterowania** > **narzędzia administracyjne** > **zasady zabezpieczeń lokalnych**  >  **Zasady lokalne** > **użytkownika usługi Rights Management**.
  1. Wybierz **Zaloguj się jako usługa**.
  1. W **właściwości** okna dialogowego Dodaj konto użytkownika.
  1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  1. Zamknij wszystkie okna.

### <a name="setup-date"></a> Bazy danych ma stan "Przestarzałe"

- **Przyczyna**. SQL Data Sync spowoduje usunięcie bazy danych, które są w trybie offline z usługi w ciągu 45 dni lub więcej (zliczane od chwili, gdy baza danych przeszedł do trybu offline). Jeśli bazy danych jest w trybie offline, przez co najmniej 45 dni, a następnie powróci do trybu online, jego stan to **nieaktualne**.

- **Rozpoznawanie**. Możesz uniknąć **nieaktualne** stanu, zapewniając, że żadna z baz danych do trybu offline co najmniej 45 dni.

  Jeśli stan bazy danych jest **nieaktualne**:

  1. Usuń bazę danych, która ma **nieaktualne** stan z grupy synchronizacji.
  1. Dodawanie bazy danych ponownie do grupy synchronizacji.

  > [!WARNING]
  > Utracisz wszystkie zmiany wprowadzone do tej bazy danych, podczas gdy była w trybie offline.

### <a name="setup-date2"></a> Grupa synchronizacji ma stan "Przestarzałe"

- **Przyczyna**. Jeśli co najmniej jedną zmianę nie dotyczą okres przechowywania całej 45 dni, grupą synchronizacji może stać się nieaktualne.

- **Rozpoznawanie**. Aby uniknąć **nieaktualne** stan grupy synchronizacji, sprawdź wyniki zadań synchronizacji w podglądzie historii w regularnych odstępach czasu. Zbadać i rozwiązać wszelkie zmiany, których nie można zastosować.

  Jeśli stan grupą synchronizacji **nieaktualne**, Usuń grupę synchronizacji, a następnie utwórz je ponownie.

### <a name="setup-delete2"></a> Nie można usunąć grupy synchronizacji w ciągu trzech minut odinstalowanie lub zatrzymanie agenta

Nie można usunąć grupy synchronizacji, w ciągu trzech minut odinstalowanie lub zatrzymanie skojarzone agenta klienta SQL Data Sync.

- **Rozpoznawanie**.

  1. Usuń grupy synchronizacji, gdy agenci synchronizacji skojarzone są w trybie online (zalecane).
  1. Jeśli agent jest w trybie offline, ale jest zainstalowany, przenieś ją w trybie online na komputerze lokalnym. Poczekaj, aż stan agenta, aby pojawiało się jako **Online** w portalu usługi SQL Data Sync. Następnie można usunąć grupy synchronizacji.
  1. Jeśli agent jest w trybie offline, ponieważ zostało odinstalowane:  
    a.  Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b.  Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.  
    c. Spróbuj usunąć grupę synchronizacji.

### <a name="setup-restore"></a> Co się stanie, gdy przywrócić utracony lub uszkodzony bazy danych?

W przypadku utraty lub uszkodzenia bazy danych jest przywrócenie z kopii zapasowej, może to być rozbieżności danych w grupy synchronizacji, do których należy bazy danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat SQL Data Sync zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [dzienniki monitora SQL Data Sync za pomocą usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji o usłudze SQL Database zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
