---
title: Tworzenie sieci wirtualnej platformy Azure - PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się szybko utworzyć sieć wirtualną przy użyciu programu PowerShell. Sieć wirtualna umożliwia zasobów platformy Azure, takich jak maszyny wirtualne do komunikowania się przez użytkowników ze sobą i z Internetu.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fe171000f83c27f23972569b93e351340f4426ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Tworzenie sieci wirtualnej przy użyciu programu PowerShell

Sieci wirtualnej umożliwia zasobów platformy Azure, takich jak maszyn wirtualnych (VM) do prywatnie komunikowania się ze sobą i z Internetu. W tym artykule należy Dowiedz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, możesz wdrożyć dwóch maszyn wirtualnych w sieci wirtualnej. Następnie podłącz do jednej maszyny Wirtualnej z Internetu, a prywatnie komunikacji między dwiema maszynami wirtualnymi.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga środowiska AzureRM PowerShell wersji modułu 5.4.1 lub nowszym. Aby znaleźć zainstalowanej wersji, uruchom ` Get-Module -ListAvailable AzureRM`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów, aby zawierała sieci wirtualnej. Utwórz nową grupę zasobów o [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy domyślną sieci wirtualnej o nazwie *myVirtualNetwork* w *EastUS* lokalizacji:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Zasobów platformy Azure są wdrażane dla podsieci sieci wirtualnej, dlatego należy utworzyć podsieć. Tworzenie konfiguracji podsieci z [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapisać konfiguracji podsieci do sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), co powoduje podsieci w sieci wirtualnej:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny Wirtualnej

Utwórz maszynę Wirtualną z [nowe AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Po uruchomieniu polecenia znajdujący się zostanie wyświetlony monit o poświadczenia. Wartości, które należy wprowadzić są skonfigurowane jako nazwy użytkownika i hasła dla maszyny Wirtualnej. `-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Dane wyjściowe podobne do następujących przykładowe dane wyjściowe są zwracane, a Azure rozpoczyna tworzenie maszyny Wirtualnej w tle.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny Wirtualnej 

Wprowadź następujące polecenie:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Maszyna wirtualna ma kilka minut na utworzenie. Nie należy kontynuować do następnego kroku, dopóki nie wykonuje poprzednie polecenie i dane wyjściowe są zwracane do programu PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Połączenie z maszyną wirtualną z Internetu

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) do zwrócenia publiczny adres IP maszyny wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVm1* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Zastąp `<publicIpAddress>` w poniższym poleceniu z publicznego adresu IP adres zwrócony z poprzedniego polecenia, a następnie wprowadź następujące polecenie: 

```
mstsc /v:<publicIpAddress>
```

Plik protokołu Remote Desktop Protocol (RDP) jest utworzony i pobrana na komputer. Otwórz plik rdp pobranego. Po wyświetleniu monitu wybierz **Connect**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej. Musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić poświadczenia zostały wprowadzone podczas tworzenia maszyny Wirtualnej. Kliknij przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz **tak** lub **Kontynuuj**, aby nawiązać połączenie.

## <a name="communicate-privately-between-vms"></a>Prywatnie komunikacji między maszynami wirtualnymi

Z programu PowerShell na *myVm1* maszyny Wirtualnej, wprowadź `ping myvm2`. Polecenie ping nie powiedzie się, ponieważ używa polecenia ping protokołu komunikacyjnego sterowania Internetem (ICMP) i ICMP nie jest dozwolona przez zaporę systemu Windows, domyślnie.

Aby umożliwić *myVm2* na polecenie ping *myVm1* w kolejnym kroku, wprowadź następujące polecenie z programu PowerShell, który umożliwia ICMP dla ruchu przychodzącego przez zaporę systemu Windows:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Zamknij Podłączanie pulpitu zdalnego do *myVm1*. 

Wykonaj kroki [połączenie z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet) ponownie nawiązać połączenie, ale *myVm2*. 

W wierszu polecenia na *myVm2* maszyny Wirtualnej, wprowadź `ping myvm1`.

Pojawi się odpowiedzi od *myVm1*, ponieważ dozwolone ICMP przez zaporę systemu Windows na *myVm1* maszyny Wirtualnej w poprzednim kroku.

Zamknij Podłączanie pulpitu zdalnego do *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można użyć [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule tworzone są domyślne sieci wirtualnej i dwie maszyny wirtualne. Połączone z jedną maszynę Wirtualną z Internetu i prywatnie przekazywane między maszyny Wirtualnej i inną maszynę Wirtualną. Aby dowiedzieć się więcej na temat ustawień sieci wirtualnej, zobacz [Zarządzanie sieci wirtualnej](manage-virtual-network.md). 

Domyślnie program Azure umożliwia nieograniczony prywatnej komunikacji między maszynami wirtualnymi, ale tylko maszyn wirtualnych systemu Windows umożliwia przychodzących połączeń pulpitu zdalnego z Internetu. Aby dowiedzieć się ograniczyć różnego rodzaju komunikacji sieciowej do i z maszyn wirtualnych, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md)
