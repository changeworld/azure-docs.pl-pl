---
title: 'Azure: Szybki start — tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell'
description: Informacje na temat tworzenia kopii zapasowych maszyn wirtualnych przy użyciu programu Azure PowerShell
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c3086f0b68f8e2bf8bdd115e483d53ea94384f1a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876313"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Programu Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) moduł jest używany do tworzenia i zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów. 

[Usługa Azure Backup](backup-overview.md) tworzy kopie zapasowe maszyn lokalnych i aplikacji i maszyn wirtualnych platformy Azure. W tym artykule przedstawiono sposób tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu modułu AZ. Alternatywnie można utworzyć kopię zapasową maszyny Wirtualnej przy użyciu [wiersza polecenia platformy Azure](quick-backup-vm-cli.md), lub [witryny Azure portal](quick-backup-vm-portal.md).

W tym przewodniku Szybki start opisano wykonywanie kopii zapasowej istniejącej maszyny wirtualnej platformy Azure. Jeśli musisz utworzyć maszynę wirtualną, możesz [utworzyć maszynę wirtualną za pomocą programu Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ten przewodnik Szybki Start wymaga programu Azure PowerShell AZ modułu Wersja 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="log-in-and-register"></a>Zaloguj się i rejestrowanie

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```
2. Przy pierwszym użyciu usługi Azure Backup, należy zarejestrować dostawcę usługi Azure Recovery Service w subskrypcji przy użyciu [AzResourceProvider rejestru](/powershell/module/az.Resources/Register-azResourceProvider), wykonując następujące czynności:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

A [magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, która przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Po utworzeniu magazynu:

- Dla grupy zasobów i lokalizacji należy określić grupę zasobów i lokalizacji maszyny Wirtualnej, aby utworzyć kopię zapasową.
- Jeśli używasz tego [przykładowy skrypt](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) do utworzenia maszyny Wirtualnej, grupa zasobów jest **myResourceGroup**, maszyna wirtualna jest ***myVM**, a zasoby znajdują się w **WestEurope**  regionu.
- Usługa Azure Backup automatycznie obsługuje magazyn kopii zapasowych danych. Domyślnie używa magazynu [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Nadmiarowość geograficzna zapewnia, że kopie zapasowe danych są replikowane do regionu pomocniczego platformy Azure, setek odległości od regionu podstawowego.

Teraz Utwórz magazyn.


1. Użyj [New AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)do utworzenia magazynu:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Ustaw kontekst magazynu za pomocą [AzRecoveryServicesVaultContext zestaw](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), wykonując następujące czynności:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Zmień konfigurację nadmiarowości magazynu (LRS/GRS) magazynu przy użyciu [AzRecoveryServicesBackupProperties zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperties?view=azps-1.6.0), wykonując następujące czynności:
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > Nadmiarowość magazynu mogą być modyfikowane tylko wtedy, gdy istnieją żadnych elementów kopii zapasowych chronionych w magazynie.

## <a name="enable-backup-for-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

Włącz wykonywanie kopii zapasowej maszyny wirtualnej platformy Azure, a następnie określ zasady kopii zapasowych.

- Zasady określają, kiedy wykonywane kopie zapasowe i jak długo ma być przechowywana punktów odzyskiwania utworzonych przez tworzenie kopii zapasowych.
- Domyślne zasady ochrony uruchamia kopii zapasowej raz dziennie dla maszyny Wirtualnej i przechowują punkty odzyskiwania utworzone przez 30 dni. Zasady domyślne można użyć, aby zapewnić szybką ochronę maszyny Wirtualnej. 

Włącz kopię zapasową w następujący sposób:

1. Najpierw Ustaw domyślne zasady za pomocą [Get AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Włącz wykonywanie kopii zapasowej maszyny Wirtualnej, z [AzRecoveryServicesBackupProtection Włącz](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Określ zasady, grupy zasobów i nazwę maszyny Wirtualnej.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Wykonywane kopie zapasowe zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowej. Można również uruchomić kopii zapasowej usługi ad hoc:

- Pierwszy początkowe zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania.
- Po początkowej kopii zapasowej każde zadanie tworzenia kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
- Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Aby uruchomić kopii zapasowej usługi ad-hoc, należy użyć[AzRecoveryServicesBackupItem kopii zapasowej](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Określasz kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Każda maszyna wirtualna, której kopia zapasowa ma być tworzona, jest traktowana jako element. Aby uruchomić zadanie tworzenia kopii zapasowej, można uzyskać informacji na temat maszyny Wirtualnej przy użyciu [Get AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Uruchom zadanie tworzenia kopii zapasowej ad-hoc w następujący sposób:

1. Określany jest kontener, uzyskać informacji o maszynie Wirtualnej i uruchomić tworzenie kopii zapasowej.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Może być konieczne czekać nawet do 20 minut, ponieważ pierwsze zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania. Monitorowanie zadania, zgodnie z opisem w następnej procedurze.


## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

1. Uruchom [Get AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) do monitorowania stanu zadania.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    Dane wyjściowe będą podobne do poniższego przykładu, który pokazuje zadania jako **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Jeśli stan zadania to **Ukończono**, maszyna wirtualna jest chroniona i ma być przechowywany punkt pełnego odzyskiwania.


## <a name="clean-up-the-deployment"></a>Czyszczenie wdrożenia

Jeśli nie są już potrzebne do tworzenia kopii zapasowej maszyny Wirtualnej, możesz je wyczyścić.
- Jeśli chcesz wypróbować funkcję przywracania maszyny Wirtualnej, Pomiń oczyszczanie się.
- Jeśli użyto istniejącej maszyny Wirtualnej, możesz pominąć końcowe [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) polecenia cmdlet, aby pozostawić grupę zasobów i maszynę Wirtualną na miejscu.

Wyłącz ochronę, usunąć punkty przywracania oraz Magazyn. Następnie możesz usunąć grupę zasobów i skojarzone zasoby maszyny Wirtualnej w następujący sposób:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania. 

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) do tworzenia kopii zapasowych maszyn wirtualnych w witrynie Azure portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) można szybko przywrócić Maszynę wirtualną
