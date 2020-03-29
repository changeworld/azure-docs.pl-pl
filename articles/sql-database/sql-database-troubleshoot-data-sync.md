---
title: Rozwiązywanie problemów z funkcją SQL Data Sync
description: Dowiedz się, jak rozwiązywać typowe problemy z synchronizacją danych SQL azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 8e223d0c6243bfddc1e5a56867c4c69de5e2a62e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822453"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Rozwiązywanie problemów z funkcją SQL Data Sync

W tym artykule opisano sposób rozwiązywania znanych problemów z synchronizacją danych SQL usługi Azure. Jeśli istnieje rozwiązanie problemu, jest ono dostępne w tym miejscu.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="sync-issues"></a>Problemy z synchronizacją

- [Synchronizacja kończy się niepowodzeniem w interfejsie użytkownika portalu dla lokalnych baz danych skojarzonych z agentem klienta](#sync-fails)

- [Moja grupa synchronizacji utknęła w stanie przetwarzania](#sync-stuck)

- [W tabelach widzę błędne dane](#sync-baddata)

- [Widzę niespójne dane klucza podstawowego po pomyślnej synchronizacji](#sync-pkdata)

- [Widzę znaczny spadek wydajności](#sync-perf)

- [Widzę ten komunikat: "Nie można wstawić wartości NULL do kolumny kolumny \<>. Kolumna nie zezwala na wartości null." Co to oznacza i jak mogę to naprawić?](#sync-nulls)

- [W jaki sposób synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, że gdy te same dane są synchronizowane w wielu grupach synchronizacji i ciągle się zmienia w wyniku?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>Synchronizacja kończy się niepowodzeniem w interfejsie użytkownika portalu dla lokalnych baz danych skojarzonych z agentem klienta

Synchronizacja kończy się niepowodzeniem w interfejsie użytkownika portalu synchronizacji danych SQL dla lokalnych baz danych, które są skojarzone z agentem klienta. Na komputerze lokalnym, na który jest uruchomiony agent, można zobaczyć Błędy System.IO.IOException w dzienniku zdarzeń. Błędy mówią, że na dysku jest za mało miejsca.

- **Przyczyna**. Na dysku nie ma wystarczającej ilości miejsca.

- **Rozdzielczość**. Utwórz więcej miejsca na dysku, na którym znajduje się katalog %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>Moja grupa synchronizacji utknęła w stanie przetwarzania

Grupa synchronizacji w synchronizacji danych SQL jest w stanie przetwarzania przez długi czas. Nie odpowiada na polecenie **stop,** a dzienniki nie pokazują żadnych nowych wpisów.

Każdy z następujących warunków może spowodować, że grupa synchronizacji zostanie zablokowana w stanie przetwarzania:

- **Przyczyna**. Agent klienta jest w trybie offline

- **Rozdzielczość**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozdzielczość**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:

    1. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.
    1. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Usługa SQL Data Sync jest zatrzymana.

- **Rozdzielczość**. Uruchom ponownie usługę SQL Data Sync.

    1. W menu **Start** wyszukaj **hasło Usługi**.
    1. W wynikach wyszukiwania wybierz pozycję **Usługi**.
    1. Znajdź usługę **SQL Data Sync.**
    1. Jeśli stan usługi to **Zatrzymano,** kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz polecenie **Start**.

> [!NOTE]
> Jeśli poprzednie informacje nie przesuną grupy synchronizacji ze stanu przetwarzania, pomoc techniczna firmy Microsoft może zresetować stan grupy synchronizacji. Aby zresetować stan grupy synchronizacji, na [forum usługi Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)utwórz post. W poście dołącz identyfikator subskrypcji i identyfikator grupy synchronizacji dla grupy, która musi zostać zresetowana. Inżynier pomocy technicznej firmy Microsoft odpowie na twój post i poinformuje Cię, kiedy stan zostanie zresetowany.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>W tabelach widzę błędne dane

Jeśli tabele, które mają taką samą nazwę, ale które są z różnych schematów bazy danych są uwzględnione w synchronizacji, zobaczysz błędne dane w tabelach po synchronizacji.

- **Przyczyna**. Proces inicjowania obsługi administracyjnej synchronizacji danych SQL używa tych samych tabel śledzenia dla tabel, które mają taką samą nazwę, ale które znajdują się w różnych schematach. Z tego powodu zmiany z obu tabel są odzwierciedlane w tej samej tabeli śledzenia. Powoduje to błędne zmiany danych podczas synchronizacji.

- **Rozdzielczość**. Upewnij się, że nazwy tabel, które są zaangażowane w synchronizacji są różne, nawet jeśli tabele należą do różnych schematów w bazie danych.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>Widzę niespójne dane klucza podstawowego po pomyślnej synchronizacji

Synchronizacja jest zgłaszana jako pomyślna, a dziennik nie pokazuje nieudanych lub pominiętych wierszy, ale można zaobserwować, że dane klucza podstawowego są niespójne między bazami danych w grupie synchronizacji.

- **Przyczyna**. Ten wynik jest zgodnie z projektem. Zmiany w dowolnej kolumnie klucza podstawowego powodują niespójne dane w wierszach, w których klucz podstawowy został zmieniony.

- **Rozdzielczość**. Aby zapobiec temu problemowi, upewnij się, że żadne dane w kolumnie klucza podstawowego nie są zmieniane. Aby rozwiązać ten problem po jego wystąpieniu, usuń wiersz, który ma niespójne dane ze wszystkich punktów końcowych w grupie synchronizacji. Następnie włóż ponownie wiersz.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Widzę znaczny spadek wydajności

Wydajność znacznie się obniża, prawdopodobnie do punktu, w którym nie można nawet otworzyć interfejsu użytkownika synchronizacji danych.

- **Przyczyna**. Najbardziej prawdopodobną przyczyną jest pętla synchronizacji. Pętla synchronizacji występuje, gdy synchronizacja według grupy synchronizacji A wyzwala synchronizację według grupy synchronizacji B, która następnie wyzwala synchronizację według grupy synchronizacji A. Rzeczywista sytuacja może być bardziej złożona i może obejmować więcej niż dwie grupy synchronizacji w pętli. Problem polega na tym, że istnieje okrągłe wyzwalanie synchronizacji, które jest spowodowane przez nakładające się na siebie grupy synchronizacji.

- **Rozdzielczość**. Najlepszą poprawką jest zapobieganie. Upewnij się, że nie masz odwołań cyklicznych w grupach synchronizacji. Żaden wiersz zsynchronizowany przez jedną grupę synchronizacji nie może być synchronizowany przez inną grupę synchronizacji.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>Widzę ten komunikat: "Nie można wstawić wartości NULL do kolumny kolumny \<>. Kolumna nie zezwala na wartości null." Co to oznacza i jak mogę to naprawić? 
Ten komunikat o błędzie wskazuje, że wystąpił jeden z dwóch następujących problemów:
-  Tabela nie ma klucza podstawowego. Aby rozwiązać ten problem, dodaj klucz podstawowy do wszystkich tabel, które synchronizujesz.
-  W instrukcji CREATE INDEX znajduje się klauzula WHERE. Synchronizacja danych nie obsługuje tego warunku. Aby rozwiązać ten problem, usuń klauzulę WHERE lub ręcznie wkonuj zmiany we wszystkich bazach danych. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>W jaki sposób synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, że gdy te same dane są synchronizowane w wielu grupach synchronizacji i ciągle się zmienia w wyniku?
Synchronizacja danych nie obsługuje odwołań cyklicznych. Pamiętaj, aby ich unikać. 

## <a name="client-agent-issues"></a>Problemy z agentem klienta

Aby rozwiązać problemy z agentem klienta, zobacz [Rozwiązywanie problemów z agentem synchronizacji danych](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemy z konfiguracją i konserwacją

- [Otrzymuję komunikat "dysk z miejsca"](#setup-space)

- [Nie mogę usunąć grupy synchronizacji](#setup-delete)

- [Nie mogę wyrejestrować lokalnej bazy danych programu SQL Server](#setup-unreg)

- [Nie mam wystarczających uprawnień do uruchamiania usług systemowych](#setup-perms)

- [Baza danych ma stan "Nieaktualne"](#setup-date)

- [Grupa synchronizacji ma stan "Nieaktualne"](#setup-date2)

- [Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymania agenta](#setup-delete2)

- [Co się stanie, gdy przywrócę utraconą lub uszkodzoną bazę danych?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>Otrzymuję komunikat "dysk z miejsca"

- **Przyczyna**. Jeśli resztki plików muszą zostać usunięte, może pojawić się komunikat "dysk za nieskłony" . Może to być spowodowane przez oprogramowanie antywirusowe lub pliki są otwarte podczas próby usunięcia operacji.

- **Rozdzielczość**. Ręcznie usuń pliki synchronizacji, które znajdują się`del \*sync\* /s`w folderze %temp% ( ). Następnie usuń podkatalogi w folderze %temp%.

> [!IMPORTANT]
> Nie usuwaj żadnych plików podczas synchronizacji.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Nie mogę usunąć grupy synchronizacji

Próba usunięcia grupy synchronizacji nie powiedzie się. Każdy z następujących scenariuszy może spowodować niepowodzenie usunięcia grupy synchronizacji:

- **Przyczyna**. Agent klienta jest w trybie offline.

- **Rozdzielczość**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozdzielczość**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:  
    a. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Baza danych jest w trybie offline.

- **Rozdzielczość**. Upewnij się, że bazy danych SQL i bazy danych programu SQL Server są w trybie online.

- **Przyczyna**. Grupa synchronizacji jest inicjowanie obsługi administracyjnej lub synchronizowanie.

- **Rozdzielczość**. Poczekaj, aż proces inicjowania obsługi administracyjnej lub synchronizacji zakończy się, a następnie ponów próbę usunięcia grupy synchronizacji.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Nie mogę wyrejestrować lokalnej bazy danych programu SQL Server

- **Przyczyna**. Najprawdopodobniej próbujesz wyrejestrować bazę danych, która została już usunięta.

- **Rozdzielczość**. Aby wyrejestrować lokalną bazę danych programu SQL Server, wybierz bazę danych, a następnie wybierz pozycję **Wymuś usuń**.

  Jeśli ta operacja nie może usunąć bazy danych z grupy synchronizacji:

  1. Zatrzymaj, a następnie uruchom ponownie usługę hosta agenta klienta:  
    a. Wybierz menu **Start.**  
    b. W polu wyszukiwania wpisz **services.msc**.  
    d. W sekcji **Programy** w okienku wyników wyszukiwania kliknij dwukrotnie pozycję **Usługi**.  
    d. Kliknij prawym przyciskiem myszy usługę **synchronizacji danych SQL.**  
    e. Jeśli usługa jest uruchomiona, zatrzymaj ją.  
    f. Kliknij prawym przyciskiem myszy usługę, a następnie wybierz polecenie **Start**.  
    g. Sprawdź, czy baza danych jest nadal zarejestrowana. Jeśli nie jest już zarejestrowany, gotowe. W przeciwnym razie należy kontynuować następny krok.
  1. Otwórz aplikację agenta klienta (SqlAzureDataSyncAgent).
  1. Wybierz **pozycję Edytuj poświadczenia**, a następnie wprowadź poświadczenia bazy danych.
  1. Kontynuuj wyrejestrowanie.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Nie mam wystarczających uprawnień do uruchamiania usług systemowych

- **Przyczyna**. Ten błąd występuje w dwóch sytuacjach:
  -   Nazwa użytkownika i/lub hasło są nieprawidłowe.
  -   Określone konto użytkownika nie ma wystarczających uprawnień do logowania się jako usługa.

- **Rozdzielczość**. Udziel poświadczeń logowania jako usługi do konta użytkownika:

  1. Przejdź do **pozycji Uruchom** > **Panel sterowania** > **Narzędzia administracyjne** > Zarządzanie**prawami użytkownika****lokalnymi zasadami zabezpieczeń** > lokalnych **.** > 
  1. Wybierz **pozycję Zaloguj się jako usługę**.
  1. W oknie dialogowym **Właściwości** dodaj konto użytkownika.
  1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  1. Zamknij wszystkie okna.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Baza danych ma stan "Nieaktualne"

- **Przyczyna**. Sql Data Sync usuwa bazy danych, które zostały w trybie offline z usługi przez 45 dni lub więcej (jak liczone od czasu bazy danych przeszedł w tryb offline). Jeśli baza danych jest w trybie offline przez 45 dni lub dłużej, a następnie wraca do trybu online, jej stan jest **nieaktualny**.

- **Rozdzielczość**. Można uniknąć **stanu Nieaktualne,** upewniając się, że żadna z baz danych nie przejdzie do trybu offline przez 45 dni lub dłużej.

  Jeśli stan bazy danych to **Nieaktualne:**

  1. Usuń z grupy synchronizacji bazę danych, która ma stan **Nieaktualny.**
  1. Dodaj bazę danych z powrotem do grupy synchronizacji.

  > [!WARNING]
  > Tracisz wszystkie zmiany wprowadzone do tej bazy danych, gdy była w trybie offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>Grupa synchronizacji ma stan "Nieaktualne"

- **Przyczyna**. Jeśli jedna lub więcej zmian nie będzie obowiązywać przez cały okres przechowywania wynoszący 45 dni, grupa synchronizacji może stać się nieaktualna.

- **Rozdzielczość**. Aby uniknąć stanu **Nieaktualne** dla grupy synchronizacji, regularnie sprawdzaj wyniki zadań synchronizacji w przeglądarce historii. Zbadaj i rozwiąż wszelkie zmiany, które nie można zastosować.

  Jeśli stan grupy synchronizacji to **Nieaktualne,** usuń grupę synchronizacji, a następnie utwórz ją ponownie.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymania agenta

Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymania skojarzonego agenta klienta synchronizacji danych SQL.

- **Rozdzielczość**.

  1. Usuń grupę synchronizacji, gdy skojarzone agenci synchronizacji są w trybie online (zalecane).
  1. Jeśli agent jest w trybie offline, ale jest zainstalowany, przejedź go do trybu online na komputerze lokalnym. Poczekaj, aż stan agenta pojawi się jako **online** w portalu synchronizacji danych SQL. Następnie usuń grupę synchronizacji.
  1. Jeśli agent jest w trybie offline, ponieważ został odinstalowany:  
    a.  Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b.  Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.  
    d. Spróbuj usunąć grupę synchronizacji.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Co się stanie, gdy przywrócę utraconą lub uszkodzoną bazę danych?

Jeśli przywrócisz utraconą lub uszkodzoną bazę danych z kopii zapasowej, może istnieć brak zbieżności danych w grupach synchronizacji, do których należy baza danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat bazy danych SQL, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
