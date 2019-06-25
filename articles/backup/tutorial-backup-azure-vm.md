---
title: Tworzenie kopii zapasowych wielu maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowych wielu maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services, za pomocą programu Azure PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7cbe2cca37ce237409042e40b4a60311aed2446c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273994"
---
# <a name="back-up-azure-vms-with-powershell"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym samouczku opisano sposób wdrażania [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services, aby utworzyć kopię zapasową wielu maszyn wirtualnych platformy Azure przy użyciu programu PowerShell.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz magazyn usługi Recovery Services i Ustaw kontekst magazynu.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalacz zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych przed można utworzyć kopię zapasową (lub ochronę) maszyny wirtualnej, należy wykonać [wymagania wstępne](backup-azure-arm-vms-prepare.md) do przygotowania środowiska na potrzeby ochrony maszyn wirtualnych. 

> [!IMPORTANT]
> W tym samouczku przyjęto założenie, że utworzono już grupę zasobów i maszynę wirtualną platformy Azure.


## <a name="log-in-and-register"></a>Zaloguj się i rejestrowanie


1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```
2. Przy pierwszym użyciu usługi Azure Backup, należy zarejestrować dostawcę usługi Azure Recovery Service w subskrypcji przy użyciu [AzResourceProvider rejestru](/powershell/module/az.Resources/Register-azResourceProvider). Jeśli użytkownik jest już zarejestrowany, Pomiń ten krok.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

A [magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, która przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.


- W tym samouczku utworzysz magazyn w tej samej grupie zasobów i lokalizacji co chcesz wykonać kopię maszyny Wirtualnej.
- Usługa Azure Backup automatycznie obsługuje magazyn kopii zapasowych danych. Domyślnie używa magazynu [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Nadmiarowość geograficzna zapewnia, że kopie zapasowe danych są replikowane do regionu pomocniczego platformy Azure, setek odległości od regionu podstawowego.

Utwórz magazyn w następujący sposób:

1. Użyj [New AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)do utworzenia magazynu. Określ nazwę grupy zasobów i lokalizacji maszyny Wirtualnej, aby utworzyć kopię zapasową.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Ustaw kontekst magazynu za pomocą [AzRecoveryServicesVaultContext zestaw](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - Kontekst magazynu to typ danych chronionych w magazynie.
   - Po ustawieniu kontekst ma zastosowanie do wszystkich kolejnych poleceń cmdlet

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Wykonywane kopie zapasowe zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowej. Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania.

- Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej raz dziennie o określonej godzinie.
- Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. 

Aby włączyć i utworzyć kopię zapasową maszynę wirtualną platformy Azure w ramach tego samouczka, firma Microsoft wykonaj następujące czynności:

1. Określ kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Każda maszyna wirtualna do utworzenia kopii zapasowej jest element. Aby uruchomić zadanie tworzenia kopii zapasowej, można uzyskać informacji na temat maszyny Wirtualnej przy użyciu [Get AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom tworzenie kopii zapasowej ad hoc z[AzRecoveryServicesBackupItem kopii zapasowej](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - Pierwszy początkowe zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania.
    - Po początkowej kopii zapasowej każde zadanie tworzenia kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
    - Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Włącz, a następnie uruchom kopię zapasową w następujący sposób:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

Jeśli napotkasz problemy podczas tworzenia kopii zapasowej maszyny wirtualnej, przejrzyj ten [artykule dotyczącym rozwiązywania problemów](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Usuwanie magazynu usług Recovery Services

Jeśli musisz usunąć magazyn, najpierw usuń punkty odzyskiwania w magazynie, a następnie wyrejestrować magazyn, w następujący sposób:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd](backup-azure-vms-automation.md) bardziej szczegółowy przewodnik dotyczący tworzenia kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell. 
- [Zarządzanie i monitorowanie maszyn wirtualnych platformy Azure](backup-azure-manage-vms.md)
- [Przywracanie maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md)
