---
title: Szybki Start Azure — tworzenie kopii zapasowej maszyny Wirtualnej z szablonu usługi Resource Manager
description: Dowiedz się, jak utworzyć kopię zapasową maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481369"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure przy użyciu szablonu usługi Resource Manager

[Usługa Azure Backup](backup-overview.md) tworzy kopie zapasowe maszyn lokalnych i aplikacji i maszyn wirtualnych platformy Azure. W tym artykule przedstawiono sposób tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu szablonu usługi Resource Manager i programu Azure PowerShell. Ten przewodnik Szybki Start koncentruje się na proces wdrażania szablonu usługi Resource Manager, aby utworzyć magazyn usług odzyskiwania. Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [dokumentacji usługi Resource Manager](/azure/azure-resource-manager/) i [odwołanie do szablonu](/azure/templates/microsoft.recoveryservices/allversions).

Alternatywnie można utworzyć kopię zapasową maszyny Wirtualnej przy użyciu [programu Azure PowerShell](./quick-backup-vm-powershell.md), [wiersza polecenia platformy Azure](quick-backup-vm-cli.md), lub [witryny Azure portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Utwórz Maszynę wirtualną i magazyn usługi Recovery Services

A [magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, która przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Szablon używany w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ten szablon umożliwia wdrożenie prostego Windows maszyny Wirtualnej i magazynu usługi Recovery Services skonfigurowano DefaultPolicy do ochrony.

Aby wdrożyć szablon, zaznacz **wypróbuj** aby otworzyć usługa Azure Cloud shell, a następnie wklej poniższy skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy w oknie shell, a następnie wybierz pozycję **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Program Azure PowerShell jest używane do wdrażania szablonu usługi Resource Manager w taki sposób, w tym przewodniku Szybki Start. [Witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), i [interfejsu API Rest](../azure-resource-manager/resource-group-template-deploy-rest.md) może również służyć do wdrażania szablonów.

## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Szablon umożliwia utworzenie maszyny Wirtualnej i umożliwia ponownie na maszynie Wirtualnej. Po wdrożeniu szablonu, należy uruchomić zadanie tworzenia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [uruchomić zadanie tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować zadanie tworzenia kopii zapasowej, zobacz [monitorować zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Czyszczenie wdrożenia

Jeśli nie są już potrzebne do tworzenia kopii zapasowej maszyny Wirtualnej, możesz je wyczyścić.

- Jeśli chcesz wypróbować funkcję przywracania maszyny Wirtualnej, Pomiń oczyszczanie się.
- Jeśli użyto istniejącej maszyny Wirtualnej, możesz pominąć końcowe [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) polecenia cmdlet, aby pozostawić grupę zasobów i maszynę Wirtualną na miejscu.

Wyłącz ochronę, usunąć punkty przywracania oraz Magazyn. Następnie usuń grupę zasobów i skojarzone zasoby maszyny Wirtualnej w następujący sposób:

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
