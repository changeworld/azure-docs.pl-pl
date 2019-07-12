---
title: Tworzenie maszyny Wirtualnej z systemem Windows na podstawie szablonu na platformie Azure | Dokumentacja firmy Microsoft
description: Z łatwością tworzyć nową maszynę Wirtualną Windows, należy użyć szablonu usługi Resource Manager i programu PowerShell.
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
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70a9680b9f8696cd40cb5631217861dc6d1d7ad8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719904"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Utwórz maszynę wirtualną Windows za pomocą szablonu usługi Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną Windows przy użyciu szablonu usługi Azure Resource Manager i programu Azure PowerShell w usłudze Azure Cloud shell. Do szablonu używanego w tym artykule wdraża pojedynczej maszyny wirtualnej z systemem Windows Server w nowej sieci wirtualnej z jedną podsiecią. W celu tworzenia maszyny wirtualnej systemu Linux, zobacz [jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonów usługi Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure, zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
- Tworzy maszynę wirtualną.

Poniższy przykład obejmuje tworzenie maszyny Wirtualnej z [szablonu szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Oto kopię szablonu:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Aby uruchomić skrypt programu PowerShell, zaznacz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

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

Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie zamiast z w usłudze Azure Cloud shell, ten samouczek wymaga modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

W poprzednim przykładzie określono szablonu przechowywanego w usłudze GitHub. Możesz również pobrać lub utworzyć szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

Poniżej przedstawiono dodatkowe zasoby:

- Aby dowiedzieć się, jak opracowywać szablony usługi Resource Manager, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).
- Aby wyświetlić schematy maszyny wirtualnej platformy Azure, zobacz [odwołanie do szablonu usługi Azure](/azure/templates/microsoft.compute/allversions).
- Aby zobaczyć więcej przykładów szablonu maszyny wirtualnej, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Ostatnie polecenie programu PowerShell z poprzednim skrypcie wyświetlana nazwa maszyny wirtualnej. Aby połączyć z maszyną wirtualną, zobacz [jak połączyć i logowanie się na maszynie wirtualnej platformy Azure, systemem Windows](./connect-logon.md).

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpiły problemy dotyczące wdrożenia, może zająć się [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Dowiedz się, jak tworzyć i zarządzać nimi maszynę wirtualną w [Utwórz Windows maszyn wirtualnych i zarządzanie przy użyciu modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby dowiedzieć się więcej na temat tworzenia szablonów, wyświetlić składnię JSON i właściwości dla typów zasobów, które można wdrożyć:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
