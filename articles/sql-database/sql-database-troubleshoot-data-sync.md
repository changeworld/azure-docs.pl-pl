---
title: Rozwiązywanie problemów z usługi Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe problemy z usługą Azure SQL Data Sync.
services: sql-database
ms.date: 07/16/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 2be6d0321db41772116078d5308824fe8e1b64fd
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113903"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Rozwiązywanie problemów z usługą SQL Data Sync

W tym artykule opisano, jak rozwiązywać problemy z znanych problemów z usługą Azure SQL Data Sync. W przypadku rozwiązywania problemu, jest ona udostępniana w tym miejscu.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemy z synchronizacją

- [Synchronizacja nie powiedzie się w portalu interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta](#sync-fails)

- [Moje grupy synchronizacji jest zablokowany w stanie przetwarzania](#sync-stuck)

- [Widzę błędnych danych w tabeli](#sync-baddata)

- [Widzę niespójność danych klucza podstawowego po pomyślnej synchronizacji](#sync-pkdata)

- [Czy mogę zobaczyć znaczne pogorszenie wydajności](#sync-perf)

- [Pojawia się następujący komunikat: "nie można wstawić wartości NULL w kolumnie <column>. Kolumna nie dopuszcza wartości null." Co to znaczy, i jak to naprawić?](#sync-nulls)

- [Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, kiedy te same dane jest synchronizowana w wielu grupach synchronizacji i ulega ciągłym zmianom w wyniku?](#sync-circ)

### <a name="sync-fails"></a> Synchronizacja nie powiedzie się w portalu interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta

Synchronizacja nie powiedzie się w portalu SQL Data Sync interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta. Na komputerze lokalnym, w którym jest uruchomiony agent zostaną wyświetlone błędy System.IO.IOException w dzienniku zdarzeń. Błędy Załóżmy, że na dysku jest za mało miejsca.

- **Przyczyna**. Na dysku jest za mało miejsca.

- **Rozpoznawanie**. Utwórz więcej miejsca na dysku, na którym znajduje się katalogu % TEMP %.

### <a name="sync-stuck"></a> Moje grupy synchronizacji jest zablokowany w stanie przetwarzania

Grupą synchronizacji w SQL Data Sync był w stanie przetwarzania przez długi czas. Nie odpowiada na **zatrzymać** polecenia i dzienniki przedstawiają żadnych nowych wpisów.

Dowolne z następujących warunków może prowadzić do grupy synchronizacji jest zablokowana w stanie przetwarzania:

- **Przyczyna**. Agent klienta jest w trybie offline

- **Rozpoznawanie**. Pamiętaj, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Agent klienta jest niezainstalowana lub jego brak.

- **Rozpoznawanie**. Jeśli agent klienta jest niezainstalowana lub w inny sposób, Brak:

    1. Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji SQL Data Sync.
    2. Zainstaluj agenta na komputerze lokalnym (może być tym samym lub innym komputerze). Następnie prześlij klucz agenta, który został wygenerowany w portalu dla agenta, który jest wyświetlany w trybie offline.

- **Przyczyna**. Usługa SQL Data Sync jest zatrzymana.

- **Rozpoznawanie**. Uruchom ponownie usługę SQL Data Sync.

    1. W **Start** menu, wyszukaj **usług**.
    2. W wynikach wyszukiwania wybierz **usług**.
    3. Znajdź **SQL Data Sync** usługi.
    4. Jeśli stan usługi **zatrzymane**, kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz **Start**.

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

### <a name="sync-nulls"></a> Pojawia się następujący komunikat: "nie można wstawić wartości NULL w kolumnie <column>. Kolumna nie dopuszcza wartości null." Co to znaczy, i jak to naprawić? 
Ten komunikat o błędzie wskazuje, że wystąpił jeden z dwóch następujących problemów:
-  Tabela nie ma klucza podstawowego. Aby rozwiązać ten problem, należy dodać klucz podstawowy do wszystkich tabel, które synchronizowania.
-  Brak klauzuli WHERE w instrukcji CREATE INDEX. Synchronizacja danych nie obsługuje tego warunku. Aby rozwiązać ten problem, usuń klauzulę WHERE, lub ręcznie wprowadzić zmiany do wszystkich baz danych. 
 
### <a name="sync-circ"></a> Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, kiedy te same dane jest synchronizowana w wielu grupach synchronizacji i ulega ciągłym zmianom w wyniku?
Synchronizacja danych nie obsługuje odwołań cyklicznych. Należy ich unikać. 

## <a name="client-agent-issues"></a>Problemy dotyczące agenta klienta

- [Agent klienta zainstalować, odinstalować lub naprawić kończy się niepowodzeniem](#agent-install)

- [Agent klienta nie działa po anulowaniu dezinstalacji](#agent-uninstall)

- [Moja baza danych nie jest wymienione na liście agenta](#agent-list)

- [Agent klienta nie zaczyna się (błąd 1069)](#agent-start)

- [Nie mogę przesłać klucz agenta](#agent-key)

- [Nie można usunąć agenta klienta z poziomu portalu, jeśli jego skojarzony lokalnej bazy danych jest niedostępny](#agent-delete)

- [Lokalnego agenta synchronizacji aplikacji nie można nawiązać połączenia usługi synchronizacji lokalnych](#agent-connect)

### <a name="agent-install"></a> Agent klienta zainstalować, odinstalować lub naprawić kończy się niepowodzeniem

- **Przyczyna**. Wiele scenariuszy mogą być przyczyną tego błędu. Aby określić konkretną przyczynę tego błędu, sprawdź dzienniki.

- **Rozpoznawanie**. Aby znaleźć określone przyczynę niepowodzenia, należy wygenerować i spójrz na dzienniki Instalatora Windows. Można włączyć rejestrowanie w wierszu polecenia. Na przykład jeśli pobrany plik AgentServiceSetup.msi jest LocalAgentHost.msi, generowanie i przejrzyj pliki dziennika przy użyciu następujących wierszy polecenia:

    -   Dla instalacji: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Aby uzyskać odinstalowuje: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Można również włączyć rejestrowanie dla wszystkich instalacji, które są wykonywane przez Instalatora Windows. Artykuł bazy wiedzy Microsoft Knowledge Base [sposobach włączania rejestrowania zdarzeń Instalatora Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) umożliwia rozwiązanie jednego kliknięcia, aby włączyć rejestrowanie dla Instalatora Windows. Umożliwia także lokalizacją dzienników.

### <a name="agent-uninstall"></a> Agent klienta nie działa po anulowaniu dezinstalacji

Agent klienta nie rozwiąże problemu, nawet po zakończeniu możesz anulować jego dezinstalacji.

- **Przyczyna**. Dzieje się tak, ponieważ agent klienta SQL Data Sync nie przechowuje poświadczeń.

- **Rozpoznawanie**. Możesz wypróbować te dwa rozwiązania:

    -   Wprowadź ponownie poświadczenia dla agenta klienta za pomocą apletu services.msc.
    -   Odinstaluj agenta klienta, a następnie zainstalować nową. Pobierz i zainstaluj najnowszą wersję agenta klienta z [Centrum pobierania](http://go.microsoft.com/fwlink/?linkid=221479).

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

Dowiedz się, że agent nie jest uruchomiona na komputerze, który jest hostem serwera SQL. Podczas próby ręcznego uruchomienia agenta zobaczysz okno dialogowe, która wyświetla komunikat "Błąd 1069: nie można uruchomić usługi z powodu niepowodzenia logowania."

![Okno dialogowe błędu 1069 synchronizacji danych](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Przyczyna**. Prawdopodobną przyczyną wystąpienia tego błędu jest to, że hasło na lokalnym serwerze została zmieniona od czasu utworzenia agenta i hasło agenta.

- **Rozpoznawanie**. Zaktualizuj hasło agenta do swoje bieżące hasło serwera:

  1. Znajdź usługę agenta klienta SQL Data Sync.  
    a. Wybierz **Start**.  
    b. W polu wyszukiwania wprowadź **services.msc**.  
    c. W wynikach wyszukiwania wybierz **usług**.  
    d. W **usług** okna, przewiń do wpisu dla **agenta synchronizacji danych programu SQL**.  
  2. Kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL**, a następnie wybierz pozycję **zatrzymać**.
  3. Kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL**, a następnie wybierz pozycję **właściwości**.
  4. Na **właściwości agenta synchronizacji danych programu SQL**, wybierz opcję **Zaloguj** kartę.
  5. W **hasło** wprowadź hasło.
  6. W **Potwierdź hasło** pole, wprowadź ponownie hasło.
  7. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  8. W **usług** okna, kliknij prawym przyciskiem myszy **agenta synchronizacji danych programu SQL** usługi, a następnie kliknij przycisk **Start**.
  9. Zamknij **usług** okna.

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
  2. Zastosuj nowy klucz do agenta.

  Aby zastosować nowy klucz na agencie:

  1. W Eksploratorze plików przejdź do katalogu instalacji agenta. Domyślny katalog instalacji to C:\\Program Files (x86)\\programu Microsoft SQL Data Sync.
  2. Kliknij dwukrotnie ikonę podkatalogu bin.
  3. Otwórz aplikację SqlAzureDataSyncAgent.
  4. Wybierz **przesłać klucz agenta**.
  5. W podanym miejscu Wklej klucz ze Schowka.
  6. Kliknij przycisk **OK**.
  7. Zamknij program.

### <a name="agent-delete"></a> Nie można usunąć agenta klienta z poziomu portalu, jeśli jego skojarzony lokalnej bazy danych jest niedostępny

Jeśli lokalny punkt końcowy (czyli bazy danych) zarejestrowana w usłudze SQL Data Sync agenta klienta staje się niedostępny, nie można usunąć agenta klienta.

- **Przyczyna**. Nie można usunąć lokalny agent, ponieważ nieosiągalny bazy danych jest nadal zarejestrowany za pośrednictwem agenta. Podczas próby usunięcia agenta proces usuwania próbuje nawiązać połączenie bazy danych, która kończy się niepowodzeniem.

- **Rozpoznawanie**. Użyj "wymuszonego usunięcia" można usunąć bazy danych jest nieosiągalny.

> [!NOTE]
> Jeśli tabele metadanych synchronizacji pozostają po "wymuszonego usunięcia", użyj `deprovisioningutil.exe` pora to oczyścić.

### <a name="agent-connect"></a> Lokalnego agenta synchronizacji aplikacji nie można nawiązać połączenia usługi synchronizacji lokalnych

- **Rozpoznawanie**. Spróbuj wykonać następujące kroki:

  1. Zakończ działanie aplikacji.  
  2. Otwórz Panel usługi składnika.  
    a. W polu wyszukiwania na pasku zadań, wprowadź **services.msc**.  
    b. W wynikach wyszukiwania, kliknij dwukrotnie **usług**.  
  3. Zatrzymaj **SQL Data Sync** usługi.
  4. Uruchom ponownie **SQL Data Sync** usługi.  
  5. Otwórz ponownie aplikację.

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

- **Przyczyna**. Agent klienta jest niezainstalowana lub jego brak.

- **Rozpoznawanie**. Jeśli agent klienta jest niezainstalowana lub w inny sposób, Brak:  
    a. Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji SQL Data Sync.  
    b. Zainstaluj agenta na komputerze lokalnym (może być tym samym lub innym komputerze). Następnie prześlij klucz agenta, który został wygenerowany w portalu dla agenta, który jest wyświetlany w trybie offline.

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
  2. Otwórz aplikację agenta klienta (SqlAzureDataSyncAgent).
  3. Wybierz **edytowanie poświadczeń**, a następnie wprowadź poświadczenia dla bazy danych.
  4. Przejdź do wyrejestrowania.

### <a name="setup-perms"></a> I nie ma wystarczających uprawnień do uruchamiania usług systemowych

- **Przyczyna**. Ten błąd występuje w dwóch sytuacjach:
  -   Nazwa użytkownika i/lub hasło są niepoprawne.
  -   Określone konto użytkownika nie ma wystarczających uprawnień, aby zalogować się jako usługa.

- **Rozpoznawanie**. Przyznanie poświadczeń log na as-a-service, aby konto użytkownika:

  1. Przejdź do **Start** > **Panel sterowania** > **narzędzia administracyjne** > **zasady zabezpieczeń lokalnych**  >  **Zasady lokalne** > **użytkownika usługi Rights Management**.
  2. Wybierz **Zaloguj się jako usługa**.
  3. W **właściwości** okna dialogowego Dodaj konto użytkownika.
  4. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  5. Zamknij wszystkie okna.

### <a name="setup-date"></a> Bazy danych ma stan "Przestarzałe"

- **Przyczyna**. SQL Data Sync spowoduje usunięcie bazy danych, które są w trybie offline z usługi w ciągu 45 dni lub więcej (zliczane od chwili, gdy baza danych przeszedł do trybu offline). Jeśli bazy danych jest w trybie offline, przez co najmniej 45 dni, a następnie powróci do trybu online, jego stan to **nieaktualne**.

- **Rozpoznawanie**. Możesz uniknąć **nieaktualne** stanu, zapewniając, że żadna z baz danych do trybu offline co najmniej 45 dni.

  Jeśli stan bazy danych jest **nieaktualne**:

  1. Usuń bazę danych, która ma **nieaktualne** stan z grupy synchronizacji.
  2. Dodawanie bazy danych ponownie do grupy synchronizacji.

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
  2. Jeśli agent jest w trybie offline, ale jest zainstalowany, przenieś ją w trybie online na komputerze lokalnym. Poczekaj, aż stan agenta, aby pojawiało się jako **Online** w portalu usługi SQL Data Sync. Następnie można usunąć grupy synchronizacji.
  3. Jeśli agent jest w trybie offline, ponieważ zostało odinstalowane:  
    a.  Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji SQL Data Sync.  
    b.  Zainstaluj agenta na komputerze lokalnym (może być tym samym lub innym komputerze). Następnie prześlij klucz agenta, który został wygenerowany w portalu dla agenta, który jest wyświetlany w trybie offline.  
    c. Spróbuj usunąć grupę synchronizacji.

### <a name="setup-restore"></a> Co się stanie, gdy przywrócić utracony lub uszkodzony bazy danych?

W przypadku utraty lub uszkodzenia bazy danych jest przywrócenie z kopii zapasowej, może to być rozbieżności danych w grupy synchronizacji, do których należy bazy danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat SQL Data Sync zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)  
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)  
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)  
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)  
-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:  
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji o usłudze SQL Database zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
