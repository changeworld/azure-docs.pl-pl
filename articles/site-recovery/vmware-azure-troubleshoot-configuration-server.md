---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 287a4104104c12e33fa2c50c398f422f9e6ea8c5
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418707"
---
# <a name="troubleshoot-configuration-server-issues"></a>Rozwiązywanie problemów z konfiguracją serwera

Ten artykuł ułatwia rozwiązywanie problemów podczas wdrażania i zarządzania nimi [usługi Azure Site Recovery](site-recovery-overview.md) serwera konfiguracji. Serwer konfiguracji działa jako serwer zarządzania. Serwer konfiguracji umożliwia konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure przy użyciu usługi Site Recovery. W poniższych sekcjach omówiono najbardziej typowe błędy, które mogą wystąpić podczas dodawania nowego serwera konfiguracji i zarządzania serwerem konfiguracji.

## <a name="registration-failures"></a>Błędy rejestracji

Po zainstalowaniu agenta mobilności na maszynie źródłowej rejestruje się z serwerem konfiguracji. Możesz przeprowadzić debugowanie błędów w tym kroku, postępując zgodnie z tymi wytycznymi:

1. Otwórz plik C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (ProgramData folder może być folderem ukrytym. Jeśli nie widzisz folder ProgramData, w Eksploratorze plików na **widoku** na karcie **Pokaż/Ukryj** zaznacz **ukryte elementy** pole wyboru.) Awarii może być spowodowany przez wiele problemów.

2. Wyszukaj ciąg **nie można odnaleźć prawidłowego adresu IP**. Jeśli ciąg zostanie znaleziony:
   1. Sprawdź, czy identyfikator żądanego hosta jest taki sam jak identyfikator hosta na maszynie źródłowej.
   2. Sprawdź, czy maszyna źródłowa ma co najmniej jeden adres IP przypisany do fizycznej karty sieciowej. Rejestracji agenta z serwerem konfiguracji zakończyło się sukcesem maszyna źródłowa musi mieć co najmniej jeden prawidłowy adres IPv4 przypisany do fizycznej karty sieciowej.
   3. Uruchom jedno z następujących poleceń na maszynie źródłowej, aby uzyskać wszystkie adresy IP maszyny źródłowej:
      - Aby uzyskać Windows: `> ipconfig /all`
      - Dla systemu Linux: `# ifconfig -a`

3. Jeśli ciąg **nie można odnaleźć prawidłowego adresu IP** nie zostanie znaleziona, wyszukaj ciąg **Przyczyna = > o wartości NULL**. Ten błąd występuje, jeśli maszyna źródłowa używa pusty hosta można zarejestrować za pomocą serwera konfiguracji. Jeśli ciąg zostanie znaleziony:
    - Po rozwiązaniu problemów, postępuj zgodnie z wytycznymi w [zarejestrowania komputera źródłowego z serwerem konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) ręcznie próbę rejestracji.

4. Jeśli ciąg **Przyczyna = > o wartości NULL** nie zostanie odnaleziony na maszynie źródłowej, otwórz plik C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (ProgramData folder może być folderem ukrytym. Jeśli nie widzisz folder ProgramData, w Eksploratorze plików na **widoku** na karcie **Pokaż/Ukryj** zaznacz **ukryte elementy** pole wyboru.) Awarii może być spowodowany przez wiele problemów. 

5. Wyszukaj ciąg **żądania post: (7) — nie można połączyć się z serwerem**. Jeśli ciąg zostanie znaleziony:
    1. Rozwiązywanie problemów z siecią między maszyną źródłową a serwerem konfiguracji. Sprawdź, czy serwer konfiguracji jest dostępny z maszyny źródłowej, za pomocą narzędzia sieciowe, takie jak ping, polecenie traceroute lub przeglądarki sieci web. Upewnij się, że maszyna źródłowa może osiągnąć serwer konfiguracji za pośrednictwem portu 443.
    2. Sprawdź, czy żadnych reguł zapory w bloku maszyny źródłowej połączenie między maszyną źródłową a serwerem konfiguracji. Praca z administratorów sieci można odblokować wszystkie problemy z połączeniem.
    3. Upewnij się, że foldery na liście [wykluczenia folderów Site Recovery z programów antywirusowych](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczane z oprogramowania antywirusowego.
    4. W przypadku rozwiązania problemów z siecią i spróbuj ponownie rejestracji zgodnie z wytycznymi podanymi w [zarejestrowania komputera źródłowego z serwerem konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Jeśli ciąg **żądania post: (7) — nie można połączyć się z serwerem** nie zostanie znaleziona, w tym samym pliku dziennika, poszukaj ciągu **żądania: (60) — certyfikat elementu równorzędnego nie można uwierzytelnić przy użyciu podanych certyfikatów urzędu certyfikacji**. Ten błąd może wystąpić, ponieważ wygasł certyfikat serwera konfiguracji lub na maszynie źródłowej nie obsługuje protokołu TLS 1.0 lub nowsze protokoły SSL. On również może wystąpić, jeśli Zapora blokuje komunikację SSL między maszyną źródłową a serwerem konfiguracji. Jeśli ciąg zostanie znaleziony: 
    1. Aby rozwiązać problem, nawiązać połączenia z serwerem konfiguracji adresu IP za pomocą przeglądarki sieci web na maszynie źródłowej. Za pośrednictwem protokołu https identyfikatora URI:\/\/< adres IP serwera konfiguracji\>: 443 /. Upewnij się, że maszyna źródłowa może osiągnąć serwer konfiguracji za pośrednictwem portu 443.
    2. Sprawdź, czy odpowiednie reguły zapory na maszynie źródłowej muszą być dodane lub usunięte dla maszyny źródłowej komunikował się z serwerem konfiguracji. Z powodu różnych oprogramowanie zapory, które mogą zostać użyte firma Microsoft nie można wyświetlać wszystkie konfiguracje zapory wymagane. Praca z administratorów sieci można odblokować wszystkie problemy z połączeniem.
    3. Upewnij się, że foldery na liście [wykluczenia folderów Site Recovery z programów antywirusowych](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczane z oprogramowania antywirusowego.  
    4. Po rozwiązaniu problemów i spróbuj ponownie rejestracji się następującymi wskazówkami [zarejestrowania komputera źródłowego z serwerem konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. W systemie Linux Jeśli wartość platformy w < INSTALLATION_DIR\>/etc/drscout.conf jest uszkodzony, rejestracja kończy się niepowodzeniem. Aby zidentyfikować ten problem, otwórz plik /var/log/ua_install.log. Wyszukaj ciąg **przerywanie konfiguracji, ponieważ VM_PLATFORM wartość null lub go nie jest VmWare/Azure**. Platforma powinien być ustawiony na **VmWare** lub **Azure**. Jeśli plik drscout.conf jest uszkodzony, zalecamy możesz [Odinstaluj agenta mobilności](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) i ponownie zainstaluj agenta mobilności. Jeśli odinstalowanie kończy się niepowodzeniem, wykonaj następujące czynności:. Otwórz plik Installation_Directory/uninstall.sh i komentarz wywołanie **StopServices** funkcji.
    b. Otwórz plik Installation_Directory/Vx/bin/uninstall.sh i komentarz wywołanie **stop_services** funkcji.
    c. Otwórz plik Installation_Directory/Fx/uninstall.sh i komentarz dla całej sekcji, które podejmuje próbę zatrzymania usługi Fx.
    d. [Odinstaluj](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) agenta mobilności. Po pomyślnej dezinstalacji należy ponownie uruchomić system, a następnie spróbuj ponownie zainstalować agenta mobilności.

## <a name="installation-failure-failed-to-load-accounts"></a>Błąd instalacji: Nie można załadować kont

Ten błąd występuje, gdy usługa nie może odczytać danych z połączenia transportowego podczas instalowania agenta mobilności i rejestrowanie serwera konfiguracji. Aby rozwiązać ten problem, upewnij się, że protokół TLS 1.0 jest włączona na maszynie źródłowej.

## <a name="vcenter-discovery-failures"></a>błędy wykrywania vCenter

Aby rozwiązać błędy wykrywania vCenter, należy dodać serwer vCenter do ustawień serwera proxy listy obejścia. 

- Pobierz narzędzie PsExec z [tutaj](https://aka.ms/PsExec) dostępu do zawartości użytkownika systemu.
- Otwórz program Internet Explorer w systemie użytkownika zawartości, uruchamiając następujące wiersza polecenia programu psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Dodaj ustawienia serwera proxy w programie Internet Explorer, a następnie uruchom ponownie usługę tmanssvc.
- Aby skonfigurować ustawienia serwera proxy DRA, uruchom cd dostawcy C:\Program Files\Microsoft Azure Site Recovery
- Następnie wykonaj DRCONFIGURATOR. Plik EXE / configure /AddBypassUrls [Dodaj adres IP/nazwę FQDN programu vCenter Server oferowane w trakcie **poświadczenia serwera vCenter Server/vSphere ESXi Konfiguruj** kroku [wdrażanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Zmienianie adresu IP serwera konfiguracji

Zdecydowanie zaleca się, że nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP, które są przypisane do serwera konfiguracji statycznych adresów IP. Nie używaj adresów IP protokołu DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token jest nieprawidłowy

Aby uniknąć tego błędu, upewnij się, że czas zegara systemowego nie jest to różni się od czasu lokalnego o ponad 15 minut. Uruchom ponownie instalatora, aby ukończyć rejestrację.

## <a name="failed-to-create-a-certificate"></a>Nie można utworzyć certyfikatu

Nie można utworzyć certyfikatu, które są wymagane do uwierzytelniania Usługa Site Recovery. Po upewnieniu się, że korzystasz z Instalatora jako administrator lokalny, uruchom ponownie Instalatora.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Nie można aktywować licencji Windows z Server Standard w wersji EWALUACYJNEJ do serwera w warstwie standardowa

1. W ramach wdrożenia serwera konfiguracji za pomocą pakietu OVF licencji ewaluacyjnej jest używana, która jest ważna przez 180 dni. Należy aktywować tej licencji, zanim to pobiera wygasł. W przeciwnym wypadku to powodować częste zamykania serwera konfiguracji i powodując przeszkodą dla działania replikacji.
2. Jeśli nie można aktywować licencji Windows skontaktowanie się z [zespołu pomocy technicznej Windows](https://aka.ms/Windows_Support) Aby rozwiązać ten problem.

## <a name="register-source-machine-with-configuration-server"></a>Zarejestrowanie komputera źródłowego serwera konfiguracji

### <a name="if-the-source-machine-runs-windows"></a>Jeśli na maszynie źródłowej jest uruchomiony Windows

Uruchom następujące polecenie na maszynie źródłowej:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Ustawienie | Szczegóły
--- | ---
Sposób użycia | / Csendpoint UnifiedAgentConfigurator.exe < adres IP serwera konfiguracji \> /passphrasefilepath < ścieżka do pliku hasła\>
Dzienniki konfiguracji agenta | Znajduje się w obszarze % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasło. Użyj dowolnego Nieprawidłowa ścieżka UNC lub ścieżka do pliku lokalnego.

### <a name="if-the-source-machine-runs-linux"></a>Jeśli maszyna źródłowa działa w systemie Linux

Uruchom następujące polecenie na maszynie źródłowej:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Ustawienie | Szczegóły
--- | ---
Sposób użycia | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i < adres IP serwera konfiguracji\> - P < ścieżka do pliku hasła\>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisywane są hasła. Użyj dowolnej prawidłowy folder.

## <a name="unable-to-configure-the-configuration-server"></a>Nie można skonfigurować serwer konfiguracji

Po zainstalowaniu aplikacji niezwiązanych z serwera konfiguracji na maszynie wirtualnej, może być nie można skonfigurować głównego elementu docelowego. 

Serwer konfiguracji musi być serwerem jednozadaniowe go i używanie zgodnie z udostępnionego serwera nie jest obsługiwany. 

Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące konfiguracji w [wdrażanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Usuń nieaktualne wpisy dla elementów chronionych z bazy danych serwera konfiguracji 

Aby usunąć stare chronionej maszyny, na serwerze konfiguracji, użyj następujących kroków. 
 
1. Aby określić maszyną źródłową a adres IP przestarzały wpis: 

    1. Otwórz MYSQL cmdline w trybie administratora. 
    2. Wykonaj następujące polecenia. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Spowoduje to zwrócenie listy zarejestrowanych maszyn wraz z ich adresów IP, a ostatni Puls. Znajdź hosta, który zawiera pary starych replikacji.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i przejdź do C:\ProgramData\ASR\home\svsystems\bin. 
4. Aby usunąć szczegóły zarejestrowanych hostów i informacje przestarzały wpis z serwera konfiguracji, uruchom następujące polecenie, używając na maszynie źródłowej i adres IP przestarzały wpis. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Jeśli masz wpis serwera źródłowego "Lokalnego VM01" z adresem ip 10.0.0.4 następnie użyć następującego polecenia.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Uruchom ponownie następujące usługi na maszynie źródłowej, aby ponownie zarejestrować z serwerem konfiguracji. 
 
    - Usługa aplikacji InMage Scout
    - Agent InMage Scout VX Agent — Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uaktualnienie nie powiedzie się, gdy nie można zatrzymać usług

Uaktualnienie serwera konfiguracji nie powiedzie się, gdy niektóre usługi nie zostanie zatrzymana. 

Aby zidentyfikować problem, przejdź do C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile na serwerze konfiguracji. Jeśli okaże się poniższe błędy, należy użyć poniższe kroki, aby rozwiązać ten problem: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Aby rozwiązać ten problem:

Ręcznie zatrzymaj następujące usługi:

- cxprocessserver
- Agent InMage Scout VX Agent — wartownik/posterunek, 
- Agent usług Microsoft Azure Recovery Services, 
- Usługi Microsoft Azure Site Recovery 
- tmansvc
  
Aby zaktualizować serwer konfiguracji, uruchom [ujednoliconej konfiguracji](service-updates-how-to.md#links-to-currently-supported-update-rollups) ponownie.

## <a name="azure-active-directory-application-creation-failure"></a>Błąd tworzenia aplikacji w usłudze Azure Active Directory

Masz niewystarczające uprawnienia do tworzenia aplikacji w usłudze Azure Active Directory (AAD) przy użyciu [Otwórz wirtualizacji aplikacji (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) szablonu.

Aby rozwiązać ten problem, zaloguj się do witryny Azure portal, a następnie wykonaj jedną z następujących czynności:

- Żądanie roli dewelopera aplikacji w usłudze AAD. Aby uzyskać więcej informacji na temat roli dewelopera aplikacji, zobacz [uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Upewnij się, że **użytkownik może utworzyć aplikacji** flaga jest ustawiona na *true* w usłudze AAD. Aby uzyskać więcej informacji, zobacz [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Serwer przetwarzania/główny docelowy są nie można nawiązać komunikacji z serwerem konfiguracji 

Serwer przetwarzania (PS) i moduły Master Target (MT) nie mogą komunikować się z serwerem konfiguracji (CS) i ich stan jest wyświetlany jako niepołączone w witrynie Azure portal.

Zazwyczaj jest to z powodu błędu przy użyciu portu 443. Wykonaj następujące kroki, aby odblokować port i ponownie włączyć komunikację z CS.

**Sprawdź, czy agenta usług MARS jest wywoływana przez agenta głównego serwera docelowego**

Aby sprawdzić, czy wzorzec docelowego agenta można utworzyć sesji protokołu TCP dla adres IP serwera konfiguracji, wyszukaj ślad podobny do następującego w dziennikach agenta główny element docelowy:

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT / / Zastąp pakiet Integracyjny CS tutaj adres IP

Jeśli okaże się ślady podobny do następującego w dziennikach agenta MT, MT Agent zgłasza błędy na porcie 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Ten błąd może wystąpić w sytuacji, gdy inne aplikacje także korzystają z portu 443 lub z powodu ustawień zapory blokuje port.

Aby rozwiązać ten problem:

- Upewnij się, że port 443 nie jest blokowany przez zaporę.
- Jeśli port jest niedostępny z powodu innej aplikacji przy użyciu tego portu, Zatrzymaj i Odinstaluj aplikację.
  - Zatrzymywanie aplikacji nie jest możliwe, należy skonfigurować nowej, czystej CS.
- Uruchom ponownie serwer konfiguracji.
- Uruchom ponownie usługę IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Serwer konfiguracji nie jest połączony ze względu na nieprawidłowe wpisy Identyfikator UUID

Ten błąd może wystąpić, gdy istnieje wiele wpisów UUID wystąpienia konfiguracji serwera (CS) w bazie danych. Ten problem występuje często, podczas klonowania maszyny Wirtualnej serwera konfiguracji.

Aby rozwiązać ten problem:

1. Usuń stare/old CS maszyny Wirtualnej z programu vCenter. Aby uzyskać więcej informacji, zobacz [usuwanie serwerów i wyłączanie ochrony](site-recovery-manage-registration-and-protection.md).
2. Zaloguj się do serwera konfiguracji maszyny Wirtualnej i połączenia z bazą danych MySQL svsdb1. 
3. Wykonaj następujące zapytanie:

    > [!IMPORTANT]
    >
    > Sprawdź, czy wprowadzasz identyfikator UUID szczegóły serwera konfiguracji sklonowane lub przestarzały wpis serwera konfiguracji, który nie jest już używany do ochrony maszyn wirtualnych. Nieprawidłowy identyfikator UUID wprowadzania spowoduje utratę informacji dla wszystkich istniejących elementów chronionych.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Należy odświeżyć stronę portalu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Nieskończona logowania pętli występuje podczas wprowadzania poświadczeń

Po wprowadzeniu prawidłową nazwę użytkownika i hasła na serwerze konfiguracji pakietu OVF, zaloguj się Azure nadal monit o podanie poprawnych poświadczeń.

Ten problem może wystąpić, gdy czas systemowy jest niepoprawny.

Aby rozwiązać ten problem:

Ustaw prawidłowy czas na komputerze i ponów próbę logowania. 
 