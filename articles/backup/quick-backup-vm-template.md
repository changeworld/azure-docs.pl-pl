---
title: Przewodnik Szybki Start platformy Azure — tworzenie kopii zapasowej maszyny wirtualnej przy użyciu szablonu Menedżer zasobów
description: Dowiedz się, jak utworzyć kopię zapasową maszyn wirtualnych przy użyciu szablonu Azure Resource Manager
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7075e127192635c08aa2da2b4798dea6d6abb13b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639414"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure przy użyciu szablonu Menedżer zasobów

[Azure Backup](backup-overview.md) tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule opisano sposób tworzenia kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu szablonu Menedżer zasobów i Azure PowerShell. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Menedżer zasobów w celu utworzenia magazynu usługi Recovery Services. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.recoveryservices/allversions).

Alternatywnie można utworzyć kopię zapasową maszyny wirtualnej przy użyciu [Azure PowerShell](./quick-backup-vm-powershell.md), [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md)lub [Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Tworzenie maszyny wirtualnej i magazynu Recovery Services

[Magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, który przechowuje dane kopii zapasowej chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ten szablon pozwala wdrożyć prostą maszynę wirtualną z systemem Windows i magazyn Recovery Services skonfigurowany przy użyciu DefaultPolicy na potrzeby ochrony.

Aby wdrożyć szablon, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

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

Azure PowerShell służy do wdrażania szablonu Menedżer zasobów w tym przewodniku Szybki Start. Do wdrażania szablonów można także używać [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), interfejsu [wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)i [interfejsów API REST](../azure-resource-manager/resource-group-template-deploy-rest.md) .

## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Szablon tworzy maszynę wirtualną i włącza ją z powrotem na maszynie wirtualnej. Po wdrożeniu szablonu należy uruchomić zadanie tworzenia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować zadanie tworzenia kopii zapasowej, zobacz [monitorowanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Wyczyść wdrożenie

Jeśli kopia zapasowa maszyny wirtualnej nie jest już potrzebna, można ją wyczyścić.

- Jeśli chcesz wypróbować przywracanie maszyny wirtualnej, Pomiń oczyszczanie.
- Jeśli użyto istniejącej maszyny wirtualnej, możesz pominąć końcowe polecenie cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , aby pozostawić grupę zasobów i maszynę wirtualną.

Wyłącz ochronę, Usuń punkty przywracania i magazyn. Następnie Usuń grupę zasobów i skojarzone z nią zasoby maszyn wirtualnych w następujący sposób:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania.

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) utworzyć kopię zapasową maszyn wirtualnych w Azure Portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) szybko przywrócić maszynę wirtualną
