---
title: Zainstaluj agenta maszyny Wirtualnej platformy Azure w trybie offline | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować agenta maszyny Wirtualnej platformy Azure w trybie offline.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e9fc8351b5e9a4f2274f0906d4071f86dcbcff26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640670"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Zainstaluj agenta maszyny wirtualnej platformy Azure w trybie offline 

Agent maszyny wirtualnej platformy Azure (Agent maszyny Wirtualnej) udostępnia przydatne funkcje, takie jak Resetowanie hasła administratora lokalnego i wypychanie skryptu. W tym artykule przedstawiono sposób instalowania agenta maszyny Wirtualnej w trybie offline maszyny wirtualnej Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kiedy należy używać agenta maszyny Wirtualnej w trybie offline

Zainstaluj agenta maszyny Wirtualnej w trybie offline w następujących scenariuszach:

- Wdrożonej maszyny Wirtualnej platformy Azure nie ma zainstalowanego agenta maszyny Wirtualnej lub agent nie działa.
- Nie pamiętasz hasła administratora dla maszyny Wirtualnej lub nie masz dostępu do maszyny Wirtualnej.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Sposób instalowania agenta maszyny Wirtualnej w trybie offline

Wykonaj następujące kroki, aby zainstalować agenta maszyny Wirtualnej w trybie offline.

> [!NOTE]
> Można zautomatyzować proces instalowania agenta maszyny Wirtualnej w trybie offline.
> Aby to zrobić, należy użyć [skrypty odzyskiwania maszyny Wirtualnej platformy Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Jeśli zdecydujesz się używać skryptów odzyskiwania maszyny Wirtualnej platformy Azure, można użyć następującego procesu:
> 1. Za pomocą skryptów można dołączyć dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do maszyny Wirtualnej odzyskiwania, należy pominąć krok 1.
> 2. Wykonaj kroki 2 – 10, aby zastosować środki zaradcze.
> 3. Za pomocą skryptów, aby ponownie utworzyć maszynę Wirtualną, należy pominąć krok 11.
> 4. Wykonaj krok 12.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Dołącz dysk systemu operacyjnego maszyny wirtualnej do innej maszyny Wirtualnej jako dysk z danymi

1.  Usuń maszynę Wirtualną. Pamiętaj o wybraniu **zachować dyski** opcji po usunięciu maszyny Wirtualnej.

2.  Dołącz dysk systemu operacyjnego jako dysku danych do innej maszyny Wirtualnej (nazywane _narzędzia do rozwiązywania problemów_ maszyny Wirtualnej). Aby uzyskać więcej informacji, zobacz [dołączanie dysku danych do maszyny Wirtualnej z systemem Windows w witrynie Azure portal](../windows/attach-managed-disk-portal.md).

3.  Połączyć się do rozwiązywania problemów z maszyny Wirtualnej. Otwórz **Zarządzanie komputerem** > **Zarządzanie dyskami**. Upewnij się, że dysk systemu operacyjnego jest w trybie online i przypisania litery dysku do partycji dysku.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Modyfikowanie dysku systemu operacyjnego do zainstalowania agenta maszyny Wirtualnej platformy Azure

1.  Podłączanie pulpitu zdalnego należy dokonać do rozwiązywania problemów z maszyny Wirtualnej.

2.  Na dołączonym dysku systemu operacyjnego przejdź do folderu \windows\system32\config. Skopiuj wszystkie pliki z tego folderu do przechowywania kopii zapasowych, w przypadku, gdy wymagane będzie wycofanie.

3.  Rozpocznij **Edytora rejestru** (regedit.exe).

4.  Wybierz **HKEY_LOCAL_MACHINE** klucza. W menu, wybierz **pliku** > **Załaduj gałąź rejestru**:

    ![Załaduj gałąź](./media/install-vm-agent-offline/load-hive.png)

5.  Przejdź do folderu \windows\system32\config\SYSTEM na dołączonym dysku systemu operacyjnego. Nazwa gałęzi, wprowadź **BROKENSYSTEM**. Nowej gałęzi rejestru jest wyświetlana w obszarze **HKEY_LOCAL_MACHINE** klucza.

6.  Przejdź do folderu \windows\system32\config\SOFTWARE na dołączonym dysku systemu operacyjnego. Nazwa oprogramowania hive, można wprowadzić **BROKENSOFTWARE**.

7. Jeśli dołączony dysk systemu operacyjnego jest zainstalowany agent maszyny Wirtualnej, należy wykonać kopię zapasową bieżącej konfiguracji. Jeśli nie ma zainstalowanego agenta maszyny Wirtualnej, przejdź do następnego kroku.
      
    1. Zmień nazwę folderu \windowsazure \windowsazure.old.

    2. Eksportuj następujących rejestrów:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Użyj istniejących plików na maszynę Wirtualną do rozwiązywania problemów z jako repozytorium do instalacji agenta maszyny Wirtualnej. Wykonaj następujące czynności:

    1. Z narzędzia do rozwiązywania problemów maszyny Wirtualnej należy wyeksportować następujących podkluczy w rejestrze w formacie (reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Eksportowanie podkluczy rejestru](./media/install-vm-agent-offline/backup-reg.png)

    2. Edytowanie plików rejestru. W każdym pliku, należy zmienić wartość wpisu **systemu** do **BROKENSYSTEM** (jak pokazano na poniższych ilustracjach) i Zapisz plik. Należy pamiętać, **ImagePath** bieżącego agenta maszyny Wirtualnej. Konieczne będzie skopiuj odpowiedni folder na dołączonym dysku systemu operacyjnego. 

        ![Zmień wartość podklucza rejestru](./media/install-vm-agent-offline/change-reg.png)

    3. Importowanie plików rejestru do repozytorium przez dwukrotne kliknięcie każdego pliku rejestru.

    4. Upewnij się, że następujące trzy klucze podrzędne są pomyślnie zaimportowane do **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Skopiuj folder instalacyjny bieżącego agenta maszyny Wirtualnej na dołączonym dysku systemu operacyjnego: 

        1.  Na dołączonym dysku systemu operacyjnego Utwórz folder o nazwie WindowsAzure w ścieżce katalogu głównego.

        2.  Przejdź do C:\WindowsAzure na rozwiązywanie problemów z maszyny Wirtualnej, sprawdź dla każdego folderu o nazwie C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Skopiuj folder agentów gości, który ma numer najnowszej wersji od C:\WindowsAzure do folderu WindowsAzure w dołączonym dysku systemu operacyjnego. Jeśli nie masz pewności, powinien zostać skopiowany folder, skopiować wszystkie foldery agentów gości. Na poniższej ilustracji przedstawiono przykład folderu agentów gości, który jest kopiowany na dołączonym dysku systemu operacyjnego.

             ![Skopiuj folder agentów gości](./media/install-vm-agent-offline/copy-files.png)

9.  Wybierz **BROKENSYSTEM**. W menu, wybierz opcję **pliku** > **Zwolnij gałąź rejestru**.

10.  Wybierz **BROKENSOFTWARE**. W menu, wybierz opcję **pliku** > **Zwolnij gałąź rejestru**.

11.  Odłącz dysk systemu operacyjnego, a następnie ponownie utwórz maszynę Wirtualną przy użyciu dysku systemu operacyjnego.

12.  Dostęp do maszyny Wirtualnej. Należy zauważyć, że działa RdAgent, i są generowane dzienniki.

Jeśli maszyna wirtualna została utworzona przy użyciu modelu wdrażania usługi Resource Manager, wszystko będzie gotowe.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Użyj właściwości ProvisionGuestAgent dla klasycznych maszyn wirtualnych

Jeśli maszyna wirtualna została utworzona przy użyciu modelu klasycznego, należy użyć modułu Azure PowerShell, aby zaktualizować **ProvisionGuestAgent** właściwości. Właściwość informuje platformy Azure, że maszyna wirtualna ma zainstalowanego agenta maszyny Wirtualnej.

Aby ustawić **ProvisionGuestAgent** właściwość, uruchom następujące polecenia w programie Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Następnie uruchom `Get-AzureVM` polecenia. Należy zauważyć, że **GuestAgentStatus** właściwość jest teraz wypełniana danymi:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie agenta maszyny wirtualnej na platformie Azure](../extensions/agent-windows.md)
- [Rozszerzenia maszyn wirtualnych i funkcji dla Windows](../extensions/features-windows.md)
