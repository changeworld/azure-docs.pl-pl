---
title: Szybki start — parcie maszyny wirtualnej za pomocą programu PowerShell
description: W tym przewodniku Szybki start dowiedz się, jak zrobić na komputerze wirtualnym platformy Azure zapas maszyn wirtualnych za pomocą modułu programu Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74171957"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell

Moduł [AZ programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub skryptów.

[Usługa Azure Backup](backup-overview.md) tworzą kopię zapasową maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule pokazano, jak zrobić z nim utworzenie kopii zapasowej maszyny Wirtualnej platformy Azure za pomocą modułu AZ. Alternatywnie można wyw. [Azure CLI](quick-backup-vm-cli.md) [Azure portal](quick-backup-vm-portal.md)

W tym przewodniku Szybki start opisano wykonywanie kopii zapasowej istniejącej maszyny wirtualnej platformy Azure. Jeśli musisz utworzyć maszynę wirtualną, możesz [utworzyć maszynę wirtualną za pomocą programu Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ten przewodnik Szybki start wymaga modułu AZ usługi Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Zaloguj się i zarejestruj

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```

2. Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji w [usłudze Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)w następujący sposób:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[Magazyn usług odzyskiwania](backup-azure-recovery-services-vault-overview.md) to kontener logiczny, który przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania kopii zapasowej tworzy punkt odzyskiwania wewnątrz magazynu usług odzyskiwania. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Podczas tworzenia przechowalni:

- W przypadku grupy zasobów i lokalizacji określ grupę zasobów i lokalizację maszyny Wirtualnej, której chcesz poprzeć.
- Jeśli ten [przykładowy skrypt](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) został użyty do utworzenia maszyny Wirtualnej, grupą zasobów jest **myResourceGroup**, maszyna wirtualna to ***myVM**, a zasoby znajdują się w regionie **WestEurope.**
- Usługa Azure Backup automatycznie obsługuje magazyn dla kopii zapasowych danych. Domyślnie przechowalnia używa [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy-grs.md). Nadmiarowość geograficzna zapewnia, że kopie zapasowe danych są replikowane do pomocniczego regionu platformy Azure, setki mil od regionu podstawowego.

Teraz utwórz przechowalnię:

1. Użyj [New-AzRecoveryServicesVault,](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) aby utworzyć magazyn:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Ustaw kontekst przechowalni za pomocą [funkcji Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)w następujący sposób:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Zmień konfigurację nadmiarowości magazynu (LRS/GRS) magazynu za pomocą [właściwości Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)w następujący sposób:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Nadmiarowość magazynu można modyfikować tylko wtedy, gdy nie ma żadnych elementów kopii zapasowej chronionych do magazynu.

## <a name="enable-backup-for-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

Włącz tworzenie kopii zapasowych dla maszyny Wirtualnej platformy Azure i określić zasady tworzenia kopii zapasowych.

- Zasady określają, kiedy kopie zapasowe są uruchamiane i jak długo punkty odzyskiwania utworzone przez kopie zapasowe powinny być zachowywane.
- Domyślna zasada ochrony uruchamia kopię zapasową raz dziennie dla maszyny Wirtualnej i zachowuje utworzone punkty odzyskiwania przez 30 dni. Tej domyślnej zasady można użyć, aby szybko chronić maszynę wirtualną.

Włącz tworzenie kopii zapasowych w następujący sposób:

1. Najpierw ustaw domyślną zasadę za pomocą [Get-AzRecoveryServiceSBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Włącz tworzenie kopii zapasowych maszyn wirtualnych za pomocą [enable-AzRecoveryServiceBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Określ zasady, grupę zasobów i nazwę maszyny Wirtualnej.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Kopie zapasowe są uruchamiane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych. Można również uruchomić kopię zapasową na żądanie:

- Pierwsze początkowe zadanie tworzenia kopii zapasowej tworzy pełny punkt odzyskiwania.
- Po początkowej kopii zapasowej każde zadanie kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
- Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Aby uruchomić kopię zapasową na żądanie, należy użyć [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Należy określić kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Każda maszyna wirtualna, której kopia zapasowa ma być tworzona, jest traktowana jako element. Aby rozpocząć zadanie tworzenia kopii zapasowej, można uzyskać informacje o maszynie Wirtualnej z [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Uruchom zadanie kopii zapasowej na żądanie w następujący sposób:

1. Określ kontener, uzyskaj informacje o maszynie Wirtualnej i uruchom kopię zapasową.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Może być konieczne odczekanie do 20 minut, ponieważ pierwsze zadanie tworzenia kopii zapasowej tworzy pełny punkt odzyskiwania. Monitoruj zadanie zgodnie z opisem w następnej procedurze.

## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

1. Uruchom [Get-AzRecoveryservicesBackupJob,](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) aby monitorować stan zadania.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Dane wyjściowe są podobne do następującego przykładu, który pokazuje zadanie jako **InProgress:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Po **zakończeniu**stanu zadania maszyna wirtualna jest chroniona i ma pełny punkt odzyskiwania.

## <a name="clean-up-the-deployment"></a>Oczyszczanie wdrożenia

Jeśli nie trzeba już wykonać kopii zapasowej maszyny Wirtualnej, można ją wyczyścić.

- Jeśli chcesz wypróbować przywracanie maszyny Wirtualnej, pomiń oczyszczanie.
- Jeśli użyto istniejącej maszyny Wirtualnej, można pominąć końcowe polecenie cmdlet [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby pozostawić grupę zasobów i maszynę wirtualną na miejscu.

Wyłącz ochronę, usuń punkty przywracania i przechowalnię. Then delete the resource group and associated VM resources, as follows:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania.

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) zrobić kopii zapasowej maszyn wirtualnych w witrynie Azure portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) szybko przywrócić maszynę wirtualną
