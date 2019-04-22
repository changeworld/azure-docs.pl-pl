---
title: Skonfigurować serwer przetwarzania na platformie Azure, aby zakończyć się niepowodzeniem w trakcie odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować serwer przetwarzania na platformie Azure, aby zakończyć się niepowodzeniem powrót po awarii z platformy Azure do lokalnych podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: ramamill
ms.openlocfilehash: 6849ffb6fa46365aa775b9410067cb0874c70ef8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59362157"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Skalowanie na potrzeby powrotu po awarii przy użyciu dodatkowych serwerów przetwarzania

Domyślnie, Jeśli replikujesz maszyny wirtualne VMware lub serwery fizyczne do platformy Azure za pomocą [Site Recovery](site-recovery-overview.md), serwer przetwarzania jest zainstalowany na komputerze z serwerem konfiguracji i służy do koordynowania innych transfer danych między Site Recovery i infrastruktury lokalnej. Aby zwiększyć wydajność i skalowanie w poziomie wdrożenie replikacji, możesz dodać autonomicznego dodatkowych serwerów przetwarzania. W tym artykule opisano, jak to zrobić.

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="capacity-planning"></a>Planowanie pojemności

Upewnij się, że zostały wykonane [planowania pojemności](site-recovery-plan-capacity-vmware.md) potrzeby replikacji oprogramowania VMware. Pomaga to identyfikować jak i kiedy należy wdrażać dodatkowych serwerów przetwarzania.

> [!NOTE]
> Korzystanie ze sklonowanym składnika serwera przetwarzania nie jest obsługiwana. Wykonaj kroki opisane w tym artykule, aby każdy PS skalowalnego w poziomie.

### <a name="sizing-requirements"></a>Wymagania w zakresie rozmiaru 

Sprawdź wymagania w zakresie rozmiaru podsumowane w tabeli. Ogólnie rzecz biorąc Jeśli musisz skalować wdrożenie do ponad 200 maszyn źródłowych lub masz łącznie codziennie współczynnik więcej niż 2 TB do zmian danych, potrzebujesz dodatkowych serwerów przetwarzania do obsługi natężenia ruchu.

| **Dodatkowym serwerze przetwarzania** | **Rozmiar dysku w pamięci podręcznej** | **Współczynnik zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci RAM |300 GB |250 GB lub mniej |Replikowanie maszyn 85 lub mniej. |
|8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci RAM |600 GB |250 GB do 1 TB |Replikacja między maszynami 85 150. |
|12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) 24 GB pamięci RAM |1 TB |1 TB do 2 TB |Replikacja między maszynami 150 225. |

W przypadku, gdy każda chronione maszyny źródłowej skonfigurowano 3 dyskami 100 GB.

### <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące dodatkowym serwerze przetwarzania są podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



## <a name="download-installation-file"></a>Pobierz plik instalacyjny

Pobierz plik instalacyjny na serwer przetwarzania w następujący sposób:

1. Zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services.
2. Otwórz **infrastruktura usługi Site Recovery** > **VMWare i maszyn fizycznych** > **serwery konfiguracji** (w ramach programu VMware i fizycznych Maszyny).
3. Wybierz serwer konfiguracji, aby przejść do szczegółów serwera. Następnie kliknij przycisk **+ serwer przetwarzania**.
4. W **serwer Dodaj przetwarzania** >  **wybierz, w której chcesz wdrożyć serwer przetwarzania**, wybierz opcję **Deploy Scale-out serwera przetwarzania w środowisku lokalnym**.

   ![Dodaj stronę serwerów](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kliknij przycisk **pobrać z witryny Microsoft Azure Recovery ujednoliconej konfiguracji**. Spowoduje to pobranie najnowszej wersji pliku instalacyjnego.

   > [!WARNING]
   > Wersja instalacji serwera przetwarzania powinna być taka sama jak, lub starszej niż, wersja serwera konfiguracji zostały uruchomione. Prosty sposób, aby zapewnić zgodność wersji jest użycie tego samego Instalatora, który ostatnio używane do instalowania lub aktualizowania serwera konfiguracji.

## <a name="install-from-the-ui"></a>Zainstaluj w interfejsie użytkownika

Należy zainstalować w następujący sposób. Po skonfigurowaniu serwera, należy przeprowadzić migrację maszyn źródłowych, które z niej korzystać.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalowanie przy użyciu wiersza polecenia

Zainstaluj, uruchamiając następujące polecenie:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Gdzie parametry wiersza polecenia są następujące:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Na przykład:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Tworzenie pliku ustawień serwera proxy

Jeśli potrzebujesz skonfigurować serwer proxy, parametr ProxySettingsFilePath przyjmuje plik jako dane wejściowe. Można utworzyć pliku w następujący sposób i przekaż go jako parametr wejściowy ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [Zarządzanie przetwarzania ustawień serwera](vmware-azure-manage-process-server.md)
