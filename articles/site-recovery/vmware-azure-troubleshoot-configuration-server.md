---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050799"
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

7. W systemie Linux Jeśli wartość platformy w < INSTALLATION_DIR\>/etc/drscout.conf jest uszkodzony, rejestracja kończy się niepowodzeniem. Aby zidentyfikować ten problem, otwórz plik /var/log/ua_install.log. Wyszukaj ciąg **przerywanie konfiguracji, ponieważ VM_PLATFORM wartość null lub go nie jest VmWare/Azure**. Platforma powinien być ustawiony na **VmWare** lub **Azure**. Jeśli plik drscout.conf jest uszkodzony, zalecamy możesz [Odinstaluj agenta mobilności](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) i ponownie zainstaluj agenta mobilności. Jeśli odinstalowanie kończy się niepowodzeniem, wykonaj następujące czynności:
    1. Otwórz plik Installation_Directory/uninstall.sh i komentarz wywołanie **StopServices** funkcji.
    2. Otwórz plik Installation_Directory/Vx/bin/uninstall.sh i komentarz wywołanie **stop_services** funkcji.
    3. Otwórz plik Installation_Directory/Fx/uninstall.sh i komentarz dla całej sekcji, które podejmuje próbę zatrzymania usługi Fx.
    4. [Odinstaluj](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agenta mobilności. Po pomyślnej dezinstalacji należy ponownie uruchomić system, a następnie spróbuj ponownie zainstalować agenta mobilności.

## <a name="installation-failure-failed-to-load-accounts"></a>Błąd instalacji: Nie można załadować kont

Ten błąd występuje, gdy usługa nie może odczytać danych z połączenia transportowego podczas instalowania agenta mobilności i rejestrowanie serwera konfiguracji. Aby rozwiązać ten problem, upewnij się, że protokół TLS 1.0 jest włączona na maszynie źródłowej.

## <a name="change-the-ip-address-of-the-configuration-server"></a>Zmienianie adresu IP serwera konfiguracji

Zdecydowanie zaleca się, że nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP, które są przypisane do serwera konfiguracji statycznych adresów IP. Nie używaj adresów IP protokołu DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token jest nieprawidłowy

Aby uniknąć tego błędu, upewnij się, że czas zegara systemowego nie jest to różni się od czasu lokalnego o ponad 15 minut. Uruchom ponownie instalatora, aby ukończyć rejestrację.

## <a name="failed-to-create-a-certificate"></a>Nie można utworzyć certyfikatu

Nie można utworzyć certyfikatu, które są wymagane do uwierzytelniania Usługa Site Recovery. Po upewnieniu się, że korzystasz z Instalatora jako administrator lokalny, uruchom ponownie Instalatora.

## <a name="register-the-source-machine-with-the-configuration-server"></a>Zarejestrowanie komputera źródłowego serwera konfiguracji

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
Sposób użycia | /usr/local/ASR/Vx/bin dysku CD<br /><br /> UnifiedAgentConfigurator.sh -i < adres IP serwera konfiguracji\> - P < ścieżka do pliku hasła\>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisywane są hasła. Użyj dowolnej prawidłowy folder.

