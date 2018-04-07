---
title: Zainstaluj agenta maszyny Wirtualnej platformy Azure w trybie offline | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania agenta maszyny Wirtualnej platformy Azure w trybie offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 8b9d1ea3e4b5bf959484a737453d7f6f1f63d1e6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Zainstaluj agenta maszyny wirtualnej platformy Azure w trybie offline 

Agent maszyny wirtualnej Azure (Agent maszyny Wirtualnej) zawiera przydatne funkcje, takie jak Resetowanie hasła administratora lokalnego i wypychanie skryptu. W tym artykule przedstawiono sposób instalowania agenta maszyny Wirtualnej w trybie offline maszyny wirtualnej systemu Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kiedy należy używać agenta maszyny Wirtualnej w trybie offline

Zainstaluj agenta maszyny Wirtualnej w tryb offline w następujących scenariuszach:

- Wdrożonej maszyny Wirtualnej platformy Azure nie ma zainstalowanego agenta maszyny Wirtualnej lub agent nie działa.
- Nie pamiętasz hasła administratora dla maszyny Wirtualnej lub nie masz dostępu do maszyny Wirtualnej.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Jak zainstalować agenta maszyny Wirtualnej w trybie offline

Poniższe kroki należy zainstalować agenta maszyny Wirtualnej w trybie offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Dołączyć dysk systemu operacyjnego maszyny wirtualnej do innej maszyny Wirtualnej jako dysk z danymi

1.  Usuń maszynę Wirtualną. Należy wybrać **zachować dyski** opcję po usunięciu maszyny Wirtualnej.

2.  Dołączanie dysku systemu operacyjnego jako dysku danych do innej maszyny Wirtualnej (nazywane _Rozwiązywanie problemów z_ maszyny Wirtualnej). Aby uzyskać więcej informacji, zobacz [dołączenie dysku danych do maszyny Wirtualnej systemu Windows w portalu Azure](attach-managed-disk-portal.md).

3.  Podłącz do rozwiązywania problemów z maszyny Wirtualnej. Otwórz **Zarządzanie komputerem** > **dysku zarządzania**. Upewnij się, dysk systemu operacyjnego jest w trybie online i że litery dysków są przypisane do partycji dysku.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Modyfikowanie dysku systemu operacyjnego do zainstalowania agenta maszyny Wirtualnej Azure

1.  Wprowadzić połączeń usług pulpitu zdalnego do rozwiązywania problemów z maszyny Wirtualnej.

2.  Na dysku systemu operacyjnego, który można dołączyć przejdź do folderu \windows\system32\config. Skopiuj wszystkie pliki w tym folderze jako zapasowy, w przypadku wycofywania jest wymagana.

3.  Uruchom **Edytora rejestru** (regedit.exe).

4.  Wybierz **HKEY_LOCAL_MACHINE** klucza. W menu, wybierz **pliku** > **Hive obciążenia**:

    ![Załaduj gałąź](./media/install-vm-agent-offline/load-hive.png)

5.  Przejdź do folderu \windows\system32\config\SYSTEM na dysku systemu operacyjnego, który można dołączyć. Wprowadź nazwę gałęzi, **BROKENSYSTEM**. Nowe gałęzi rejestru jest wyświetlana w obszarze **HKEY_LOCAL_MACHINE** klucza.

6.  Przejdź do folderu \windows\system32\config\SOFTWARE na dysku systemu operacyjnego, który można dołączyć. Wprowadź nazwę oprogramowanie hive, **BROKENSOFTWARE**.

7.  Jeśli nie działa, czy Agent maszyny Wirtualnej, Utwórz kopię zapasową bieżącej konfiguracji.

    >[!NOTE]
    >Jeśli maszyna wirtualna nie jest zainstalowany agent, przejdź do kroku 8. 
      
    1. Zmień nazwę folderu \windowsazure na \windowsazure.old.

    2. Wyeksportuj następujące rejestry organizacji:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Użyj istniejących plików na rozwiązywanie problemów z maszyny Wirtualnej jako repozytorium do instalacji agenta maszyny Wirtualnej. Wykonaj następujące czynności:

    1. Z rozwiązywania maszyny Wirtualnej należy wyeksportować poniższe podklucze w formacie rejestru (reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Eksportowanie podkluczy rejestru](./media/install-vm-agent-offline/backup-reg.png)

    2. Edytowanie plików rejestru. W każdym pliku, zmień wartość wpisu **systemu** do **BROKENSYSTEM** (jak pokazano na poniższych ilustracjach) i Zapisz plik.

        ![Zmień wartość podklucza rejestru](./media/install-vm-agent-offline/change-reg.png)

    3. Importowanie plików rejestru do repozytorium, klikając dwukrotnie każdego pliku rejestru.

    4. Potwierdź, że następujące trzy podklucze są pomyślnie zaimportowane do **BROKENSYSTEM** gałęzi:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Skopiuj folder agenta maszyny Wirtualnej z C:\windowsazure\packages do &lt;dysk systemu operacyjnego, który można dołączyć&gt;: \windowsazure\packages.

    ![Skopiuj pliki agenta maszyny Wirtualnej do dysku systemu operacyjnego](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Nie należy kopiować **dzienniki** folderu. Nowe dzienniki są generowane, po uruchomieniu usługi.

10.  Wybierz **BROKENSYSTEM**. Wybierz z menu **pliku** > **Zwolnij gałąź rejestru**.

11.  Wybierz **BROKENSOFTWARE**. Wybierz z menu **pliku** > **Zwolnij gałąź rejestru**.

12.  Odłącz dysk systemu operacyjnego, a następnie utwórz ponownie maszynę Wirtualną za pomocą dysku systemu operacyjnego.

13.  Dostęp do maszyny Wirtualnej. Należy zauważyć, że działa RdAgent i dzienniki są generowane.

Jeśli maszyna wirtualna została utworzona przy użyciu klasycznego modelu wdrażania, wszystko będzie gotowe.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Użyj właściwości ProvisionGuestAgent dla maszyny wirtualne utworzone za pomocą Menedżera zasobów Azure

Jeśli maszyna wirtualna została utworzona przy użyciu modelu wdrażania usługi Resource Manager, należy użyć modułu Azure PowerShell, aby zaktualizować **ProvisionGuestAgent** właściwości. Właściwość informuje Azure, że maszyna wirtualna jest zainstalowany Agent maszyny Wirtualnej.

Aby ustawić **ProvisionGuestAgent** właściwości, uruchom następujące polecenia w programie Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Następnie uruchom `Get-AzureVM` polecenia. Zwróć uwagę, że **GuestAgentStatus** właściwości znajduje się teraz z danymi:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi Azure agenta maszyny wirtualnej](agent-user-guide.md)
- [Rozszerzenia maszyn wirtualnych i funkcje systemu Windows](extensions-features.md)
