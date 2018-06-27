---
title: Rozwiązywanie problemów z synchronizacji danych Azure SQL | Dokumentacja firmy Microsoft
description: Sposoby rozwiązywania typowych problemów z synchronizacją danych SQL Azure.
services: sql-database
ms.date: 06/20/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: daa4ecd3ddf0e770049a81c771a8da52bac5be7f
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021395"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Rozwiązywanie problemów z synchronizacją danych SQL

W tym artykule opisano sposób rozwiązywania znanych problemów z synchronizacją danych SQL Azure. W przypadku rozwiązania problemu, jest podana w tym miejscu.

Omówienie synchronizacji danych SQL, zobacz [synchronizacji danych między wieloma bazami danych chmury i lokalnych z synchronizacji danych SQL Azure](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemy z synchronizacją

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Synchronizacja nie powiedzie się w portalu interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agenta klienta

#### <a name="description-and-symptoms"></a>Opis i objawy

Synchronizacja nie powiedzie się w portalu synchronizacji danych SQL interfejsu użytkownika dla lokalnych baz danych, które są skojarzone z agentem. Na komputerze lokalnym, który jest uruchomiony agent zobacz temat System.IO.IOException błędy w dzienniku zdarzeń. Błędy powiedzieć, że dysk jest za mało miejsca.

#### <a name="resolution"></a>Rozwiązanie

Utwórz więcej miejsca na dysku, na którym znajduje się katalogu % TEMP %.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Moja grupa synchronizacji jest zablokowana w stanie przetwarzania

#### <a name="description-and-symptoms"></a>Opis i objawy

Grupy synchronizacji w synchronizacji danych SQL została w stanie przetwarzania przez długi czas. Nie odpowiada na **zatrzymać** polecenia i dzienniki Pokaż nie nowych wpisów.

#### <a name="cause"></a>Przyczyna

Jeden z następujących warunków może spowodować grupy synchronizacji są zablokowane w stanie przetwarzania:

-   **Agent klienta jest w trybie offline**. Pamiętaj, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

-   **Agent klienta jest niezainstalowana lub Brak**. Jeśli agent klienta jest niezainstalowana lub w inny sposób, Brak:

    1. Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji synchronizacji danych SQL.
    2. Zainstaluj agenta na komputerze lokalnym (może to być taka sama lub innego komputera). Następnie należy przesłać klucz agenta, który jest generowany w portalu dla agenta, który jest wyświetlany w trybie offline.

-   **Usługa synchronizacji danych SQL została zatrzymana**.

    1. W **Start** menu, wyszukaj **usług**.
    2. W wynikach wyszukiwania wybierz **usług**.
    3. Znajdź **synchronizacji danych SQL** usługi.
    4. Jeśli stan usługi jest **zatrzymane**, kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz **Start**.

#### <a name="resolution"></a>Rozwiązanie

Jeśli powyższych informacji grupy synchronizacji nie są przenoszone poza stan przetwarzania, Microsoft Support zresetować stan synchronizacji grupy. Do grupy synchronizacji stanem resetowania, w [forum usługi Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), Utwórz wpis. W w blogu to identyfikator subskrypcji i identyfikator grupy synchronizacji dla grupy, która powinna zostać zresetowana. Inżyniera Microsoft Support będzie odpowiadać ogłoszenie i poinformuje użytkownika, gdy stan został zresetowany.

### <a name="i-see-erroneous-data-in-my-tables"></a>Błędne dane są widoczne w tabeli

#### <a name="description-and-symptoms"></a>Opis i objawy

Jeśli tabele, które mają taką samą nazwę, ale które są z inną bazą danych, schematów są dołączane do synchronizacji, zobaczysz błędnych danych w tabelach po synchronizacji.

#### <a name="cause"></a>Przyczyna

Synchronizacja danych SQL procesie inicjowania obsługi używa tego samego tabel śledzenia dla tabel, które mają taką samą nazwę, ale które są w różnych schematach. W związku z tym zmiany z obu tabel są uwzględniane w tej samej tabeli śledzenia. Powoduje to błędne dane zmian podczas synchronizacji.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że nazwy tabel, które są zaangażowane w synchronizacji różnią się, nawet jeśli tabele należą do różnych schematów w bazie danych.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Niespójne dane klucza podstawowego są widoczne po pomyślnej synchronizacji

#### <a name="description-and-symptoms"></a>Opis i objawy

Synchronizacji został zgłoszony jako powodzenia i dziennik nie zawiera żadnych wierszy nie powiodło się lub zostało pominięte, ale można zaobserwować, że danych kluczy podstawowych jest niezgodny między bazami danych w grupie synchronizacji.

#### <a name="cause"></a>Przyczyna

Ten wynik jest celowe. Zmiany w dowolnej kolumny klucza podstawowego spowodować niespójność danych w wierszach, w którym klucz podstawowy został zmieniony.

#### <a name="resolution"></a>Rozwiązanie

Aby uniknąć tego problemu, upewnij się, że żadne dane w kolumnie klucza podstawowego zostanie zmieniona.

Aby rozwiązać ten problem, po zdarzeniu, usunąć wiersza, który ma niespójne dane ze wszystkich punktów końcowych w grupie synchronizacji. Następnie można ponownie wstawić wiersza.

### <a name="i-see-a-significant-degradation-in-performance"></a>Widać znacznego pogorszenia wydajności

#### <a name="description-and-symptoms"></a>Opis i objawy

Znacznie, prawdopodobnie obniża wydajność do punktu, w którym nie można otworzyć nawet synchronizacji danych interfejsu użytkownika.

#### <a name="cause"></a>Przyczyna

Najbardziej prawdopodobną przyczyną jest pętli synchronizacji. Pętla synchronizacji występuje podczas synchronizacji przez synchronizacji grupy A wyzwala grupy synchronizacji przez synchronizacji B, który następnie wyzwala A. synchronizacji przez synchronizacji grupy Faktycznego mogą być bardziej złożone i mogą obejmować więcej niż dwie grupy synchronizacji w pętli. Problem polega na brak cykliczne wyzwolenie synchronizacji, która jest spowodowany przez grupy synchronizacji nakładających się wzajemnie.

#### <a name="resolution"></a>Rozwiązanie

Najlepiej będzie zapobiegania. Upewnij się, że w Twoich grupach synchronizacji nie ma odwołań cyklicznych. Nie można zsynchronizować dowolnego wiersza, który jest synchronizowany przez jedną grupę synchronizacji przez inną grupę synchronizacji.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Pojawia się następujący komunikat: "nie można wstawić wartości NULL do kolumny \<kolumny\>. Kolumna nie dopuszcza wartości null." Co to znaczy i jak to naprawić? 
Ten komunikat o błędzie wskazuje, że wystąpił jeden z dwóch następujących problemów:
-  Tabela nie ma klucza podstawowego. Aby rozwiązać ten problem, należy dodać do wszystkich tabel, które są synchronizowanie klucza podstawowego.
-  Brak klauzuli WHERE w instrukcji CREATE INDEX. Synchronizacja danych nie obsługuje tego warunku. Aby rozwiązać ten problem, Usuń klauzuli WHERE lub ręcznie wprowadzić zmiany do wszystkich baz danych. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, gdy te same dane jest zsynchronizowany w wielu grupach synchronizacji i śledzi zmiany w związku z tym?
Synchronizacja danych nie obsługuje odwołań cyklicznych. Pamiętaj uniknąć ich. 

## <a name="client-agent-issues"></a>Problemy dotyczące agenta klienta

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Agent klienta zainstalować, odinstalować lub napraw kończy się niepowodzeniem

#### <a name="cause"></a>Przyczyna

Wiele scenariuszy mogą być przyczyną tego błędu. Aby ustalić przyczynę określonego dla tego błędu, należy znaleźć w dziennikach.

#### <a name="resolution"></a>Rozwiązanie

Aby znaleźć określone przyczynę niepowodzenia, generowanie i znaleźć w dziennikach Instalatora Windows. Można włączyć rejestrowanie w wierszu polecenia. Na przykład jeśli pobrany plik AgentServiceSetup.msi LocalAgentHost.msi, generowanie i przejrzyj pliki dziennika przy użyciu następujących wierszy polecenia:

-   Do instalacji: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Aby uzyskać odinstalowuje: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Można również włączyć rejestrowanie dla wszystkich instalacji, które są wykonywane przez Instalatora Windows. W artykule bazy wiedzy Microsoft Knowledge Base [włączania rejestrowania Instalatora Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) zapewnia rozwiązanie jednego kliknięcia, aby włączyć rejestrowanie dla Instalatora Windows. Umożliwia także lokalizacji dzienników.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Moje agenta klienta nie działa po anulować dezinstalacji

#### <a name="description-and-symptoms"></a>Opis i objawy

Agent klienta nie działa, nawet po anulowaniu jej dezinstalacji.

#### <a name="cause"></a>Przyczyna

Dzieje się tak, ponieważ agent klienta synchronizacji danych SQL i nie przechowuje poświadczeń.

#### <a name="resolution"></a>Rozwiązanie

Możesz spróbować tych dwóch rozwiązań:

-   Wprowadź ponownie poświadczenia dla agenta klienta przy użyciu pliku services.msc.
-   Odinstaluj agenta klienta, a następnie zainstalować nową. Pobierz i zainstaluj najnowszą wersję agenta klienta z [Centrum pobierania](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Bazy danych nie jest wymieniona na liście agenta

#### <a name="description-and-symptoms"></a>Opis i objawy

Podczas próby dodania istniejącej bazy danych programu SQL Server do grupy synchronizacji bazy danych nie znajdują się na liście agentów.

#### <a name="cause"></a>Przyczyna

Te scenariusze mogą być przyczyną tego problemu:

-   Grupy klientów i agent synchronizacji znajdują się w różnych centrach danych.
-   Agent klienta listy baz danych nie jest aktualny.

#### <a name="resolution"></a>Rozwiązanie

Rozdzielczość zależy od przyczyny.

- **Grupy klientów i agent synchronizacji znajdują się w różnych centrach danych**

    Agent klienta i grupy synchronizacji musi być w tym samym centrum danych. Aby to skonfigurować, masz dwie opcje:

    -   Utwórz nowy agent w centrum danych, w której znajduje się grupa synchronizacji. Zarejestruj bazy danych z tego agenta.
    -   Usuń bieżącej grupy synchronizacji. Następnie ponownie utwórz grupę synchronizacji w centrum danych, w którym znajduje się agent.

- **Agent klienta listy baz danych nie jest aktualny**

    Zatrzymaj i ponownie uruchom usługę agenta klienta.

    Agent lokalny pobiera listę skojarzonych bazach danych tylko na pierwszym przesłanie klucza agenta. Go nie pobierają listę skojarzonych bazach danych na przesyłanie kluczowych kolejnych agentów. Bazy danych, które są zarejestrowane podczas przenoszenia agenta nie są wyświetlane w oryginalnego wystąpienia agenta.

### <a name="client-agent-doesnt-start-error-1069"></a>Agent klienta nie uruchamia się (błąd 1069)

#### <a name="description-and-symptoms"></a>Opis i objawy

Wykryj, czy agent nie jest uruchomiony na komputerze, który jest hostem serwera SQL. Podczas próby ręcznego uruchomienia agenta, zobacz okno dialogowe, w którym jest wyświetlany komunikat, "Błąd 1069: nie można uruchomić usługi z powodu niepowodzenia logowania."

![Okno dialogowe błędu 1069 synchronizacji danych](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Przyczyna

Prawdopodobną przyczyną tego błędu jest to, że hasła na lokalnym serwerze zmieniła się od czasu utworzenia agenta i hasło agenta.

#### <a name="resolution"></a>Rozwiązanie

Zaktualizuj hasło agenta, aby bieżące hasło serwera:

1. Znajdź usługę agenta klienta synchronizacji danych SQL.  
    a. Wybierz **Start**.  
    b. W polu wyszukiwania wprowadź **services.msc**.  
    c. W wynikach wyszukiwania wybierz **usług**.  
    d. W **usług** okna, przewiń do wpisu dla **SQL Agent synchronizacji danych**.  
2. Kliknij prawym przyciskiem myszy **SQL Agent synchronizacji danych**, a następnie wybierz **zatrzymać**.
3. Kliknij prawym przyciskiem myszy **SQL Agent synchronizacji danych**, a następnie wybierz **właściwości**.
4. Na **właściwości agenta synchronizacji danych SQL**, wybierz pozycję **Zaloguj** kartę.
5. W **hasło** wprowadź hasło.
6. W **Potwierdź hasło** należy ponownie wprowadzić hasło.
7. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
8. W **usług** okna, kliknij prawym przyciskiem myszy **SQL Agent synchronizacji danych** usługi, a następnie kliknij przycisk **Start**.
9. Zamknij **usług** okna.

### <a name="i-cant-submit-the-agent-key"></a>Nie można przesłać klucz agenta

#### <a name="description-and-symptoms"></a>Opis i objawy

Po utworzeniu lub ponownie utworzyć klucz dla agenta, spróbuj przesłać klucz przy użyciu aplikacji SqlAzureDataSyncAgent. Przesłanie nie powiedzie się.

![Okno dialogowe błędu synchronizacji — nie można przesłać klucz agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Przed kontynuowaniem sprawdź następujące warunki:

-   Usługa Windows synchronizacji danych SQL jest uruchomiona.  
-   Konto usługi dla usługi Windows synchronizacji danych SQL ma dostęp do sieci.    
-   Wychodzące port 1433 jest otwarty w regule zapory lokalnej.
-   Lokalny adres ip jest dodawany do serwera lub reguły zapory bazy danych dla bazy danych usługi synchronizacji metadanych.

#### <a name="cause"></a>Przyczyna

Klucz agenta unikatowo identyfikuje każdy agent lokalny. Klucz musi spełniać dwa warunki:

-   Klucz agenta klienta na serwerze synchronizacji danych SQL i komputera lokalnego muszą być identyczne.
-   Klucz agenta klienta mogą być użyte tylko raz.

#### <a name="resolution"></a>Rozwiązanie

Jeśli Twoje agent nie działa, jest on, ponieważ jeden lub oba te warunki nie są spełnione. Aby uzyskać agenta do pracy ponownie:

1. Wygenerowanie nowego klucza.
2. Zastosowanie nowego klucza do agenta.

Aby zastosować nowego klucza do agenta:

1. W Eksploratorze plików przejdź do katalogu instalacji agenta. Domyślny katalog instalacji to C:\\Program Files (x86)\\synchronizacji danych programu Microsoft SQL.
2. Kliknij dwukrotnie nazwę podkatalogu bin.
3. Otwórz aplikację SqlAzureDataSyncAgent.
4. Wybierz **przesłać klucz agenta**.
5. W podanym miejscu Wklej klucz ze Schowka.
6. Kliknij przycisk **OK**.
7. Zamknij program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Nie można usunąć agenta klienta z portalu, jeśli jego skojarzony lokalnymi bazy danych jest nieosiągalny

#### <a name="description-and-symptoms"></a>Opis i objawy

Jeśli lokalny punkt końcowy (to znaczy bazy danych) zarejestrowana w usłudze agenta klienta synchronizacji danych SQL staje się niedostępny, nie można usunąć agenta klienta.

#### <a name="cause"></a>Przyczyna

Nie można usunąć lokalnego agenta, ponieważ jest nieosiągalny bazy danych jest nadal zarejestrowany z agentem. Podczas próby usunięcia agenta, proces usuwania próbuje nawiązać połączenia bazy danych, która kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

Użyj "Wymuś usunięcie" można usunąć bazy danych jest nieosiągalny.

> [!NOTE]
> Jeśli po "Wymuś usunięcie" pozostać tabel metadanych synchronizacji, użyj deprovisioningutil.exe je wyczyścić.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Aplikacja lokalna Agent synchronizacji nie może połączyć się z usługą synchronizacji lokalnej

#### <a name="resolution"></a>Rozwiązanie

Spróbuj wykonać następujące kroki:

1. Zakończyć działanie aplikacji.  
2. Otwórz Panel usług składowych.  
    a. W polu wyszukiwania na pasku zadań, wprowadź **services.msc**.  
    b. W wynikach wyszukiwania kliknij dwukrotnie **usług**.  
3. Zatrzymaj **synchronizacji danych SQL** usługi.
4. Uruchom ponownie **synchronizacji danych SQL** usługi.  
5. Otwórz ponownie aplikację.

## <a name="setup-and-maintenance-issues"></a>Instalacja i konserwacja problemów

### <a name="i-get-a-disk-out-of-space-message"></a>Wyświetlany jest komunikat "dysk brak miejsca"

#### <a name="cause"></a>Przyczyna

Jeśli trzeba usunąć pozostawieniu plików, może zostać wyświetlony komunikat "dysk mało miejsca na". Może to być spowodowane przez oprogramowanie antywirusowe, lub pliki są otwarte, gdy próbą wykonania operacji delete.

#### <a name="resolution"></a>Rozwiązanie

Ręcznie usuń synchronizować pliki, które znajdują się w folderze % temp % (`del \*sync\* /s`). Następnie usuń podkatalogów w folderze % temp %.

> [!IMPORTANT]
> Nie usuwaj żadnych plików, w trakcie synchronizacji.

### <a name="i-cant-delete-my-sync-group"></a>Nie można usunąć mojej grupy synchronizacji

#### <a name="description-and-symptoms"></a>Opis i objawy

Próba usunięcia grupy synchronizacji kończy się niepowodzeniem.

#### <a name="causes"></a>Powoduje, że

Dowolne z poniższych scenariuszy może spowodować awarię, aby usunąć grupę synchronizacji:

-   Agent klienta jest w trybie offline.
-   Agent klienta jest niezainstalowana lub ich brak. 
-   Baza danych jest w trybie offline. 
-   Grupy synchronizacji jest inicjowania obsługi administracyjnej lub synchronizacji. 

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać nie powiodło się usunięcie grupy synchronizacji:

-   Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.
-   Jeśli agent klienta jest niezainstalowana lub w inny sposób, Brak:  
    a. Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji synchronizacji danych SQL.  
    b. Zainstaluj agenta na komputerze lokalnym (może to być taka sama lub innego komputera). Następnie należy przesłać klucz agenta, który jest generowany w portalu dla agenta, który jest wyświetlany w trybie offline.
-   Upewnij się, że usługa synchronizacji danych SQL jest uruchomiona:  
    a. W **Start** menu, wyszukaj **usług**.  
    b. W wynikach wyszukiwania wybierz **usług**.  
    c. Znajdź **synchronizacji danych SQL** usługi.  
    d. Jeśli stan usługi jest **zatrzymane**, kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz **Start**.
-   Upewnij się, że bazy danych SQL i baz danych programu SQL Server są wszystkie online.
-   Poczekaj na zakończenie procesu inicjowania obsługi administracyjnej lub synchronizacji, a następnie ponów usunięcie grupy synchronizacji.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>I nie można wyrejestrować z lokalną bazą danych programu SQL Server

#### <a name="cause"></a>Przyczyna

Prawdopodobnie chcesz wyrejestrować bazy danych, która została już usunięta.

#### <a name="resolution"></a>Rozwiązanie

Aby wyrejestrować lokalną bazą danych programu SQL Server, wybierz bazę danych, a następnie wybierz **Wymuś usunięcie**.

Jeśli ta operacja nie powiedzie się usunąć bazę danych z grupy synchronizacji:

1. Zatrzymaj i ponownie uruchom usługę hosta agenta klienta:  
    a. Wybierz **Start** menu.  
    b. W polu wyszukiwania wprowadź **services.msc**.  
    c. W **programy** sekcji wyszukiwania powoduje okienku kliknij dwukrotnie **usług**.  
    d. Kliknij prawym przyciskiem myszy **synchronizacji danych SQL** usługi.  
    e. Jeśli usługa jest uruchomiona, zatrzymaj ją.  
    f. Kliknij prawym przyciskiem myszy usługę, a następnie wybierz **Start**.  
    g. Sprawdź, czy baza danych jest nadal zarejestrowany. Jeśli nie jest już zarejestrowana, wszystko będzie gotowe. W przeciwnym razie przejdź do następnego kroku.
2. Otwórz aplikację agenta klienta (SqlAzureDataSyncAgent).
3. Wybierz **edytowanie poświadczeń**, a następnie wprowadź poświadczenia dla bazy danych.
4. Przejdź do wyrejestrowania.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>I nie ma wystarczających uprawnień do uruchamiania usług systemowych

#### <a name="cause"></a>Przyczyna

Ten błąd występuje w dwóch sytuacjach:
-   Nazwa użytkownika i/lub hasło są niepoprawne.
-   Określone konto użytkownika nie ma wystarczających uprawnień do logowania się jako usługa.

#### <a name="resolution"></a>Rozwiązanie

Przyznanie dziennika na — jako — usługa poświadczeń dla konta użytkownika:

1. Przejdź do **Start** > **Panel sterowania** > **narzędzia administracyjne** > **zasady zabezpieczeń lokalnych**  >  **Zasad lokalnych** > **praw użytkownika zarządzania**.
2. Wybierz **Zaloguj jako usługa**.
3. W **właściwości** okno dialogowe Dodaj konto użytkownika.
4. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
5. Zamknij wszystkie okna.

### <a name="a-database-has-an-out-of-date-status"></a>Bazy danych ma stan "Przestarzałe"

#### <a name="cause"></a>Przyczyna

Synchronizacja danych SQL spowoduje usunięcie bazy danych, które są w trybie offline z usługi do 45 dni lub więcej (zliczane od momentu przejścia w tryb offline bazy danych). Jeśli bazy danych jest w trybie offline, przez co najmniej 45 dni, a następnie wraca do trybu online, jego stan jest **nieaktualne**.

#### <a name="resolution"></a>Rozwiązanie

Można uniknąć **nieaktualne** stanu, zapewniając, że żadna z baz danych przejścia do trybu offline lub więcej 45 dni.

Jeśli stan bazy danych jest **nieaktualne**:

1. Usuń bazę danych, która ma **nieaktualne** stanu z grupy synchronizacji.
2. Dodawanie bazy danych ponownie do grupy synchronizacji.

> [!WARNING]
> Możesz utracić wszystkie zmiany wprowadzone do tej bazy danych podczas w trybie offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Grupy synchronizacji ma stan "Przestarzałe"

#### <a name="cause"></a>Przyczyna

W przypadku awarii jednego lub kilku zmian do zastosowania w okresie przechowywania całej 45 dni, grupy synchronizacji może stać się nieaktualne.

#### <a name="resolution"></a>Rozwiązanie

Aby uniknąć **nieaktualne** stan grupy synchronizacji, sprawdź wyniki zadań synchronizacji w przeglądarce historii, regularnie. Należy określić i rozwiązać wszelkie zmiany, których nie udało się zastosować.

Jeśli jest w stanie grupy synchronizacji **nieaktualne**, Usuń grupę synchronizacji, a następnie utwórz je ponownie.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Nie można usunąć grupy synchronizacji w ciągu trzech minut Odinstalowywanie lub zatrzymywania agenta

#### <a name="description-and-symptoms"></a>Opis i objawy

Nie można usunąć grupy synchronizacji w ciągu trzech minut Odinstalowywanie lub zatrzymywanie skojarzone agenta klienta synchronizacji danych SQL.

#### <a name="resolution"></a>Rozwiązanie

1. Usuwanie grupy synchronizacji podczas synchronizacji skojarzone agentów są w trybie online (zalecane).
2. Jeśli agent jest w trybie offline, ale jest zainstalowany, przełącz go w trybie online na komputerze lokalnym. Poczekaj, aż stan agenta były wyświetlane jako **Online** w portalu synchronizacji danych SQL. Następnie można usunąć grupy synchronizacji.
3. Jeśli agent jest w trybie offline, ponieważ została ona odinstalowana:  
    a.  Jeśli plik istnieje, usuń plik XML agenta z folderu instalacji synchronizacji danych SQL.  
    b.  Zainstaluj agenta na komputerze lokalnym (może to być taka sama lub innego komputera). Następnie należy przesłać klucz agenta, który jest generowany w portalu dla agenta, który jest wyświetlany w trybie offline.  
    c. Spróbuj usunąć grupy synchronizacji.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Co się stanie, gdy przywrócić utracony lub uszkodzony bazy danych?

W przypadku utraty lub uszkodzenia bazy danych można przywrócić z kopii zapasowej, może być nonconvergence danych w grupach synchronizacji, do których należy bazy danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat synchronizacji danych SQL zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)  
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)  
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)  
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)  
-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:  
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji dotyczących bazy danych SQL zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
