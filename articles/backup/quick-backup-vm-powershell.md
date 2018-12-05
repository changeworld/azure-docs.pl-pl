---
title: 'Azure: Szybki start — tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell'
description: Informacje na temat tworzenia kopii zapasowych maszyn wirtualnych przy użyciu programu Azure PowerShell
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 2/14/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7971c1399cc905353e271e8774c0e7ad36ca79a9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869269"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell
Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia lub skryptów. Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania, które można przechowywać w geograficznie nadmiarowych magazynach odzyskiwania. Ten artykuł szczegółowo opisuje sposób tworzenia kopii zapasowej maszyny wirtualnej za pomocą modułu Azure PowerShell. Te czynności można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md) lub [witryny Azure Portal](quick-backup-vm-portal.md).

W tym przewodniku Szybki start opisano wykonywanie kopii zapasowej istniejącej maszyny wirtualnej platformy Azure. Jeśli musisz utworzyć maszynę wirtualną, możesz [utworzyć maszynę wirtualną za pomocą programu Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 4.4 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji za pomocą polecenia [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Tworzenie magazynów usługi Recovery Services
Magazyn usługi Recovery Services jest kontenerem logicznym, który przechowuje dane kopii zapasowej dla każdego chronionego zasobu, takiego jak maszyny wirtualne platformy Azure. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Utwórz magazyn usługi Recovery Services za pomocą polecenia [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Określ taką samą grupę zasobów i lokalizację, jak w przypadku maszyny wirtualnej, która ma być chroniona. Jeśli do utworzenia maszyny wirtualnej został użyty [skrypt przykładowy](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json), grupa zasobów nosi nazwę *myResourceGroup*, maszyna wirtualna nazywa się *myVM*, a zasoby znajdują się w lokalizacji *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Magazyn jest domyślnie ustawiony na magazyn geograficznie nadmiarowy. Aby jeszcze lepiej chronić dane, ten poziom nadmiarowości magazynu gwarantuje, że dane kopii zapasowej są replikowane do dodatkowego regionu świadczenia usługi Azure, który znajduje się setki kilometrów od regionu podstawowego.

Aby używać tego magazynu w pozostałych czynnościach, ustaw kontekst magazynu za pomocą polecenia [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure
Aby określić, kiedy zadanie tworzenia kopii zapasowej ma się uruchomić oraz jak długo mają być przechowywane punkty odzyskiwania, należy utworzyć zasady i korzystać z nich. Domyślne zasady ochrony uruchamiają zadanie tworzenia kopii zapasowej codziennie i przechowują punkty odzyskiwania przez 30 dni. Możesz użyć domyślnych wartości zasad, aby zapewnić szybką ochronę maszyny wirtualnej. Najpierw ustaw domyślne zasady za pomocą polecenia [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Aby włączyć ochronę maszyny wirtualnej przy użyciu kopii zapasowej, użyj polecenia [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Określ zasady, które mają zostać użyte, a następnie grupę zasobów i maszynę wirtualną, które chcesz chronić:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej
Aby uruchomić tworzenie kopii zapasowej od razu, zamiast czekać, aż zasady domyślne uruchomią zadanie w zaplanowanym czasie, użyj polecenia [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). Pierwsze zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania. Każde zadanie tworzenia kopii zapasowej uruchomione po początkowej kopii zapasowej tworzy przyrostowe punkty odzyskiwania. Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

W następującym zestawie poleceń określasz kontener w magazynie usługi Recovery Services, który przechowuje dane kopii zapasowej, za pomocą polecenia [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Każda maszyna wirtualna, której kopia zapasowa ma być tworzona, jest traktowana jako element. Aby uruchomić zadanie tworzenia kopii zapasowej, uzyskaj informacje o elemencie maszyny wirtualnej za pomocą polecenia [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Ponieważ pierwsze zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania, proces może zająć do 20 minut.


## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej
Aby monitorować stan zadań tworzenia kopii zapasowej, użyj polecenia [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

Dane wyjściowe są podobne do następującego przykładu informującego, że zadanie tworzenia kopii zapasowej jest w toku (**InProgress**):

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Gdy *stan* wskaże, że zadanie tworzenia kopii zapasowej jest ukończone (*Completed*), maszyna wirtualna jest chroniona za pomocą usługi Recovery Services i przechowywany jest punkt pełnego odzyskiwania.


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Gdy ochrona maszyny wirtualnej nie jest już potrzebna, można ją wyłączyć i usunąć punkty przywracania oraz magazyn usługi Recovery Services, a następnie usunąć grupę zasobów i powiązane zasoby maszyny wirtualnej. Jeśli użyto istniejącej maszyny wirtualnej, możesz pominąć końcowe polecenie cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), aby pozostawić grupę zasobów i maszynę wirtualną.

Jeśli zamierzasz kontynuować pracę z samouczkiem dotyczącym tworzenia kopii zapasowych, w którym objaśniono, jak przywracać dane dla maszyny wirtualnej, pomiń kroki opisane w tej sekcji i przejdź do sekcji [Następne kroki](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania. Aby dowiedzieć się więcej na temat usług Azure Backup i Recovery Services, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Tworzenie kopii zapasowych wielu maszyn wirtualnych platformy Azure](./tutorial-backup-vm-at-scale.md)
