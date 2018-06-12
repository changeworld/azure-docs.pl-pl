---
title: Skonfiguruj serwer przetwarzania na platformie Azure dla maszyny Wirtualnej VMware i powrotu po awarii serwera fizycznego z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować serwer przetwarzania na platformie Azure do powrotu po awarii maszyn wirtualnych platformy Azure do programu VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 3e53954341136a293052f9af755515a5552432fe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300851"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>Konfigurowanie serwerów dodatkowych procesów skalowalności

Domyślnie, w przypadku replikacji maszyn wirtualnych VMware lub serwerów fizycznych do platformy Azure przy użyciu [usługi Site Recovery](site-recovery-overview.md), serwer przetwarzania jest zainstalowany na komputerze z serwerem konfiguracji i służy do koordynowania transferu danych między Site Recovery i infrastruktury lokalnej. Aby zwiększyć pojemność i skalowania w poziomie wdrożenie replikacji, możesz dodać dodatkowe autonomicznych serwerów procesu. W tym artykule opisano, jak to zrobić.

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="capacity-planning"></a>Planowanie pojemności

Upewnij się, że zostało wykonane [planowania pojemności](site-recovery-plan-capacity-vmware.md) replikacji VMware. Ułatwia ustalenie, kiedy należy wdrożyć proces dodatkowych serwerów.

### <a name="sizing-requirements"></a>Wymagania dotyczące zmiany rozmiaru 

Sprawdź wymagania dotyczące rozmiaru podsumowane w tabeli. Ogólnie rzecz biorąc Jeśli masz skalowania wdrożenia do ponad 200 maszyn źródłowych lub łączna codziennie churn — liczba więcej niż 2 TB, należy procesu dodatkowych serwerów do obsługi natężenia ruchu.

| **Serwer przetwarzania dodatkowe** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * 2 rdzenie @ 2,5 GHz), 8 GB pamięci |300 GB |250 GB lub mniej |Replikowanie maszyn 85 lub mniej. |
|8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz), 12 GB pamięci RAM |600 GB |250 GB do 1 TB |Replikują między 85 150 maszyny. |
|12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) 24 GB pamięci |1 TB |1 TB do 2 TB |Replikują między 150 225 komputerów. |

### <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące serwera przetwarzania dodatkowe podsumowano w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Pobierz plik instalacyjny

Pobierz plik instalacyjny serwera przetwarzania w następujący sposób:

1. Zaloguj się do portalu Azure, a następnie przejdź do magazynu usług odzyskiwania.
2. Otwórz **infrastrukturę odzyskiwania lokacji** > **VMWare i maszyn fizycznych** > **serwery konfiguracji** (w obszarze VMware & fizyczna Maszyny).
3. Wybierz serwer konfiguracji, aby przejść do szczegółów serwera. Następnie kliknij przycisk **+ serwera przetwarzania**.
4. W **proces dodawania serwera** >  **wybierz, w której chcesz wdrożyć serwer procesu**, wybierz pozycję **wdrażanie skalowalnego w poziomie proces serwera lokalnego**.

  ![Dodaj stronę serwerów](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kliknij przycisk **pobrać z witryny Microsoft Azure odzyskiwania Unified Instalatora**. Spowoduje to pobranie najnowszej wersji pliku instalacyjnego.

  > [!WARNING]
  Wersji procesu instalacji serwera powinna być taka sama jak, lub starszej niż, wersja serwera konfiguracji ma uruchomiony. Prosty sposób, aby zapewnić zgodność wersji jest użycie tej samej Instalatora, który ostatnio używane do instalowania lub aktualizowania serwera konfiguracji.

## <a name="install-from-the-ui"></a>Zainstaluj z interfejsu użytkownika

Zainstaluj w następujący sposób. Po skonfigurowaniu serwera, można dokonać migracji maszyn źródłowych z niego korzystać.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Zainstaluj z poziomu wiersza polecenia

Zainstaluj, uruchamiając następujące polecenie:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Gdzie parametry wiersza polecenia są następujące:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Na przykład:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Utwórz plik ustawień serwera proxy

Jeśli musisz skonfigurować serwer proxy, plik jest parametr ProxySettingsFilePath przyjmuje jako dane wejściowe. Można utworzyć pliku w następujący sposób i przekaż go jako parametr wejściowy ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [Zarządzanie przetworzyć ustawienia serwera](vmware-azure-manage-process-server.md)
