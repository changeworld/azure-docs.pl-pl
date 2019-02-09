---
title: Tworzenie maszyny Wirtualnej Windows za pomocą uproszczonego polecenia cmdlet New-AzVM usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Szybka nauka tworzenia maszyn wirtualnych Windows za pomocą uproszczonego polecenia cmdlet New-AzVM usługi Azure Cloud Shell.
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
ms.openlocfilehash: 32862d06cfa6a9a8dd9b99459362ec53fd88cec2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978969"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Utwórz maszynę wirtualną Windows za pomocą polecenia cmdlet New-AzVM uproszczone w usłudze Cloud Shell 

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) polecenia cmdlet dodano uproszczony zestaw parametrów do tworzenia nowej maszyny Wirtualnej przy użyciu programu PowerShell. W tym temacie przedstawiono sposób używania programu PowerShell w usłudze Azure Cloud Shell z najnowszą wersją polecenia cmdlet New-AzureVM wstępnie zainstalowane, aby utworzyć nową maszynę Wirtualną. Firma Microsoft użyje zestaw uproszczone parametr, który automatycznie utworzy wszystkie wymagane zasoby przy użyciu inteligentnych wartości domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Polecenie cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) pozwala utworzyć maszynę wirtualną przy użyciu inteligentnych ustawień domyślnych, umożliwiających na przykład użycie obrazu systemu Windows Server 2016 Datacenter pochodzącego z witryny Azure Marketplace. Można użyć New-AzVM, za pomocą tylko **— nazwa** parametru i będzie używać tej wartości dla wszystkich nazw zasobów. W tym przykładzie parametr **-Nazwa** zostanie ustawiony na wartość *myVM*. 

Upewnij się, że w usłudze Cloud Shell wybrano program **PowerShell** i wpisz:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Pojawi się monit o utworzenie nazwy użytkownika i hasła dla maszyny wirtualnej. Dane te będą używane do połączenia się z maszyną wirtualną w dalszej części tego tematu. Hasło musi mieć długość od 12 do 123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: mała litera, wielka litera, cyfra i znak specjalny.

Utworzenie maszyny wirtualnej i skojarzonych zasobów trwa około minutę. Gdy skończysz, możesz zobaczyć wszystkie zasoby, które zostały utworzone przy użyciu [Get AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) polecenia cmdlet.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Po zakończeniu wdrożenia utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

Użyj [Get AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) polecenie, aby zwrócić publiczny adres IP maszyny wirtualnej. Zanotuj ten adres IP.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Na komputerze lokalnym, otwórz wiersz polecenia i użyj **mstsc** polecenie, aby uruchomić sesję usług pulpitu zdalnego z nową maszyną Wirtualną. Zamień &lt;publiczny adres IP&gt; na adres IP maszyny wirtualnej. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło podane podczas tworzenia maszyny wirtualnej.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Określ inny zasób nazwy

Zapewniają również bardziej opisowe nazwy zasobów i nadal masz ich tworzone automatycznie. Oto przykład gdzie możemy mają nazwane wiele zasobów dla nowej maszyny Wirtualnej, w tym nową grupę zasobów.

```azurepowershell-interactive
New-AzVm `
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

Jeśli nie będą już potrzebne, możesz użyć [AzResourceGroup Usuń](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) polecenia, aby usunąć grupę zasobów maszyny Wirtualnej i wszystkie pokrewne zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym temacie wdrożyliśmy prostą maszynę wirtualną przy użyciu polecenia New-AzVM, a następnie połączyliśmy się z nią za pośrednictwem protokołu RDP. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
