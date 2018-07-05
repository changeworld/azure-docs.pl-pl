---
title: Tworzenie maszyny Wirtualnej Windows w usłudze Azure Cloud Shell za pomocą uproszczonego cmdlet New-AzureRMVM | Dokumentacja firmy Microsoft
description: Szybka nauka tworzenia maszyn wirtualnych Windows za pomocą polecenia cmdlet New-AzureRMVM uproszczone w usłudze Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: ede8fab67c04eb7ce8d26280de2d1563b6cc8ad2
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435673"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Utwórz maszynę wirtualną Windows za pomocą polecenia cmdlet New-AzureRMVM uproszczone w usłudze Cloud Shell 

[New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) polecenia cmdlet dodano uproszczony zestaw parametrów do tworzenia nowej maszyny Wirtualnej przy użyciu programu PowerShell. W tym temacie przedstawiono sposób używania programu PowerShell w usłudze Azure Cloud Shell z najnowszą wersją polecenia cmdlet New-AzureVM wstępnie zainstalowane, aby utworzyć nową maszynę Wirtualną. Firma Microsoft użyje zestaw uproszczone parametr, który automatycznie utworzy wszystkie wymagane zasoby przy użyciu inteligentnych wartości domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.1.1 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Możesz użyć [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) polecenia cmdlet, aby utworzyć Maszynę wirtualną przy użyciu domyślnych ustawień inteligentnych, obejmujących przy użyciu obrazu systemu Windows Server 2016 Datacenter z witryny Azure Marketplace. Możesz użyć polecenia New-AzureRMVM przy użyciu tylko **— nazwa** parametru i będzie używać tej wartości dla wszystkich nazw zasobów. W tym przykładzie parametr **-Nazwa** zostanie ustawiony na wartość *myVM*. 

Upewnij się, że w usłudze Cloud Shell wybrano program **PowerShell** i wpisz:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Pojawi się monit o utworzenie nazwy użytkownika i hasła dla maszyny wirtualnej. Dane te będą używane do połączenia się z maszyną wirtualną w dalszej części tego tematu. Hasło musi mieć długość od 12 do 123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: mała litera, wielka litera, cyfra i znak specjalny.

Utworzenie maszyny wirtualnej i skojarzonych zasobów trwa około minutę. Gdy skończysz, możesz zobaczyć wszystkie zasoby, które zostały utworzone przy użyciu [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) polecenia cmdlet.

```azurepowershell-interactive
Get-AzureRmResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Po zakończeniu wdrożenia utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

Wróć do publicznego adresu IP maszyny wirtualnej za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Zanotuj ten adres IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Na komputerze lokalnym, otwórz wiersz polecenia i użyj **mstsc** polecenie, aby uruchomić sesję usług pulpitu zdalnego z nową maszyną Wirtualną. Zamień &lt;publiczny adres IP&gt; na adres IP maszyny wirtualnej. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło podane podczas tworzenia maszyny wirtualnej.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Określ inny zasób nazwy

Zapewniają również bardziej opisowe nazwy zasobów i nadal masz ich tworzone automatycznie. Oto przykład gdzie możemy mają nazwane wiele zasobów dla nowej maszyny Wirtualnej, w tym nową grupę zasobów.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym temacie wdrożyliśmy prostą maszynę wirtualną przy użyciu polecenia New-AzVM, a następnie połączyliśmy się z nią za pośrednictwem protokołu RDP. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
