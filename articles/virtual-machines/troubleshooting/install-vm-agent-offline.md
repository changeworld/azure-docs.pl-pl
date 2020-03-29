---
title: Instalowanie agenta maszyny wirtualnej platformy Azure w trybie offline | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować agenta maszyny wirtualnej platformy Azure w trybie offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920862"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalowanie agenta maszyny wirtualnej platformy Azure w trybie offline 

Agent maszyny wirtualnej platformy Azure (Agent maszyny wirtualnej) udostępnia przydatne funkcje, takie jak resetowanie hasła administratora lokalnego i wypychanie skryptów. W tym artykule pokazano, jak zainstalować agenta maszyny wirtualnej dla maszyny wirtualnej systemu Windows w trybie offline. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kiedy używać agenta maszyny Wirtualnej w trybie offline

Zainstaluj agenta maszyny Wirtualnej w trybie offline w następujących scenariuszach:

- Wdrożona maszyna wirtualna platformy Azure nie ma zainstalowanego agenta maszyny Wirtualnej lub agent nie działa.
- Nie pamiętasz hasła administratora maszyny Wirtualnej lub nie można uzyskać dostępu do maszyny Wirtualnej.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Jak zainstalować agenta maszyny Wirtualnej w trybie offline

Aby zainstalować agenta maszyny Wirtualnej w trybie offline, należy wykonać następujące czynności.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Dołączanie dysku systemu operacyjnego maszyny wirtualnej do innej maszyny wirtualnej jako dysku danych

1. Zrób migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, utwórz dysk z migawki, a następnie dołącz dysk do maszyny wirtualnej w celu rozwiązania problemu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu portalu Azure](troubleshoot-recovery-disks-portal-windows.md). W przypadku klasycznej maszyny Wirtualnej usuń maszynę wirtualną i zachowaj dysk systemu operacyjnego, a następnie dołącz dysk systemu operacyjnego do maszyny wirtualnej w rozwiązywaniu problemów.

2.  Połącz się z maszyną wirtualną narzędzia do rozwiązywania problemów. Otwórz **zarządzanie komputerem** > **Zarządzanie dyskami**. Upewnij się, że dysk systemu operacyjnego jest w trybie online i że litery dysków są przypisane do partycji dysku.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Modyfikowanie dysku systemu operacyjnego w celu zainstalowania agenta maszyny wirtualnej platformy Azure

1.  Nawiązuj połączenie pulpitu zdalnego z maszyną wirtualną narzędzia do rozwiązywania problemów.

2.  Na maszynie wirtualnej narzędzia do rozwiązywania problemów przejdź do podłączonego dysku systemu operacyjnego, otwórz folder \windows\system32\config. Skopiuj wszystkie pliki w tym folderze jako kopię zapasową, w przypadku gdy wymagane jest wycofanie.

3.  Uruchom **Edytor rejestru** (regedit.exe).

4.  Wybierz **HKEY_LOCAL_MACHINE** klucz. W menu wybierz polecenie**Gałąź ładowania** **plików** > :

    ![Załaduj ul](./media/install-vm-agent-offline/load-hive.png)

5.  Przejdź do folderu \windows\system32\config\SYSTEM na dołączonym dysku systemu operacyjnego. Aby uzyskać nazwę gałęzi, wprowadź **BROKENSYSTEM**. Nowy gałąź rejestru jest wyświetlany pod kluczem **HKEY_LOCAL_MACHINE.**

6.  Przejdź do folderu \windows\system32\config\SOFTWARE na dołączonym dysku systemu operacyjnego. Aby uzyskać nazwę oprogramowania hive, wprowadź **BROKENSOFTWARE**.

7. Jeśli na dysku dołączonego systemu operacyjnego jest zainstalowany agent maszyny Wirtualnej, wykonaj kopię zapasową bieżącej konfiguracji. Jeśli nie ma zainstalowanego agenta maszyny Wirtualnej, przejdź do następnego kroku.
      
    1. Zmień nazwę folderu \windowsazure na \windowsazure.old.

    2. Wyeksportuj następujące rejestry:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Usługi\RdAgent

8.  Użyj istniejących plików na maszynie wirtualnej narzędzia do rozwiązywania problemów jako repozytorium instalacji agenta maszyny Wirtualnej. Wykonaj następujące czynności:

    1. Z maszyny wirtualnej do rozwiązywania problemów wyeksportuj następujące podkluczy w formacie rejestru (reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Eksportowanie podkluczy rejestru](./media/install-vm-agent-offline/backup-reg.png)

    2. Edytuj pliki rejestru. W każdym pliku zmień wartość wejścia **SYSTEM** na **BROKENSYSTEM** (jak pokazano na poniższych obrazach) i zapisz plik. Zapamiętaj **ImagePath** bieżącego agenta maszyny Wirtualnej. Będziemy musieli skopiować odpowiedni folder na dołączony dysk systemu operacyjnego. 

        ![Zmienianie wartości podkluczy rejestru](./media/install-vm-agent-offline/change-reg.png)

    3. Zaimportuj pliki rejestru do repozytorium, klikając dwukrotnie każdy plik rejestru.

    4. Upewnij się, że następujące trzy podklu skróty zostały pomyślnie zaimportowane do gałęzi **BROKENSYSTEM:**
        - System WindowsAzureGuestagent
        - Usługa WindowsAzureTelemetryService
        - RdAgent (RdAgent)

    5. Skopiuj folder instalacyjny bieżącego agenta maszyny Wirtualnej na podłączony dysk systemu operacyjnego: 

        1.  Na dołączonym dysku systemu operacyjnego utwórz folder o nazwie WindowsAzure w ścieżce głównej.

        2.  Przejdź do C:\WindowsAzure na maszynie wirtualnej do rozwiązywania problemów, poszukaj dowolnego folderu o nazwie C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Skopiuj folder GuestAgent, który ma najnowszy numer wersji z języka C:\WindowsAzure do folderu WindowsAzure na podłączonym dysku systemu operacyjnego. Jeśli nie masz pewności, który folder ma zostać skopiowany, skopiuj wszystkie foldery GuestAgent. Na poniższej ilustracji przedstawiono przykład folderu GuestAgent, który jest kopiowany na dołączony dysk systemu operacyjnego.

             ![Kopiowanie folderu GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Wybierz **BROKENSYSTEM**. Z menu wybierz polecenie**Rozładuj** **plik** > gałęzi .

10.  Wybierz **BROKENSOFTWARE**. Z menu wybierz polecenie**Rozładuj** **plik** > gałęzi .

11.  Odłącz dysk systemu operacyjnego, a następnie [zmień dysk systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Dla klasycznej maszyny Wirtualnej utwórz nową maszynę wirtualną przy użyciu naprawionego dysku systemu operacyjnego.

12.  Dostęp do maszyny Wirtualnej. Należy zauważyć, że RdAgent jest uruchomiony i dzienniki są generowane.

Jeśli maszyna wirtualna została utworzona przy użyciu modelu wdrażania Menedżera zasobów, gotowe.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Użyj właściwości ProvisionGuestAgent dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Jeśli utworzono maszynę wirtualną przy użyciu modelu klasycznego, użyj modułu Programu Azure PowerShell, aby zaktualizować **właściwość ProvisionGuestAgent.** Właściwość informuje platformę Azure, że maszyna wirtualna ma zainstalowanego agenta maszyny Wirtualnej.

Aby ustawić właściwość **ProvisionGuestAgent,** uruchom następujące polecenia w programie Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Następnie uruchom `Get-AzureVM` polecenie. Należy zauważyć, że **GuestAgentStatus** właściwość jest teraz wypełniona danymi:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Następne kroki

- [Usługa Azure Virtual Machine Agent — omówienie](../extensions/agent-windows.md)
- [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](../extensions/features-windows.md)
