---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z wdrażaniem serwera konfiguracji w celu odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 25e2b488d3b6e7e5cabd1a71d1489efaf01231b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748547"
---
# <a name="troubleshoot-configuration-server-issues"></a>Rozwiązywanie problemów z serwerem konfiguracji

Ten artykuł ułatwia rozwiązywanie problemów podczas wdrażania serwera konfiguracji [usługi Azure Site Recovery](site-recovery-overview.md) i zarządzania nim. Serwer konfiguracji działa jako serwer zarządzania. Użyj serwera konfiguracji, aby skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu usługi Site Recovery. W poniższych sekcjach omówiono najczęstsze błędy, które mogą wystąpić podczas dodawania nowego serwera konfiguracji i zarządzania serwerem konfiguracji.

## <a name="registration-failures"></a>Błędy rejestracji

Komputer źródłowy rejestruje się na serwerze konfiguracji podczas instalowania agenta mobilności. Można debugować wszelkie błędy podczas tego kroku, wykonując następujące wskazówki:

1. Otwórz plik C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Folder ProgramData może być folderem ukrytym. Jeśli nie widzisz folderu ProgramData, w Eksploratorze plików na karcie **Widok** w sekcji **Pokazywal/ukrywanie** zaznacz pole wyboru **Ukryte elementy).** Błędy mogą być spowodowane przez wiele problemów.

2. Wyszukaj ciąg **Nie znaleziono prawidłowego adresu IP**. Jeśli zostanie znaleziony ciąg:
   1. Sprawdź, czy żądany identyfikator hosta jest taki sam jak identyfikator hosta komputera źródłowego.
   2. Sprawdź, czy komputer źródłowy ma co najmniej jeden adres IP przypisany do fizycznej karty sieciowej. Aby rejestracja agenta na serwerze konfiguracji powiodła się, komputer źródłowy musi mieć co najmniej jeden prawidłowy adres IP w wersji 4 przypisany do fizycznej karty sieciowej.
   3. Uruchom jedno z następujących poleceń na komputerze źródłowym, aby uzyskać wszystkie adresy IP komputera źródłowego:
      - Dla systemu Windows:`> ipconfig /all`
      - Dla Linuksa:`# ifconfig -a`

3. Jeśli ciąg **Nie znaleziono prawidłowego adresu IP,** wyszukaj ciąg **Reason=>NULL**. Ten błąd występuje, jeśli komputer źródłowy używa pustego hosta do zarejestrowania się na serwerze konfiguracji. Jeśli zostanie znaleziony ciąg:
    - Po rozwiązaniu problemów postępuj zgodnie z wytycznymi w [zarejestruj komputer źródłowy z serwerem konfiguracji,](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) aby ręcznie ponowić próbę rejestracji.

4. Jeśli nie znaleziono ciągu **Reason=>NULL,** na komputerze źródłowym otwórz plik C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Folder ProgramData może być folderem ukrytym. Jeśli nie widzisz folderu ProgramData, w Eksploratorze plików na karcie **Widok** w sekcji **Pokazywal/ukrywanie** zaznacz pole wyboru **Ukryte elementy).** Błędy mogą być spowodowane przez wiele problemów. 

5. Wyszukaj **żądanie wpisu ciągu: (7) - Nie można połączyć się z serwerem**. Jeśli zostanie znaleziony ciąg:
    1. Rozwiąż problemy z siecią między komputerem źródłowym a serwerem konfiguracji. Sprawdź, czy serwer konfiguracji jest osiągalny z komputera źródłowego za pomocą narzędzi sieciowych, takich jak ping, traceroute lub przeglądarki sieci web. Upewnij się, że komputer źródłowy może dotrzeć do serwera konfiguracji za pośrednictwem portu 443.
    2. Sprawdź, czy reguły zapory na komputerze źródłowym blokują połączenie między komputerem źródłowym a serwerem konfiguracji. Współpracuj z administratorami sieci, aby odblokować wszelkie problemy z połączeniem.
    3. Upewnij się, że foldery wymienione w [folderze Odzyskiwanie witryny są](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) wykluczone z oprogramowania antywirusowego.
    4. Po rozwiązaniu problemów z siecią ponów próbę rejestracji, postępując zgodnie z wytycznymi w [3.](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)

6. Jeśli **żądanie wpisu ciągu: (7) - Nie można połączyć się z serwerem** nie został znaleziony, w tym samym pliku dziennika poszukaj **żądania ciągu: (60) - Certyfikat równorzędny nie może być uwierzytelniony przy użyciu podanych certyfikatów urzędu certyfikacji**. Ten błąd może wystąpić, ponieważ certyfikat serwera konfiguracji wygasł lub komputer źródłowy nie obsługuje protokołów SSL TLS 1.0 lub nowszych. Może również wystąpić, jeśli zapora blokuje komunikację SSL między komputerem źródłowym a serwerem konfiguracji. Jeśli zostanie znaleziony ciąg: 
    1. Aby rozwiązać problem, połącz się z adresem IP serwera konfiguracji za pomocą przeglądarki sieci Web na komputerze źródłowym. Użyj adresu\/ \/ \>IP serwera konfiguracji URI https:<:443/. Upewnij się, że komputer źródłowy może dotrzeć do serwera konfiguracji za pośrednictwem portu 443.
    2. Sprawdź, czy należy dodać lub usunąć reguły zapory na komputerze źródłowym, aby komputer źródłowy miał rozmawiać z serwerem konfiguracji. Ze względu na różnorodność oprogramowania zapory, które mogą być używane, nie możemy wyświetlić listy wszystkich wymaganych konfiguracji zapory. Współpracuj z administratorami sieci, aby odblokować wszelkie problemy z połączeniem.
    3. Upewnij się, że foldery wymienione w [folderze Odzyskiwanie witryny są](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) wykluczone z oprogramowania antywirusowego.  
    4. Po rozwiązaniu problemów ponów próbę rejestracji, wykonując wskazówki w [3.](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)

7. W systemie Linux, jeśli wartość platformy w <\>INSTALLATION_DIR /etc/drscout.conf jest uszkodzona, rejestracja kończy się niepowodzeniem. Aby zidentyfikować ten problem, otwórz plik /var/log/ua_install.log. Wyszukaj **konfigurację przerywania ciągu jako VM_PLATFORM wartość jest null lub nie jest VmWare/Azure**. Platforma powinna być ustawiona na **VmWare** lub **Azure**. Jeśli plik drscout.conf jest uszkodzony, zaleca się [odinstalowanie agenta mobilności,](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a następnie ponowne zainstalowanie agenta mobilności. Jeśli dezinstalacja nie powiedzie się, wykonaj następujące kroki: a. Otwórz plik Installation_Directory/uninstall.sh i skomentuj wywołanie funkcji **StopServices.**
    b. Otwórz plik Installation_Directory/Vx/bin/uninstall.sh i skomentuj wywołanie funkcji **stop_services.**
    d. Otwórz plik Installation_Directory/Fx/uninstall.sh i skomentuj całą sekcję, która próbuje zatrzymać usługę Fx.
    d. [Odinstaluj](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) agenta mobilności. Po pomyślnym odinstalowaniu uruchom ponownie system, a następnie spróbuj ponownie zainstalować agenta mobilności.

## <a name="installation-failure-failed-to-load-accounts"></a>Niepowodzenie instalacji: nie można załadować kont

Ten błąd występuje, gdy usługa nie może odczytać danych z połączenia transportowego podczas instalowania agenta mobilności i rejestrowania się na serwerze konfiguracji. Aby rozwiązać ten problem, upewnij się, że protokół TLS 1.0 jest włączony na komputerze źródłowym.

## <a name="vcenter-discovery-failures"></a>Błędy odnajdowania vCenter

Aby rozwiązać błędy odnajdowania vCenter, dodaj serwer vCenter do ustawień serwera proxy listy byPass. 

- Pobierz narzędzie PsExec [stąd,](https://aka.ms/PsExec) aby uzyskać dostęp do zawartości użytkownika systemu.
- Otwórz program Internet Explorer w zawartości użytkownika systemu, uruchamiając następujący wiersz polecenia psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Dodaj ustawienia serwera proxy w IE i uruchom ponownie usługę tmanssvc.
- Aby skonfigurować ustawienia serwera proxy DRA, uruchom cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Następnie wykonaj DRCONFIGURATOR. EXE /configure /AddBypassUrls [add IP Address/FQDN of vCenter Server provided during **Configure vCenter Server/vSphere ESXi server** step of [Configuration Server deployment](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Zmienianie adresu IP serwera konfiguracji

Zdecydowanie zaleca się, aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP przypisane do serwera konfiguracji są statyczne adresy IP. Nie używaj adresów IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Token SAML jest nieprawidłowy

Aby uniknąć tego błędu, upewnij się, że czas w zegarze systemowym nie różni się od czasu lokalnego o więcej niż 15 minut. Uruchom ponownie instalatora, aby ukończyć rejestrację.

## <a name="failed-to-create-a-certificate"></a>Nie można utworzyć certyfikatu

Nie można utworzyć certyfikatu wymaganego do uwierzytelnienia usługi Site Recovery. Uruchom ponownie instalatora po upewnienie się, że instalacja jest uruchomiona jako administrator lokalny.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Niepowodzenie aktywacji licencji systemu Windows z programu Server Standard EVALUATION do Server Standard

1. W ramach wdrażania serwera konfiguracji za pośrednictwem OVF używana jest licencja ewaluacyjna, która jest ważna przez 180 dni. Musisz aktywować tę Licencję przed jej wygaśnięciem. W przeciwnym razie może to spowodować częste zamykanie serwera konfiguracji, a tym samym powodować utrudnienia w działaniach replikacji.
2. Jeśli nie możesz aktywować licencji systemu Windows, skontaktuj się z [zespołem pomocy technicznej systemu Windows,](https://aka.ms/Windows_Support) aby rozwiązać ten problem.

## <a name="register-source-machine-with-configuration-server"></a>Rejestrowanie komputera źródłowego za pomocą serwera konfiguracyjnego

### <a name="if-the-source-machine-runs-windows"></a>Jeśli na komputerze źródłowym jest uruchamiany system Windows

Uruchom następujące polecenie na komputerze źródłowym:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Ustawienie | Szczegóły
--- | ---
Sposób użycia | UnifiedAgentConfigurator.exe /CSEndPoint <adres\> IP serwera konfiguracji /PassphraseFilePath <ścieżka pliku hasła\>
Dzienniki konfiguracji agenta | Znajduje się w obszarze %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametr obowiązkowy. Określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki pliku UNC lub lokalnego.

### <a name="if-the-source-machine-runs-linux"></a>Jeśli na komputerze źródłowym działa system Linux

Uruchom następujące polecenie na komputerze źródłowym:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Ustawienie | Szczegóły
--- | ---
Sposób użycia | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <adres\> IP serwera konfiguracji -P <ścieżka pliku hasła\>
-i | Parametr obowiązkowy. Określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym jest zapisywane hasło. Użyj dowolnego prawidłowego folderu.

## <a name="unable-to-configure-the-configuration-server"></a>Nie można skonfigurować serwera konfiguracji

Jeśli instalujesz aplikacje inne niż serwer konfiguracji na maszynie wirtualnej, skonfigurowanie głównego obiektu docelowego może być niemożna skonfigurować obiektu docelowego. 

Serwer konfiguracji musi być serwerem jednocelowym i używanie go jako serwera udostępnionego nie jest obsługiwane. 

Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące konfiguracji w [programie Wdrażanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Usuwanie starych wpisów dla elementów chronionych z bazy danych serwera konfiguracji 

Aby usunąć nieświeże chronionego komputera na serwerze konfiguracji, należy wykonać następujące kroki. 
 
1. Aby określić maszynę źródłową i adres IP nieaktualnego wpisu: 

    1. Otwórz linię cmdline MYSQL w trybie administratora. 
    2. Wykonaj następujące polecenia. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Zwraca listę zarejestrowanych maszyn wraz z ich adresami IP i ostatnim biciem serca. Znajdź hosta, który ma nieświeże pary replikacji.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i przejdź do pozycji C:\ProgramData\ASR\home\svsystems\bin. 
4. Aby usunąć dane zarejestrowanych hostów i przestarzałe informacje o wpisie z serwera konfiguracji, uruchom następujące polecenie przy użyciu komputera źródłowego i adresu IP nieaktualnego wpisu. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Jeśli masz wpis serwera źródłowego "OnPrem-VM01" z adresem IP 10.0.0.4, użyj następującego polecenia.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Uruchom ponownie następujące usługi na komputerze źródłowym, aby ponownie zarejestrować się na serwerze konfiguracji. 
 
    - InMage Scout Application Service
    - Inmage Scout VX Agent - Sentinel/Placówka

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uaktualnienie kończy się niepowodzeniem, gdy usługi nie mogą się zatrzymać

Uaktualnienie serwera konfiguracji kończy się niepowodzeniem, gdy niektóre usługi nie są zatrzymywać. 

Aby zidentyfikować problem, przejdź do pozycji C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile na serwerze konfiguracji. Jeśli znajdziesz następujące błędy, skorzystaj z poniższych czynności, aby rozwiązać problem: 

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
- InMage Scout VX Agent – Sentinel/Placówka, 
- Agent usług odzyskiwania platformy Microsoft Azure, 
- Usługa odzyskiwania witryny platformy Microsoft Azure, 
- tmansvc ( tmansvc )
  
Aby zaktualizować serwer konfiguracji, uruchom ponownie [ujednoliconą konfigurację.](service-updates-how-to.md#links-to-currently-supported-update-rollups)

## <a name="azure-active-directory-application-creation-failure"></a>Błąd tworzenia aplikacji usługi Azure Active Directory

Masz niewystarczające uprawnienia do tworzenia aplikacji w usłudze Azure Active Directory (AAD) przy użyciu szablonu [OVA (Open Virtualization Application).](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
)

Aby rozwiązać ten problem, zaloguj się do witryny Azure portal i wykonaj jedną z następujących czynności:

- Poproś o rolę dewelopera aplikacji w udaniu AAD. Aby uzyskać więcej informacji na temat roli dewelopera aplikacji, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Sprawdź, czy **użytkownik może utworzyć** flagę aplikacji jest ustawiona na *true* w UAD. Aby uzyskać więcej informacji, zobacz [Jak: Użyj portalu do utworzenia aplikacji i jednostki usługi Azure AD, która może uzyskiwać dostęp do zasobów.](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Serwer przetwarzania/główny obiekt docelowy nie mogą komunikować się z serwerem konfiguracji 

Moduły serwera przetwarzania (PS) i master target (MT) nie mogą komunikować się z serwerem konfiguracji (CS), a ich stan jest wyświetlany jako niepołączony w witrynie Azure portal.

Zazwyczaj jest to spowodowane błędem w porcie 443. Aby odblokować port i ponownie włączyć komunikację z cs, należy wykonać następujące czynności.

**Sprawdź, czy agent MARS jest wywoływany przez agenta głównego obiektu docelowego**

Aby sprawdzić, czy główny agent docelowy może utworzyć sesję TCP dla adresu IP serwera konfiguracji, poszukaj śledzenia podobnego do następującego w dziennikach agenta głównego obiektu docelowego:

TCP \<Zamień IP na CS IP tutaj>:52739 \<Zamień IP na CS IP tutaj>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT // Zamień IP na CS IP tutaj

Jeśli znajdziesz ślady podobne do następujących w dziennikach agenta MT, agent MT zgłasza błędy na porcie 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Ten błąd można napotkać, gdy inne aplikacje również używają portu 443 lub z powodu ustawienia zapory blokującego port.

Aby rozwiązać ten problem:

- Sprawdź, czy port 443 nie jest zablokowany przez zaporę.
- Jeśli port jest nieosiągalny z powodu innej aplikacji korzystającej z tego portu, zatrzymaj i odinstaluj aplikację.
  - Jeśli zatrzymanie aplikacji nie jest możliwe, skonfiguruj nowy czysty CS.
- Uruchom ponownie serwer konfiguracji.
- Uruchom ponownie usługę IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Serwer konfiguracji nie jest połączony z powodu nieprawidłowych wpisów UUID

Ten błąd może wystąpić, gdy w bazie danych znajduje się wiele wpisów UUID wystąpienia serwera konfiguracji (CS). Ten problem często występuje podczas klonowania maszyny Wirtualnej serwera konfiguracji.

Aby rozwiązać ten problem:

1. Usuń nieświeże/stare maszyny wirtualne CS z vCenter. Aby uzyskać więcej informacji, zobacz [Usuwanie serwerów i wyłącz ochronę](site-recovery-manage-registration-and-protection.md).
2. Zaloguj się do maszyny Wirtualnej serwera konfiguracji i połącz się z bazą danych mysql svsdb1. 
3. Wykonaj następującą kwerendę:

    > [!IMPORTANT]
    >
    > Sprawdź, czy wprowadzasz szczegóły UUID sklonowanego serwera konfiguracji lub przestarzałe wpisy serwera konfiguracji, który nie jest już używany do ochrony maszyn wirtualnych. Wprowadzenie nieprawidłowego UUID spowoduje utratę informacji dla wszystkich istniejących elementów chronionych.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Odśwież stronę portalu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Nieskończona pętla logowania występuje podczas wprowadzania poświadczeń

Po wprowadzeniu poprawnej nazwy użytkownika i hasła na serwerze konfiguracji OVF, azure logowania nadal monitować o poprawne poświadczenia.

Ten problem może wystąpić, gdy czas systemowy jest niepoprawny.

Aby rozwiązać ten problem:

Ustaw prawidłowy czas na komputerze i ponów próbę zalogowania. 
 