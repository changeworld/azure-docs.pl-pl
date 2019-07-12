---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Server
description: Rozwiązywanie problemów z instalacji, rejestracji usługi Azure Backup Server i kopii zapasowej i przywracania obciążeń aplikacji.
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: ee24fe4c1792f1934fcfb87a2481133631de4263
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705061"
---
# <a name="troubleshoot-azure-backup-server"></a>Rozwiązywanie problemów ze składnikiem Azure Backup Server

Rozwiązywanie problemów, które występują podczas korzystania z usługi Azure Backup Server, skorzystaj z informacji w poniższych tabelach.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zalecane jest wykonanie poniżej sprawdzania poprawności, przed rozpoczęciem rozwiązywania problemów z Microsoft Azure Backup Server (MABS):

- [Upewnij się, że Agent usługi Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://aka.ms/AB-A4dp50)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). Jeśli to konieczne, uruchom ponownie i spróbuj ponownie wykonać operację
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://aka.ms/AB-AA4dwtt)
- Jeśli rejestracja kończy się niepowodzeniem, następnie upewnij się, że serwer, na którym chcesz zainstalować serwer usługi Azure Backup nie jest już zarejestrowany w innym magazynie
- Jeśli instalacja wypychana zakończy się niepowodzeniem, sprawdź, czy agent programu DPM jest już obecny. Jeśli tak, odinstaluj agenta i ponów próbę instalacji
- [Upewnij się, że żaden inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://aka.ms/AA4nyr4).<br>
- Upewnij się, że usługa SQL Agent jest uruchomiona i ustawiona w trybie automatycznym na serwerze usługi MAB<br>


## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Rejestrowanie w magazynie | Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. | Zalecana akcja: <br> <ul><li> Pobierz najnowszy plik poświadczeń z magazynu i spróbuj ponownie. <br>(OR)</li> <li> Jeśli poprzednie działanie nie powiodło się, spróbuj pobrać poświadczenia do innego katalogu lokalnego lub Utwórz nowy magazyn. <br>(OR)</li> <li> Spróbuj zaktualizować daty i godziny ustawienia, zgodnie z opisem w [ten blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Sprawdź, czy c:\windows\temp ma więcej niż 65000 plików. Przenoszenie starych plików do innej lokalizacji lub usuwać elementy w folderze Temp. <br>(OR)</li> <li> Sprawdź stan certyfikatów. <br> a. Otwórz **zarządzania certyfikatami komputera** (w Panelu sterowania). <br> b. Rozwiń **osobistych** węzła i jego węzłami podrzędnymi **certyfikaty**.<br> c.  Usuń certyfikat **Windows Azure Tools**. <br> d. Ponów próbę rejestracji w kliencie usługi Azure Backup. <br> (OR) </li> <li> Sprawdź, czy wszystkie zasad grupy w miejscu. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika jest niespójna

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Replika jest niespójna | Sprawdź, czy jest włączona opcja wyboru wykonywanie automatycznej kontroli spójności w Kreatorze grupy ochrony. Aby uzyskać więcej informacji na temat przyczyny niespójności repliki i odpowiednie propozycje artykuł [replika jest niespójna](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> W przypadku kopii zapasowej stanu systemu/BMR Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowany na serwerze chronionym.</li><li> Sprawdź w przypadku problemów związanych z miejscem, w puli magazynów programu DPM na serwerze kopii zapasowej programu DPM/Microsoft Azure i przydzielanie magazynu zgodnie z potrzebami.</li><li> Sprawdź stan usługi kopiowania woluminów w tle na serwerze chronionym. Jeśli jest w stanie wyłączenia, należy ustawić go na uruchamianie ręczne. Uruchom usługę na serwerze. Następnie wróć do konsoli programu DPM/Microsoft Azure Backup Server i rozpoczynać synchronizacji zadanie sprawdzania spójności.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Niepowodzenie tworzenia punktu odzyskiwania w trybie online

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Niepowodzenie tworzenia punktu odzyskiwania w trybie online | **Komunikat o błędzie**: Agent usługi Kopia zapasowa Windows Azure nie może utworzyć migawki wybranego woluminu. <br> **Obejście**: Spróbuj zwiększyć miejsce w woluminie punktu replikę i odzyskiwania.<br> <br> **Komunikat o błędzie**: Agenta usługi Windows Azure Backup nie może połączyć się z usługą OBEngine <br> **Obejście**: Sprawdź, czy OBEngine istnieje na liście uruchomionych usług na komputerze. Jeśli usługa OBEngine nie jest uruchomiona, użyj polecenia "net start OBEngine", aby uruchomić usługę OBEngine. <br> <br> **Komunikat o błędzie**: Nie ustawiono hasła szyfrowania dla tego serwera. Skonfiguruj hasło szyfrowania. <br> **Obejście**: Spróbuj skonfigurować hasło szyfrowania. Jeśli nie powiedzie się, wykonaj następujące czynności: <br> <ol><li>Sprawdź, czy istnieje lokalizację tymczasową. Jest to lokalizacja, który jest wymieniony w rejestrze **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, o nazwie **ScratchLocation** powinna istnieć.</li><li> Jeśli istnieje lokalizację tymczasową, spróbuj ponowne rejestrowanie przy użyciu starego hasła. *Zawsze, gdy hasło szyfrowania można skonfigurować, zapisz go w bezpiecznym miejscu.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>Podane poświadczenia magazynu różnią się od magazynu, który serwer jest zarejestrowany

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Przywracanie | **Kod błędu:** : CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Komunikat o błędzie**: Podane poświadczenia magazynu różnią się od magazynu, który serwer jest zarejestrowany | **Przyczyna**: Ten problem występuje, gdy próbujesz przywrócić pliki na inny serwer z oryginalnego serwera przy użyciu opcji odzyskiwania zewnętrzny program DPM, a serwer, który jest odzyskiwany i oryginalnego serwera, z którym dane są kopie zapasowe nie są skojarzone z tym samym Magazyn usługi odzyskiwania.<br/> <br/>**Obejście** Aby rozwiązać ten problem, upewnij się, zarówno oryginalnego i alternatywny serwer jest zarejestrowany w tym samym magazynie.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Niepowodzenie zadania tworzenia punktu odzyskiwania w trybie online dla maszyn wirtualnych programu VMware

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Niepowodzenie zadania tworzenia punktu odzyskiwania w trybie online dla maszyny Wirtualnej VMware. Program DPM napotkał błąd z serwera VMware podczas próby pobrania informacji śledzenia zmian. Kod błędu - FileFaultFault (identyfikator 33621) |  <ol><li> Resetuj CTK w oprogramowaniu firmy VMware dla maszyn wirtualnych, których to dotyczy.</li> <li>Sprawdź, czy niezależnie od dysku nie jest w miejscu w oprogramowaniu firmy VMware.</li> <li>Zatrzymaj ochronę dla maszyn wirtualnych, których dotyczy problem i ponownie włączyć ochronę za pomocą **Odśwież** przycisku. </li><li>Uruchom DW dla maszyn wirtualnych, których to dotyczy.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM na serwerze

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do serwerów chronionych | Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM, na \<ServerName >. | **Jeśli zalecaną akcję, które są wyświetlane w ramach produktu nie rozwiąże problemu, wykonaj następujące kroki**: <ul><li> Dołączając komputer w niezaufanej domenie, postępuj zgodnie z [te kroki](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> Dołączając komputer w zaufanej domenie, rozwiązywanie problemów przy użyciu kroków opisanych w [ten blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Spróbuj wyłączyć antywirusowe podczas rozwiązywania problemów. Jeśli rozwiązało to problem, należy zmodyfikować ustawień programu antywirusowego zgodnie z sugestią podaną w [w tym artykule](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalator nie może zaktualizować metadanych rejestru

| Operacja | Szczegóły błędu | Obejście |
|-----------|---------------|------------|
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do overusage wykorzystania magazynu. Aby uniknąć tej aktualizacji wpis rejestru przycinania systemu plików ReFS. | Dostosuj klucza rejestru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ustaw wartość Dword na 1. |
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do overusage wykorzystania magazynu. Aby tego uniknąć, zaktualizuj wpis rejestru optymalizacji migawki woluminu. | Utwórz klucz rejestru **Manager\Configuration\VolSnapOptimization\WriteIds ochrony danych SOFTWARE\Microsoft** z wartością pustego ciągu. |

## <a name="registration-and-agent-related-issues"></a>Rejestracja i problemów związanych z agenta

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do serwerów chronionych | Poświadczenia, które są określone dla serwera są nieprawidłowe. | **Jeśli Zalecana akcja, która jest wyświetlana w ramach produktu nie rozwiąże problemu, wykonaj następujące kroki**: <br> Spróbuj ręcznie zainstalować agenta ochrony na serwerze produkcyjnym, jak to określono w [w tym artykule](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Agent usługi Azure Backup nie mogła nawiązać połączenia z usługą Azure Backup (identyfikator: 100050) | Agenta usługi Azure Backup nie mogła nawiązać połączenia z usługą Azure Backup. | **Jeśli Zalecana akcja, która jest wyświetlana w ramach produktu nie rozwiąże problemu, wykonaj następujące kroki**: <br>1. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Spowoduje to otwarcie okna programu Internet Explorer. <br/> 2. Przejdź do **narzędzia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**. <br/> 3. Zmień ustawienia, aby używać serwera proxy. Następnie szczegółowo Serwer proxy serwera.<br/> 4. Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwer proxy zezwalać na te [adresy URL](backup-configure-vault.md#verify-internet-access) i [adresu IP](backup-configure-vault.md#verify-internet-access).|
| Instalowanie agenta usługi Kopia zapasowa Azure nie powiodło się | Instalacja usług Microsoft Azure Recovery Services nie powiodła się. Wszystkie zmiany wprowadzone w systemie przez instalację usługi Microsoft Azure Recovery Services zostały wycofane. (IDENTYFIKATOR: 4024) | Ręcznie zainstaluj agenta platformy Azure.


## <a name="configuring-protection-group"></a>Konfigurowanie grupy ochrony

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie grup ochrony | Program DPM nie mógł wyliczyć składnika aplikacji na chronionym komputerze (nazwa komputera chronionego). | Wybierz **Odśwież** na ekranie konfiguracji ochrony grupy interfejsu użytkownika na odpowiednim poziomie źródła danych/składnika. |
| Konfigurowanie grup ochrony | Nie można skonfigurować ochrony | Jeśli serwer chroniony jest programu SQL server, sprawdź, czy uprawnienia roli sysadmin zostały nadane następującym użytkownikom konto systemowe (Zarządzanie Nt\system) na komputerze chronionym zgodnie z opisem w [w tym artykule](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurowanie grup ochrony | Ma za mało wolnego miejsca w puli magazynów dla tej grupy ochrony. | Dyski, które są dodawane do puli magazynów [partycji nie powinna zawierać](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Usuń wszystkie woluminy istniejące na dyskach. Następnie dodaj je do puli magazynów.|
| Zmiany zasad |Nie można modyfikować zasad tworzenia kopii zapasowej. Błąd: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj ponownie wykonać operację po jakimś czasie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. | **Przyczyna:**<br/>Ten błąd występuje w obszarze trzech warunków: po włączeniu ustawienia zabezpieczeń, gdy użytkownik próbuje zmniejszyć zakres przechowywania poniżej określonej wcześniej wartości minimalne i korzystasz z nieobsługiwanej wersji. (Nieobsługiwane wersje są te poniżej Microsoft Azure Backup Server w wersji 2.0.9052 i serwera usługi Azure Backup, aktualizacja 1). <br/>**Zalecana akcja:**<br/> Aby kontynuować aktualizacje związane z zasadami, należy ustawić okres przechowywania powyżej co najmniej określonego okresu. (Minimalny okres przechowywania jest siedem dni, codziennie, czterech tygodni, przez co tydzień, trzy tygodnie dla co miesiąc lub rok do roku). <br><br>Opcjonalnie inny preferowane podejście jest aktualizacja agenta usługi Kopia zapasowa i serwer usługi Azure Backup, które umożliwia wykorzystywanie wszystkie aktualizacje zabezpieczeń. |

## <a name="backup"></a>Backup

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Wystąpił nieoczekiwany błąd, gdy zadanie było uruchomione. Urządzenie nie jest gotowa. | **Jeśli Zalecana akcja, która jest wyświetlana w ramach produktu nie rozwiąże problemu, wykonaj następujące czynności:** <br> <ul><li>Ustaw obszar magazynu kopii w tle nieograniczona liczba elementów w grupie ochrony, a następnie uruchom sprawdzanie spójności.<br></li> (OR) <li>Spróbuj usunąć istniejącą ochronę grupy i tworzenie wielu nowych grup. Każdej nowej grupy ochrony powinna mieć pojedynczy element w nim.</li></ul> |
| Backup | W przypadku tworzenia kopii zapasowej tylko stanu systemu, należy sprawdzić, czy jest za mało wolnego miejsca na chronionym komputerze do przechowywania kopii zapasowej stanu systemu. | <ol><li>Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowany na komputerze chronionym.</li><li>Sprawdź, czy jest za mało miejsca na komputerze chronionym, stanu systemu. Najprostszym sposobem, aby sprawdzić, czy to jest, aby przejść do komputera chronionego, Otwórz narzędzie Kopia zapasowa systemu Windows Server, przejrzeć wybrane opcje, a następnie wybierz odzyskiwania systemu od ZERA. Interfejs użytkownika następnie informuje, ile miejsca jest wymagana. Otwórz **WSB** > **lokalna kopia zapasowa** > **harmonogram tworzenia kopii zapasowych** > **wybierz konfigurację kopii zapasowej**  >  **Pełny serwer** (rozmiar jest wyświetlany). Ten rozmiar można skorzystać w celu weryfikacji.</li></ol>
| Backup | Tworzenie kopii zapasowej awarii do odzyskiwania systemu od ZERA | Jeśli rozmiar odzyskiwania systemu od ZERA jest duża, przenieść niektórych plików aplikacji do dysku systemu operacyjnego, a następnie spróbuj ponownie. |
| Backup | Możliwość ponownego włączenia ochrony maszyny Wirtualnej VMware na nowym serwerze kopia zapasowa Microsoft Azure nie są wyświetlane jako dostępne do dodania. | Właściwości programu VMware są wskazany w starej, wycofane wystąpienia serwera usługi Microsoft Azure Backup. Aby rozwiązać ten problem:<br><ol><li>W programie VCenter (SC-VMM odpowiednik), przejdź do **Podsumowanie** kartę, a następnie **atrybuty niestandardowe**.</li>  <li>Usuń starą nazwę serwera usługi Microsoft Azure Backup z **DPMServer** wartości.</li>  <li>Wróć do nowego serwera kopia zapasowa Microsoft Azure i modyfikować strony  Po wybraniu **Odśwież** , maszyna wirtualna zostanie wyświetlony przycisk z polem wyboru jako dostępny, aby dodać do ochrony.</li></ol> |
| Backup | Wystąpił błąd podczas uzyskiwania dostępu do folderów udostępnionych/plików | Spróbuj zmodyfikować ustawień programu antywirusowego zgodnie z sugestią podaną w artykule TechNet [uruchamianie oprogramowania antywirusowego na serwerze programu DPM](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Zmień hasło

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Zmień hasło |Ten zabezpieczający numer PIN, który został wprowadzony jest nieprawidłowy. Podaj poprawny zabezpieczający numer PIN do ukończenia tej operacji. |**Przyczyna:**<br/> Ten błąd występuje, gdy wprowadzasz nieprawidłowa lub wygasła zabezpieczający numer PIN podczas wykonywania krytycznej operacji (np. Zmienianie hasła). <br/>**Zalecana akcja:**<br/> Aby zakończyć operację, należy wprowadzić prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services. Następnie przejdź do **ustawienia** > **właściwości** > **Generuj zabezpieczający numer PIN**. Aby zmienić hasło, należy użyć tego numeru PIN. |
| Zmień hasło |Operacja nie powiodła się. ID: 120002 |**Przyczyna:**<br/>Ten błąd występuje, gdy są włączone ustawienia zabezpieczeń lub podczas próby zmiany hasła podczas korzystania z nieobsługiwanej wersji.<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować do wersji minimalnej, która jest 2.0.9052 agenta kopii zapasowej. Należy również zaktualizować usługi Azure Backup Server do co najmniej aktualizacją update 1, a następnie wprowadź prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services. Następnie przejdź do **ustawienia** > **właściwości** > **Generuj zabezpieczający numer PIN**. Aby zmienić hasło, należy użyć tego numeru PIN. |


## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie powiadomienia e-mail przy użyciu konta usługi Office 365 |Identyfikator błędu: 2013| **Przyczyna:**<br> Próba użycia konta usługi Office 365 <br>**Zalecana akcja:**<ol><li> Pierwszą rzeczą, aby upewnić się, jest to, że "Zezwalaj na anonimowe przekazywania na łącznika odbioru" dla serwera programu DPM jest skonfigurowany na serwerze Exchange. Aby uzyskać więcej informacji na temat sposobu konfigurowania tego zobacz [Zezwalaj na anonimowe przekazywania w łączniku usługi odbierać](https://technet.microsoft.com/library/bb232021.aspx) w witrynie TechNet.</li> <li> Jeśli nie używasz wewnętrzny przekazywania protokołu SMTP i należy skonfigurować przy użyciu serwera usługi Office 365, możesz zainstalować program IIS za wystąpienie usługi relay. Konfigurowanie serwera DPM w celu [przekazywania SMTP do usługi O365 za pomocą usług IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **WAŻNE:** Pamiętaj przy użyciu poświadczeń użytkownika\@domena.com formatu i *nie* domena\użytkownik.<br><br><li>Punkt programu DPM, aby użyć nazwy lokalnego serwera SMTP na serwerze, jest to port 587. Wskaż jego adres e-mail użytkownika, który wiadomości e-mail powinny pochodzić z.<li> Nazwę użytkownika i hasło na stronie Instalatora programu DPM SMTP powinien być dla konta domeny w domenie, która znajduje się w programie DPM. </li><br> **UWAGA**: Zmieniając adres serwera SMTP, wprowadzić zmiany, aby nowe ustawienia, zamknij okno Ustawienia, a następnie otwórz go ponownie, upewnij się, że odzwierciedla nową wartość.  Po prostu zmieniając i testowania nie zawsze spowodować nowe ustawienia zaczęły obowiązywać, więc testowania w ten sposób jest najlepszym rozwiązaniem.<br><br>W dowolnym momencie w trakcie tego procesu można wyczyścić te ustawienia zamknięcia konsoli programu DPM, a następnie edytując następujące klucze rejestru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> klucze Usuń SMTPPassword i SMTPUserName**. Można dodać je do interfejsu użytkownika po uruchomieniu go ponownie.
