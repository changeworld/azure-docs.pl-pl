---
title: Rozwiązywanie problemów z serwerem kopii zapasowej Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z instalacji, rejestracji serwera kopii zapasowej Azure i tworzenia kopii zapasowej i przywracania obciążeń aplikacji.
services: backup
documentationcenter: ''
author: pvrk
manager: shreeshd
editor: ''
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: db0d3d9ff2b27ac0be86b73926563d226d0e5d59
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-azure-backup-server"></a>Rozwiązywanie problemów ze składnikiem Azure Backup Server

Skorzystaj z informacji w poniższych tabelach do rozwiązywania problemów, które wystąpiły podczas za pomocą serwera usługi Kopia zapasowa Azure.

## <a name="invalid-vault-credentials-provided"></a>Udostępniono nieprawidłowe poświadczenia magazynu 

Aby rozwiązać ten problem, wykonaj [te kroki rozwiązywania problemów](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM na serwerze 

Aby rozwiązać ten problem, wykonaj [te kroki rozwiązywania problemów](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues). 

## <a name="setup-could-not-update-registry-metadata"></a>Instalator nie może zaktualizować rejestru metadanych

Aby rozwiązać ten problem, wykonaj [te kroki rozwiązywania problemów](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).




## <a name="installation-issues"></a>Problemy z instalacją

| Operacja | Szczegóły błędu | Obejście |
|-----------|---------------|------------|
|Instalacja | Instalator nie może zaktualizować rejestru metadanych. Błąd tej aktualizacji może prowadzić do overusage wykorzystania magazynu. Aby tego uniknąć, należy zaktualizować wpisu rejestru przycinanie system plików ReFS. | Dostosuj klucza rejestru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ustaw wartość Dword na 1. |
|Instalacja | Instalator nie może zaktualizować rejestru metadanych. Błąd tej aktualizacji może prowadzić do overusage wykorzystania magazynu. Aby tego uniknąć, należy zaktualizować wpisu rejestru SnapOptimization woluminu. | Utwórz klucz rejestru **Manager\Configuration\VolSnapOptimization\WriteIds ochrony danych SOFTWARE\Microsoft** z wartością pusty ciąg. |
## <a name="registration-and-agent-related-issues"></a>Rejestracja i problemów związanych z agenta
| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Rejestrowanie do magazynu | Udostępniono nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. | Aby naprawić ten błąd: <ul><li> Pobierz najnowszy plik poświadczeń z magazynu i spróbuj ponownie. <br>(OR)</li> <li> Jeśli poprzedniej akcji nie powiodło się, spróbuj pobrać poświadczenia do innego katalogu lokalnego lub Utwórz nowy magazyn. <br>(OR)</li> <li> Spróbuj zaktualizować daty i godziny ustawienia, zgodnie z opisem w [ten blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Sprawdź, czy c:\windows\temp ma więcej niż 65000 plików. Przenieś stare pliki do innej lokalizacji lub usunąć elementy z folderu tymczasowego. <br>(OR)</li> <li> Sprawdź stan certyfikatów. <br> a. Otwórz **zarządzanie certyfikatami komputera** (w Panelu sterowania). <br> b. Rozwiń węzeł **osobistych** węzła i jego podrzędnym **certyfikaty**.<br> c.  Usuń certyfikat **narzędzi systemu Windows Azure**. <br> d. Ponów próbę rejestracji w kliencie usługi Kopia zapasowa Azure. <br> (OR) </li> <li> Sprawdź, czy wszystkie zasad grupy jest w miejscu. </li></ul> |
| Wypychanie agentów na chronionych serwerach | Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agentów DPM \<nazwa_serwera >. | **Jeśli Zalecana akcja, która jest wyświetlana w produkcie nie rozwiąże problemu, wykonaj następujące kroki**: <ul><li> Dołączając komputera z niezaufanej domeny, wykonaj [te kroki](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> Dołączając komputer w zaufanej domenie, rozwiązywanie problemów przy użyciu kroków opisanych w [ten blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Spróbuj wyłączyć antywirusowe podczas rozwiązywania problemów. Jeśli został rozwiązany, zmodyfikuj ustawienia antywirusowe zgodnie z sugestią podaną w [w tym artykule](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |
| Wypychanie agentów na chronionych serwerach | Poświadczenia, które są określone dla serwera są nieprawidłowe. | **Jeśli Zalecana akcja, która jest wyświetlana w produkcie nie rozwiąże problemu, wykonaj następujące kroki**: <br> Spróbuj ręcznie zainstalować agenta ochrony na serwerze produkcyjnym, jak określono w [w tym artykule](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Agent usługi Kopia zapasowa Azure nie może połączyć się z usługą kopia zapasowa Azure (identyfikator: 100050) | Azure Backup Agent nie mógł połączyć się z usługą kopia zapasowa Azure. | **Jeśli Zalecana akcja, która jest wyświetlana w produkcie nie rozwiąże problemu, wykonaj następujące kroki**: <br>1. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Zostanie otwarte okno programu Internet Explorer. <br/> 2. Przejdź do **narzędzia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**. <br/> 3. Sprawdź ustawienia serwera proxy dla konta system. Ustaw Proxy adresu IP i portu. <br/> 4. Zamknij program Internet Explorer.|
| Nie można zainstalować agenta usługi Kopia zapasowa Azure | Instalacja usług odzyskiwania Microsoft Azure nie powiodła się. Wszystkie zmiany wprowadzone w systemie przez instalację usług odzyskiwania Microsoft Azure zostały wycofane. (IDENTYFIKATOR: 4024) | Ręcznie zainstaluj agenta platformy Azure.


## <a name="configuring-protection-group"></a>Konfigurowanie grupy ochrony
| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie grup ochrony | Program DPM nie mógł wyliczyć składnika aplikacji na chronionym komputerze (nazwa komputera chronionego). | Wybierz **Odśwież** na ekranie konfiguracji ochrony grupy interfejsu użytkownika na odpowiednim poziomie źródła danych/składnika. |
| Konfigurowanie grup ochrony | Nie można skonfigurować ochrony | Jeśli serwer chroniony jest programu SQL server, sprawdź, czy uprawnienia roli sysadmin zostały przedstawione konto systemowe (NTAuthority\System) na chronionym komputerze zgodnie z opisem w [w tym artykule](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurowanie grup ochrony | Brak wolnego miejsca w puli magazynu dla tej grupy ochrony. | Dyski, które są dodawane do puli magazynu [partycji nie powinna zawierać](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Usuń wszystkie woluminy istniejące na dyskach. Następnie dodaj je do puli magazynu.|
| Zmiana zasad |Nie można modyfikować zasady tworzenia kopii zapasowej. Błąd: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj ponownie wykonać operację po jakimś czasie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. |**Przyczyna:**<br/>Ten błąd występuje w trzech warunków: po włączeniu ustawienia zabezpieczeń podczas próby zmniejszyć zakres przechowywania, poniżej wartości minimalnej określony wcześniej, i na wystąpieniu nieobsługiwanej wersji. (Wersje nieobsługiwany są te poniżej serwer kopii zapasowej Microsoft Azure w wersji 2.0.9052 i serwer kopii zapasowej Azure update 1). <br/>**Zalecana akcja:**<br/> Aby kontynuować aktualizacji związane z zasadami, Ustaw okres przechowywania powyżej co najmniej podanego okresu. (Minimalny okres przechowywania wynosi siedem dni codziennie, cztery tygodnie, co tydzień, trzy tygodnie dla co miesiąc lub rok dla rocznego.) <br><br>Opcjonalnie innego preferowane podejście jest aktualizacja agenta kopii zapasowej i kopii zapasowej serwera Azure wykorzystać wszystkie aktualizacje zabezpieczeń. |

## <a name="backup"></a>Backup
| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Replika jest niespójna | Sprawdź, czy jest włączona opcja sprawdzania spójności automatyczne w Kreatorze grupy ochrony. Aby uzyskać więcej informacji o przyczynach niespójności repliki i odpowiednie propozycje, zobacz artykuł Microsoft TechNet [replika jest niespójna](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> W przypadku kopii zapasowej stanu systemu/BMR Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowany na serwerze chronionym.</li><li> Sprawdź, czy problemów miejsca w puli magazynu DPM na serwerze DPM/Microsoft Azure Backup i przydzielania magazynu, zgodnie z wymaganiami.</li><li> Sprawdź stan usługi woluminów w tle kopii na serwerze chronionym. Jeśli jest w stanie wyłączenia, skonfigurowany do uruchamiania ręcznego. Uruchom usługę na serwerze. Następnie wróć do konsoli programu DPM/Microsoft Azure Utwórz kopię zapasową serwera i rozpoczynać synchronizacji zadanie sprawdzania spójności.</li></ol>|
| Backup | Wystąpił nieoczekiwany błąd podczas gdy zadanie było uruchomione. Urządzenie nie jest gotowy. | **Zalecana akcja, która jest wyświetlana w produkcie nie rozwiąże problemu, należy wykonać następujące kroki:** <br> <ul><li>Ustawienia obszaru magazynu kopii w tle na nieograniczony dla elementów w grupie ochrony, a następnie uruchom sprawdzanie spójności.<br></li> (OR) <li>Spróbuj usunąć istniejącą ochronę grupy i tworzenie wielu nowych grup. Każdej nowej grupy ochrony należy ma pojedynczy element.</li></ul> |
| Backup | W przypadku wykonywania kopii zapasowej tylko stanu systemu, sprawdź, czy na chronionym komputerze do przechowywania kopii zapasowej stanu systemu jest za mało wolnego miejsca. | <ol><li>Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowany na komputerze chronionym.</li><li>Sprawdź, czy istnieje wystarczająca ilość miejsca na chronionym komputerze dla stanu systemu. Najprostszym sposobem, aby sprawdzić, czy ma to na celu przejdź do komputera chronionego, Otwórz narzędzie Kopia zapasowa systemu Windows Server, kliknij go, wybór, a następnie wybierz odzyskiwania systemu od ZERA. Interfejs użytkownika następnie informuje, ile miejsca jest wymagana. Otwórz **WSB** > **lokalna kopia zapasowa** > **harmonogram tworzenia kopii zapasowych** > **konfiguracjikopiizapasowejwybierz**  >  **Pełny serwer** (rozmiar jest wyświetlane). Użyj tego rozmiaru do weryfikacji.</li></ol>
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się. | Jeśli komunikat o błędzie "systemu Windows Azure Backup Agent nie mógł utworzyć migawki wybranego woluminu" Spróbuj zwiększyć ilość miejsca na woluminie punktu repliki i odzyskiwania.
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się. | Komunikat o błędzie jest wyświetlany komunikat "Systemu Windows Azure Backup Agent nie może połączyć się z usługą OBEngine" Sprawdź, czy OBEngine istnieje na liście z uruchomionymi usługami na komputerze. Jeśli nie działa z usługą OBEngine, użyj polecenia "net start OBEngine", aby uruchomić z usługą OBEngine.
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się. | Jeśli komunikat o błędzie "nie ustawiono hasła szyfrowania dla tego serwera. Skonfiguruj hasło szyfrowania"Spróbuj Konfigurowanie hasło szyfrowania. Jeśli nie powiedzie się, należy wykonać następujące czynności: <br> <ol><li>Sprawdź, czy pliki tymczasowe lokalizacji istnieje. Jest to lokalizacja, w której jest wymieniony w rejestrze **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, o nazwie **ScratchLocation** powinna istnieć.</li><li> Jeśli istnieje lokalizacja pliki tymczasowe, spróbuj ponowne rejestrowanie przy użyciu starego hasła. *Gdy hasło szyfrowania można skonfigurować, zapisz go w bezpiecznej lokalizacji.*</li><ol>
| Backup | Niepowodzenia wykonywania kopii zapasowej BMR | Jeśli rozmiar odzyskiwania systemu od ZERA jest duży, przenieść niektórych plików aplikacji na dysk systemu operacyjnego i spróbuj ponownie. |
| Backup | Możliwość ponownego włączenia ochrony maszyny Wirtualnej VMware na nowym serwerze kopia zapasowa Microsoft Azure nie są wyświetlane jako dostępne do dodania. | Właściwości VMware są odnosi się stary wystąpienie wycofane serwer kopii zapasowej Microsoft Azure. Aby rozwiązać ten problem:<br><ol><li>W programie VCenter (SC-VMM odpowiednik), przejdź do **Podsumowanie** kartę, a następnie **atrybuty niestandardowe**.</li>  <li>Usuwanie starego nazwa serwer kopii zapasowej Microsoft Azure z **DPMServer** wartości.</li>  <li>Wróć na nowym serwerze kopia zapasowa Microsoft Azure i modyfikować strony  Po wybraniu **Odśwież** przycisku, maszyna wirtualna jest wyświetlana z polem wyboru jako dostępny, aby dodać do ochrony.</li></ol> |
| Backup | Błąd podczas uzyskiwania dostępu do udostępnionych plików/folderów | Spróbuj zmodyfikować ustawienia antywirusowe zgodnie z sugestią podaną w artykule TechNet [uruchamianie oprogramowania antywirusowego na serwerze programu DPM](https://technet.microsoft.com/library/hh757911.aspx).|
| Backup | Niepowodzenie zadania tworzenia punktu odzyskiwania w trybie online dla maszyny Wirtualnej VMware. Program DPM napotkał błąd programu VMware podczas próby pobrania informacji śledzenia zmian. ErrorCode — FileFaultFault (identyfikator 33621) |  <ol><li> Resetuj CTK VMware dla odpowiednich maszyn wirtualnych.</li> <li>Sprawdź, czy niezależne dysk nie znajduje się w miejscu VMware.</li> <li>Zatrzymaj ochronę odpowiednich maszyn wirtualnych i Wznów ochronę z **Odśwież** przycisku. </li><li>Uruchom DW dla odpowiednich maszyn wirtualnych.</li></ol>|


## <a name="change-passphrase"></a>Zmień hasło
| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Zmień hasło |Ten zabezpieczający numer PIN, który został wprowadzony jest niepoprawny. Podaj poprawne zabezpieczeń numer PIN, aby wykonać tę operację. |**Przyczyna:**<br/> Ten błąd występuje, gdy wprowadź jest nieprawidłowa lub wygasła zabezpieczający numer PIN, podczas wykonywania krytycznej operacji (np. zmienić hasło). <br/>**Zalecana akcja:**<br/> Aby ukończyć operację, wprowadź prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania. Następnie przejdź do **ustawienia** > **właściwości** > **Generuj kod PIN zabezpieczeń**. Ten numer PIN umożliwia zmianę hasła. |
| Zmień hasło |Operacja nie powiodła się. ID: 120002 |**Przyczyna:**<br/>Ten błąd występuje, gdy ustawienia zabezpieczeń zostały włączone albo podczas próby zmiany hasła podczas korzystania z nieobsługiwanej wersji.<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy zaktualizować agenta kopii zapasowej do minimalnej wersji, która jest 2.0.9052. Należy również zaktualizować serwer kopii zapasowej Azure do co najmniej 1 aktualizacji, a następnie wprowadź prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania. Następnie przejdź do **ustawienia** > **właściwości** > **Generuj kod PIN zabezpieczeń**. Ten numer PIN umożliwia zmianę hasła. |


## <a name="configure-email-notifications"></a>Skonfiguruj powiadomienia e-mail
| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie powiadomień poczty e-mail przy użyciu konta usługi Office 365 |Identyfikator błędu: 2013| **Przyczyna:**<br> Próby użycia konta usługi Office 365 <br>**Zalecana akcja:**<ol><li> Przede wszystkim należy zapewnić jest, że "Zezwalaj na anonimowe przekazywania na łącznika odbioru" dla serwera programu DPM jest skonfigurowany na serwerze Exchange. Aby uzyskać więcej informacji na temat sposobu konfiguracji tego zobacz [Zezwalaj na anonimowe przekazywania łącznika odbierania](http://technet.microsoft.com/library/bb232021.aspx) w witrynie TechNet.</li> <li> Jeśli nie można używać wewnętrznego przekaźnika SMTP i należy skonfigurować za pomocą serwera usługi Office 365, usługi IIS można skonfigurować jako przekaźnik. Konfigurowanie serwera DPM w celu [przekazywania SMTP dla usługi Office 365 za pomocą usług IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **Ważne:** należy użyć user@domain.com format i *nie* domena\użytkownik.<br><br><li>Punkt programu DPM, należy użyć nazwy serwera lokalnego jako serwera SMTP, portu 587. Następnie wskaż na nim adres e-mail użytkownika, który powinna pochodzić wiadomości e-mail.<li> Nazwa użytkownika i hasło na stronie Instalatora programu DPM SMTP powinien być dla konta domeny w domenie, w której program DPM znajduje się na. </li><br> **Uwaga**: zmieniając adresu serwera SMTP, wprowadzić zmiany, aby nowe ustawienia, zamknij okno Ustawienia, a następnie otwórz go, należy upewnić się, odzwierciedla nową wartość.  Po prostu zmieniania i testowania nie może być zawsze powoduje nowe ustawienia zaczęły obowiązywać, dlatego najlepszym rozwiązaniem jest testowanie go w ten sposób.<br><br>W dowolnym momencie w trakcie tego procesu można wyczyścić te ustawienia zamknięcie konsoli programu DPM, a następnie edytując następujące klucze rejestru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> usunąć SMTPPassword i Klucze SMTPUserName**. Można dodać je do interfejsu użytkownika po uruchomieniu go ponownie.
