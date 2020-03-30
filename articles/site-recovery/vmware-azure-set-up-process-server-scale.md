---
title: Konfigurowanie serwera procesów skalowanych w poziomie podczas odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania serwera procesów skalowanych w poziomie podczas odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257149"
---
# <a name="scale-with-additional-process-servers"></a>Skalowanie za pomocą dodatkowych serwerów przetwarzania

Domyślnie podczas replikowania maszyn wirtualnych vmware lub serwerów fizycznych na platformę Azure przy użyciu [usługi Site Recovery](site-recovery-overview.md)serwer przetwarzania jest zainstalowany na komputerze serwera konfiguracji i jest używany do koordynowania transferu danych między odzyskiem lokacji a infrastrukturą lokalną. Aby zwiększyć pojemność i skalować w poziomie wdrożenia replikacji, można dodać dodatkowe autonomiczne serwery procesów. W tym artykule opisano sposób konfigurowania serwera procesów skalowanych w poziomie.

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="capacity-planning"></a>Planowanie pojemności

Upewnij się, że wykonano [planowanie pojemności](site-recovery-plan-capacity-vmware.md) replikacji VMware. Pomaga to zidentyfikować sposób i kiedy należy wdrożyć dodatkowe serwery procesów.

Od wersji 9.24 wskazówki są dodawane podczas wyboru serwera przetwarzania dla nowych replikacji. Serwer przetwarzania zostanie oznaczony jako Zdrowy, Ostrzeżenie i Krytyczny na podstawie określonych kryteriów. Aby zrozumieć różne scenariusze, które mogą mieć wpływ na stan serwera przetwarzania, przejrzyj [alerty serwera procesów](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Użycie sklonowanego składnika process server nie jest obsługiwane. Wykonaj kroki opisane w tym artykule dla każdego ps skalowania w poziomie.

### <a name="sizing-requirements"></a>Wymagania dotyczące rozmiaru 

Sprawdź wymagania dotyczące rozmiaru podsumowane w tabeli. Ogólnie rzecz biorąc, jeśli trzeba skalować wdrożenia do ponad 200 komputerów źródłowych lub masz całkowity dzienny współczynnik zmian więcej niż 2 TB, potrzebujesz dodatkowych serwerów przetwarzania do obsługi woluminu ruchu.

| **Dodatkowy serwer przetwarzania** | **Rozmiar dysku w pamięci podręcznej** | **Szybkość zmiany danych** | **Chronione maszyny** |
| --- | --- | --- | --- |
|4 procesory wirtualne (2 gniazda \@ * 2 rdzenie 2,5 GHz), pamięć 8 GB |300 GB |250 GB lub mniej |Replikacja 85 maszyn lub mniej. |
|8 procesorów wirtualnych (2 gniazda \@ * 4 rdzenie 2,5 GHz), 12 GB pamięci |600 GB |250 GB do 1 TB |Replikacja między 85-150 maszyn. |
|12 procesorów wirtualnych (2 gniazda \@ * 6 rdzeni 2,5 GHz) 24 GB pamięci |1 TB |1 TB do 2 TB |Replikacja między 150-225 maszyn. |

Gdzie każdy chroniony komputer źródłowy jest skonfigurowany z 3 dyskami po 100 GB każdy.

### <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dla dodatkowego serwera przetwarzania są podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Pobierz plik instalacyjny

Pobierz plik instalacyjny serwera przetwarzania w następujący sposób:

1. Zaloguj się do witryny Azure portal i przejdź do magazynu usług odzyskiwania.
2. Otwórz **infrastrukturę odzyskiwania witryn** > **vmware i** > **serwery konfiguracji** maszyn fizycznych (w obszarze Dla maszyn wirtualnych & komputerów fizycznych).
3. Wybierz serwer konfiguracji, aby przejść do szczegółów serwera. Następnie kliknij **przycisk + Serwer przetwarzania**.
4. W **obszarze Dodaj serwer** >  procesu**Wybierz miejsce, w którym chcesz wdrożyć serwer przetwarzania,** wybierz pozycję **Wdrażanie lokalnego serwera przetwarzania skalowaj**w poziomie .

   ![Strona Dodawanie serwerów](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kliknij **pozycję Pobierz ujednoliconą instalację usługi Microsoft Azure Site Recovery**. Spowoduje to pobranie najnowszej wersji pliku instalacyjnego.

   > [!WARNING]
   > Wersja instalacyjna serwera przetwarzania powinna być taka sama jak lub wcześniejsza niż wersja serwera konfiguracji, którą masz uruchomione. Prostym sposobem zapewnienia zgodności wersji jest użycie tego samego instalatora, który był ostatnio używany do instalowania lub aktualizowania serwera konfiguracji.

## <a name="install-from-the-ui"></a>Zainstaluj z interfejsu użytkownika

Zainstaluj w następujący sposób. Po skonfigurowaniu serwera należy przeprowadzić migrację komputerów źródłowych w celu jego użycia.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalacja z wiersza polecenia

Zainstaluj, uruchamiając następujące polecenie:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Gdzie parametry wiersza polecenia są następujące:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Przykład:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Tworzenie pliku ustawień serwera proxy

Jeśli chcesz skonfigurować serwer proxy, parametr ProxySettingsFilePath przyjmuje plik jako dane wejściowe. Można utworzyć plik w następujący sposób i przekazać go jako wejściowy parametr ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zarządzaniu ustawieniami serwera przetwarzania](vmware-azure-manage-process-server.md)
