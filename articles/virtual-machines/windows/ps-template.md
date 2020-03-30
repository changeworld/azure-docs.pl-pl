---
title: Tworzenie maszyny Wirtualnej systemu Windows na podstawie szablonu na platformie Azure
description: Użyj szablonu Menedżera zasobów i programu PowerShell, aby łatwo utworzyć nową maszynę wirtualną systemu Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065548"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Tworzenie maszyny wirtualnej systemu Windows na podstawie szablonu Menedżera zasobów

Dowiedz się, jak utworzyć maszynę wirtualną systemu Windows przy użyciu szablonu usługi Azure Resource Manager i programu Azure PowerShell z powłoki usługi Azure Cloud. Szablon użyty w tym artykule wdraża pojedynczą maszynę wirtualną z systemem Windows Server w nowej sieci wirtualnej z pojedynczą podsiecią. Aby utworzyć maszynę wirtualną systemu Linux, zobacz [Jak utworzyć maszynę wirtualną systemu Linux za pomocą szablonów usługi Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
- Tworzy maszynę wirtualną.

Poniższy przykład tworzy maszynę wirtualną z [szablonu Szybki start platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Oto kopia szablonu:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Aby uruchomić skrypt programu PowerShell, wybierz pozycję **Spróbuj,** aby otworzyć powłokę usługi Azure Cloud. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie zamiast z powłoki usługi Azure Cloud, ten samouczek wymaga modułu programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

W poprzednim przykładzie określono szablon przechowywany w usłudze GitHub. Można również pobrać lub utworzyć szablon i określić ścieżkę lokalną z parametrem. `--template-file`

Oto kilka dodatkowych zasobów:

- Aby dowiedzieć się, jak tworzyć szablony Usługi Resource Manager, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
- Aby wyświetlić schematy maszyny wirtualnej platformy Azure, zobacz [odwołanie do szablonu platformy Azure](/azure/templates/microsoft.compute/allversions).
- Aby wyświetlić więcej przykładów szablonów maszyn wirtualnych, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Ostatnie polecenie programu PowerShell z poprzedniego skryptu pokazuje nazwę maszyny wirtualnej. Aby połączyć się z maszyną wirtualną, zobacz [Jak połączyć się i zalogować się na maszynie wirtualnej platformy Azure z systemem Windows](./connect-logon.md).

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpiły problemy z wdrożeniem, możesz przyjrzeć się [rozwiązywaniu typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager.](../../resource-manager-common-deployment-errors.md)
- Dowiedz się, jak tworzyć maszynę wirtualną i zarządzać nią w [programie Tworzenie maszyn wirtualnych systemu Windows i zarządzanie nimi za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby dowiedzieć się więcej na temat tworzenia szablonów, wyświetl składnię JSON i właściwości wdrożonych typów zasobów:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Sieć Microsoft.Network/virtual Sieci](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
