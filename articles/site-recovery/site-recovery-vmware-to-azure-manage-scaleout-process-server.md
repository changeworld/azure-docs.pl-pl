---
title: " Zarządzanie serwerem proces skalowania w poziomie w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft"
description: W tym artykule opisano sposób konfigurowania i zarządzania nimi skalowalnego w poziomie serwera przetwarzania w usłudze Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768564"
---
# <a name="manage-a-scale-out-process-server"></a>Zarządzanie serwerem proces skalowania w poziomie

Serwer przetwarzania skalowalnego w poziomie działa jako koordynatora do transferu danych między usługi Site Recovery i infrastruktury lokalnej. W tym artykule opisano, jak można skonfigurować, konfigurowania i zarządzania serwera przetwarzania skalowalnego w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono zalecaną sprzętu, oprogramowania i konfiguracji sieci wymagane do skonfigurowania serwera przetwarzania skalowalnego w poziomie.

> [!NOTE]
> [Planowanie pojemności](site-recovery-capacity-planner.md) jest to ważny krok, aby zapobiec wdrażaniu serwera przetwarzania skalowalnego w poziomie z konfiguracją tego zestawy wymagań obciążenia. Przeczytaj więcej na temat [skalowanie właściwości dla skalowalnego w poziomie serwera procesu](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Pobieranie oprogramowania skalowalnego w poziomie serwera przetwarzania
1. Zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania.
2. Przejdź do **infrastrukturę odzyskiwania lokacji** > **serwery konfiguracji** (w obszarze VMware i maszyn fizycznych).
3. Wybierz serwer konfiguracji, aby przejść do strony szczegółów serwera konfiguracji.
4. Kliknij przycisk **+ serwera przetwarzania** przycisku.
5. W **proces dodawania serwera** wybierz pozycję **wdrażanie skalowalnego w poziomie proces serwera lokalnego** opcję **wybierz, w której chcesz wdrożyć serwer procesu** listy rozwijanej.

  ![Dodaj stronę serwerów](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Kliknij przycisk **Pobierz Instalatora Microsoft Azure Site Recovery Unified** łącze, aby pobrać najnowszą wersję instalacji serwera przetwarzania skalowalnego w poziomie.

  > [!WARNING]
  Wersja serwera przetwarzania skalowalnego w poziomie powinna być większa lub mniejsza niż wersja serwera konfiguracji w swoim środowisku. Prosty sposób, aby zapewnić zgodność wersji ma używać tego samego bitów Instalatora, które ostatnio używane do zainstalowania/aktualizacji serwera konfiguracji.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Instalowanie i rejestrowanie serwera przetwarzania skalowalnego w poziomie z graficznym interfejsem użytkownika
Jeśli masz do skalowania wdrożenia ponad 200 maszyn źródłowych lub całkowita stawki dziennej przenoszenie więcej niż 2 TB, potrzebujesz dodatkowych procesów serwerów do obsługi natężenia ruchu.

Sprawdź [rozmiaru zalecenia dotyczące serwerów procesu](#size-recommendations-for-the-process-server), a następnie wykonaj te instrukcje, aby skonfigurować serwer przetwarzania. Po skonfigurowaniu serwera, można dokonać migracji maszyn źródłowych z niego korzystać.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Instalowanie i rejestrowanie serwera proces skalowania w poziomie przy użyciu wiersza polecenia

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Przykładowe zastosowanie
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Serwer przetwarzania skalowalnego w poziomie Instalator argumenty wiersza polecenia.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Utwórz plik konfiguracji ustawień serwera proxy
Parametr ProxySettingsFilePath przyjmuje pliku jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modyfikowanie ustawień serwera proxy dla serwera przetwarzania skalowalnego w poziomie
1. Zaloguj się do serwera przetwarzania skalowalnego w poziomie.
2. Otwórz okno poleceń programu PowerShell administratora.
3. Uruchom następujące polecenie
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Następnie przejdź do katalogu **%PROGRAMDATA%\ASR\Agent** i uruchom następujące polecenie
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Ponowne rejestrowanie serwera proces skalowania w poziomie
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Następnie otwórz wiersz polecenia administratora.
* Przejdź do katalogu **%PROGRAMDATA%\ASR\Agent** i uruchom polecenie

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Uaktualnianie serwera proces skalowania w poziomie
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Wycofanie z eksploatacji serwera proces skalowania w poziomie
1. Upewnij się, że:
  - Stan połączenia serwera konfiguracji jest pokazywana jako **połączony** w portalu Azure
  - Serwer przetwarzania jest nadal mogły komunikować się z serwerem konfiguracji.
2. Zaloguj się do serwera przetwarzania jako administrator
3. Otwórz Panel sterowania > Program > Odinstaluj programy
4. Odinstaluj programy w kolejności określonej następujące:
  * Serwer procesu/serwera konfiguracji odzyskiwania witryny Microsoft Azure
  * Microsoft Azure Site odzyskiwania konfiguracji serwera zależności
  * Agent usług Microsoft Azure Recovery Services

Może upłynąć w górę do 15 minut do usunięcia serwera przetwarzania do uwzględnienia w portalu Azure.

  > [!NOTE]
  Jeśli serwer przetwarzania nie jest w stanie komunikować się z serwerem konfiguracji (stan połączenia w portalu jest odłączony), a następnie należy wykonać następujące czynności w celu przeczyszczenia serwera konfiguracji.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Wyrejestrowywanie odłączony serwer proces skalowania w poziomie z serwera konfiguracji

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Ustawianie rozmiaru wymagania dotyczące serwera przetwarzania skalowalnego w poziomie

| **Serwer przetwarzania dodatkowe** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * 2 rdzenie @ 2,5 GHz), 8 GB pamięci |300 GB |250 GB lub mniej |Replikowanie maszyn 85 lub mniej. |
|8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz), 12 GB pamięci RAM |600 GB |250 GB do 1 TB |Replikują między 85 150 maszyny. |
|12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) 24 GB pamięci |1 TB |1 TB do 2 TB |Replikują między 150 225 komputerów. |
