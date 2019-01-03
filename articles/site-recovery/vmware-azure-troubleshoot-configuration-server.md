---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 6c8f4fa1fdfdb18d57f001308a6b2105acf9a08d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788858"
---
# <a name="troubleshoot-configuration-server-issues"></a>Rozwiązywanie problemów z konfiguracją serwera

Ten artykuł ułatwia rozwiązywanie problemów dotyczących wdrażania i zarządzania nimi [usługi Azure Site Recovery](site-recovery-overview.md) serwera konfiguracji. Serwer konfiguracji działa jako serwer zarządzania i służy do konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych do platformy Azure przy użyciu Site Recovery. Następujące sekcje w tym artykule omówiono najbardziej typowe błędy widoczne podczas dodawania nowego serwera konfiguracji i zarządzania serwerem konfiguracji.

## <a name="registration-failures"></a>Błędy rejestracji

Maszyna źródłowa rejestruje się za pomocą serwera konfiguracji podczas instalacji agenta mobilności. Wszelkie błędy, w tym kroku może być debugowany, postępując zgodnie z wytycznymi podanymi poniżej:

1. Przejdź do pliku C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. Folder ProgramData może być folderem ukrytym. Jeśli nie można zlokalizować, spróbuj anulować Ukryj folder. Awarii może być wiele problemów.
2. Wyszukaj ciąg "Nieprawidłowy znaleziono żadnego adresu IP". Jeśli ciąg zostanie znaleziony,
    - Sprawdź, czy identyfikator żądanego hosta jest taka sama jak maszyna źródłowa.
    - Maszyna źródłowa powinien mieć co najmniej jeden adres IP przypisany do fizycznej karty Sieciowej o rejestrację agenta przy użyciu CS zakończyło się sukcesem.
    - Uruchom polecenie na maszynie źródłowej `> ipconfig /all` (dla systemu operacyjnego Windows) i `# ifconfig -a` (w systemie operacyjnym Linux) można pobrać wszystkie adresy IP na maszynie źródłowej.
    - Należy pamiętać, że rejestracja agenta wymaga prawidłowego adresu IP v4, przypisane do fizycznej karty sieciowej.
3. Jeśli powyższy ciąg nie zostanie znaleziony, wyszukaj ciąg "Przyczyna" = > "NULL". Jeśli znaleziono,
    - Jeśli maszyna źródłowa używa pusty hosta, jeśli można zarejestrować za pomocą serwera konfiguracji, ten błąd występuje.
    - Po rozwiązaniu problemów, postępuj zgodnie z wytycznymi podanymi [tutaj](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) ręcznie próbę rejestracji.
4. Jeśli powyższy ciąg nie zostanie znaleziony, przejdź do maszyny źródłowej, a w dzienniku C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData może być folderem ukrytym. Jeśli nie można zlokalizować, spróbuj anulować Ukryj folder. Awarii może być wiele problemów. Wyszukaj ciąg "żądania post: (7) — nie można połączyć z serwerem". Jeśli znaleziono,
    - Rozwiązywanie problemów z siecią między maszyny i konfiguracji serwera źródłowego. Sprawdź, czy ten serwer konfiguracji jest dostępny z maszyny źródłowej, za pomocą narzędzi sieci, takie jak ping, polecenie traceroute przeglądarki sieci web itp., upewnij się, że maszyna źródłowa jest dostęp do serwera konfiguracji za pośrednictwem portu 443.
    - Sprawdź, czy istnieją odpowiednie reguły zapory na maszynie źródłowej blokują połączenie między maszyny i konfiguracji serwera źródłowego. Praca z administratorów sieci można odblokować problemy z połączeniem.
    - Upewnij się, folderów, o których wspomniano [tutaj](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczane z oprogramowania antywirusowego.
    - Po rozwiązaniu problemów z siecią, i spróbuj ponownie rejestracji poniższe wskazówki podane [tutaj](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Jeśli nie zostanie znaleziony, w tym samym dziennika wyszukaj ciąg "żądania: (60) — certyfikat elementu równorzędnego nie można uwierzytelnić przy użyciu podanych certyfikatów urzędu certyfikacji. ". Jeśli znaleziono, 
    - Ten błąd może to być spowodowane konfiguracji certyfikat serwera wygasł lub maszyna źródłowa nie obsługuje protokołu TLS 1.0 i powyżej SSL protokoły znajduje się zapora, która blokuje komunikację SSL między maszyny i konfiguracji serwera źródłowego.
    - Aby rozwiązać problem, połączyć się z adresu IP serwera konfiguracji za pomocą przeglądarki sieci web na maszynie źródłowej za pomocą identyfikatora URI https://<CSIPADDRESS>: 443 /. Należy upewnić się, że maszyna źródłowa jest dostęp do serwera konfiguracji za pośrednictwem portu 443.
    - Sprawdź, czy istnieją odpowiednie reguły zapory na maszynie źródłowej być dodana/usunięta dla maszyny źródłowej komunikował się CS. Ponieważ może to być wiele inne oprogramowanie zapory, nie jest możliwe wyświetlić listę w dół konfiguracje wymagane, skontaktuj się z Administratorzy sieci klienta.
    - Upewnij się, folderów, o których wspomniano [tutaj](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczane z oprogramowania antywirusowego.  
    - Po rozwiązaniu problemów, i spróbuj ponownie rejestracji poniższe wskazówki podane [tutaj](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. W systemie Linux Jeśli wartość platforma /etc/drscout.conf < INSTALLATION_DIR > jest uszkodzona, następnie rejestracja zakończy się niepowodzeniem. Aby zidentyfikować, przejdź do /var/log/ua_install.log dziennika. Zawiera ciąg "Trwa przerywanie konfiguracji, ponieważ VM_PLATFORM wartość null lub go nie jest VmWare/platformy Azure." Platforma powinna być równa "VmWare" lub "Azure". Ponieważ plik drscout.conf jest uszkodzony, zaleca się [odinstalować](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agentem mobility agent i zainstaluj ponownie. W przypadku niepowodzenia dezinstalacji, należy wykonać następujące czynności:
    - Otwórz plik Installation_Directory/uninstall.sh i komentarz dla wywołania funkcji *StopServices*
    - Otwórz plik Installation_Directory/Vx/bin/uninstall i Skomentuj wywołanie do funkcji `stop_services`
    - Otwórz plik Installation_Directory/Fx/uninstall i komentarz pełną sekcję, która podejmuje próbę zatrzymania usługi Fx.
    - Teraz spróbuj [odinstalować](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agenta mobilności. Po pomyślnej dezinstalacji należy ponownie uruchomić system i spróbuj ponownie zainstalować agenta.

## <a name="installation-failure---failed-to-load-accounts"></a>Niepowodzenie instalacji — nie można załadować kont

Ten błąd występuje, gdy usługa nie może odczytać danych z połączenia transportowego podczas instalowania agenta mobilności i rejestrowanie serwera konfiguracji. Aby rozwiązać problem, upewnij się, że protokół TLS 1.0 jest włączona na maszynie źródłowej.

## <a name="change-ip-address-of-configuration-server"></a>Zmienianie adresu IP serwera konfiguracji

Zdecydowanie zalecane jest aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, wszystkie adresy IP przypisane do serwera konfiguracji statycznych adresów IP i nie adresy IP protokołu DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token jest nieprawidłowy

Aby uniknąć tego błędu, upewnij się, że czas zegara systemowego nie jest większa niż 15 minut od czasu lokalnego. Uruchom ponownie instalatora, aby ukończyć rejestrację.

## <a name="failed-to-create-certificate"></a>Nie można utworzyć certyfikatu

Nie można utworzyć certyfikatu wymaganego do uwierzytelnienia Site Recovery. Po upewnieniu się, że uruchamiasz Instalatora jako administrator lokalny, uruchom ponownie Instalatora.

## <a name="register-source-machine-with-configuration-server"></a>Zarejestrowanie komputera źródłowego serwera konfiguracji

### <a name="if-source-machine-has-windows-os"></a>Jeśli maszyna źródłowa ma system operacyjny Windows

Uruchom następujące polecenie na maszynie źródłowej

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | / Csendpoint UnifiedAgentConfigurator.exe  <CSIP> /passphrasefilepath <PassphraseFilePath>
Dzienniki konfiguracji agenta | W obszarze % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasło. Użyj dowolnego Nieprawidłowa ścieżka UNC lub ścieżka do pliku lokalnego.

### <a name="if-source-machine-has-linux-os"></a>Jeśli maszyna źródłowa ma systemu operacyjnego Linux

Uruchom następujące polecenie na maszynie źródłowej

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | /usr/local/ASR/Vx/bin dysku CD<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisywane są hasła. Użyj dowolnego prawidłowego folderu