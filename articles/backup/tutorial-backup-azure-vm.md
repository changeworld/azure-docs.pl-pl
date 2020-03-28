---
title: 'Samouczek: wiele kopii zapasowych maszyn wirtualnych platformy Azure za pomocą programu PowerShell'
description: W tym samouczku opisano szczegóły tworzenia kopii zapasowej wielu maszyn wirtualnych platformy Azure do magazynu usług odzyskiwania przy użyciu programu Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171779"
---
# <a name="back-up-azure-vms-with-powershell"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym samouczku opisano sposób wdrażania magazynu usług [Odzyskiwania kopii zapasowych platformy Azure](backup-overview.md) w celu utworzenia kopii zapasowej wielu maszyn wirtualnych platformy Azure przy użyciu programu PowerShell.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz magazyn usług odzyskiwania i ustaw kontekst przechowalni.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalanie zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych Przed utworzeniem kopii zapasowej (lub ochrony) maszyny wirtualnej należy wykonać [wymagania wstępne,](backup-azure-arm-vms-prepare.md) aby przygotować środowisko do ochrony maszyn wirtualnych.

> [!IMPORTANT]
> W tym samouczku przyjęto założenie, że utworzono już grupę zasobów i maszynę wirtualną platformy Azure.

## <a name="sign-in-and-register"></a>Zaloguj się i zarejestruj

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```

2. Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji w [usłudze Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Jeśli już się zarejestrowałeś, pomiń ten krok.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[Magazyn usług odzyskiwania](backup-azure-recovery-services-vault-overview.md) to kontener logiczny, który przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania kopii zapasowej tworzy punkt odzyskiwania wewnątrz magazynu usług odzyskiwania. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

* W tym samouczku utworzysz magazyn w tej samej grupie zasobów i lokalizacji co maszyna wirtualna, której chcesz utworzyć kopii zapasowej.
* Usługa Azure Backup automatycznie obsługuje magazyn dla kopii zapasowych danych. Domyślnie przechowalnia używa [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy-grs.md). Nadmiarowość geograficzna zapewnia, że kopie zapasowe danych są replikowane do pomocniczego regionu platformy Azure, setki mil od regionu podstawowego.

Utwórz przechowalnię w następujący sposób:

1. Użyj [New-AzRecoveryServicesVault,](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)aby utworzyć magazyn. Określ nazwę grupy zasobów i lokalizację maszyny Wirtualnej, której chcesz się sprzedać.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Ustaw kontekst przechowalni za pomocą [programu Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Kontekst magazynu to typ danych chronionych w magazynie.
   * Po ustawieniu kontekstu stosuje się do wszystkich kolejnych poleceń cmdlet

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Kopie zapasowe są uruchamiane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych. Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania.

* Domyślna zasada ochrony wyzwala zadanie tworzenia kopii zapasowej raz dziennie o określonej godzinie.
* Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni.

Aby włączyć i wykonać kopię zapasową maszyny Wirtualnej platformy Azure w tym samouczku, wykonujemy następujące czynności:

1. Określ kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Każda maszyna wirtualna do tworzenia kopii zapasowych jest elementem. Aby rozpocząć zadanie tworzenia kopii zapasowej, można uzyskać informacje o maszynie Wirtualnej z [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom kopię zapasową na żądanie za pomocą[kopii zapasowej AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Pierwsze początkowe zadanie tworzenia kopii zapasowej tworzy pełny punkt odzyskiwania.
    * Po początkowej kopii zapasowej każde zadanie kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
    * Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Włącz i uruchom kopię zapasową w następujący sposób:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz problemy podczas tworzenia kopii zapasowej maszyny wirtualnej, zapoznaj się z tym [artykułem dotyczącym rozwiązywania problemów](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Usuwanie magazynu usług Recovery Services

Jeśli chcesz usunąć przechowalnię, najpierw usuń punkty odzyskiwania w przechowalni, a następnie wyrejestruuj przechowalnię w następujący sposób:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Następne kroki

* [Przejrzyj](backup-azure-vms-automation.md) bardziej szczegółowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania maszyn wirtualnych platformy Azure za pomocą programu PowerShell.
* [Zarządzanie maszynami wirtualnymi platformy Azure i monitorowanie ich](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md)
