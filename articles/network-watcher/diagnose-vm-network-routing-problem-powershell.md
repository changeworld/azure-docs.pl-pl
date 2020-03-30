---
title: Diagnozowanie problemu z routingiem sieci maszyny Wirtualnej — usługa Azure PowerShell
titleSuffix: Azure Network Watcher
description: W tym artykule dowiesz się, jak zdiagnozować problem routingu sieci maszyny wirtualnej przy użyciu funkcji następnego przeskoku usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: b5a636471eab188dc8648761afedd81694331953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834709"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnozowanie problemu z routingiem sieci maszyny wirtualnej — usługa Azure PowerShell

W tym artykule można wdrożyć maszynę wirtualną (VM), a następnie sprawdzić komunikację na adres IP i adres URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, `Az` ten artykuł wymaga modułu programu Azure PowerShell. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.



## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Wykonywanie pozostałych kroków możesz zacząć dopiero wtedy, gdy maszyna wirtualna zostanie utworzona i program PowerShell zwróci dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową z obserwatorem sieci, należy najpierw włączyć obserwatora sieci w regionie, w którego znajduje się maszyna wirtualna, w której chcesz przetestować, a następnie użyć funkcji następnego przeskoku obserwatora sieci, aby przetestować komunikację.

## <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączonego obserwatora sieci w regionie Wschodnie stany USA, użyj [programu Get-AzNetworkWatcher,](/powershell/module/az.network/get-aznetworkwatcher) aby pobrać obserwatora sieci. Poniższy przykład pobiera istniejącą usługę Network Watcher o nazwie *NetworkWatcher_eastus* znajdującą się w grupie zasobów *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Jeśli nie masz jeszcze włączonego obserwatora sieci w regionie Wschodnie stany USA, użyj [new-AzNetworkWatcher,](/powershell/module/az.network/new-aznetworkwatcher) aby utworzyć obserwatora sieci w regionie Wschodnie stany USA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować routing z maszyny Wirtualnej, należy użyć polecenia [Get-AzNetworkWatcherNextHop,](/powershell/module/az.network/get-aznetworkwatchernexthop) aby określić następny przeskok routingu, gdy ruch jest przeznaczony dla określonego adresu.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po kilku sekundach dane wyjściowe informują, że **NextHopType** to **Internet,** a **RouteTableId** to **Trasa systemowa**. Ten wynik informuje, że istnieje prawidłowa trasa do miejsca docelowego.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Zwrócone dane wyjściowe informuje, że **Brak** jest **NextHopType**, i że **RouteTableId** jest również **trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby dokładniej przeanalizować routing, przejrzyj skuteczne trasy interfejsu sieciowego za pomocą polecenia [Get-AzEffectiveRouteTable:](/powershell/module/az.network/get-azeffectiveroutetable)

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Dane wyjściowe, które zawierają następujący tekst jest zwracany:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak widać w poprzednim wyjściu, trasa z **AddressPrefix** **0.0.0.0/0** trasy cały ruch nie jest przeznaczony dla adresów w ramach prefiksów adresu innej trasy z następnym przeskokiem **Internet**. Jak widać również na wyjściu, choć istnieje domyślna trasa do prefiksu 172.16.0.0/12, który zawiera adres 172.31.0.100, **nextHopType** jest **Brak**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodano zakres adresów 172.16.0.0/12 do przestrzeni adresowej sieci wirtualnej, platforma Azure zmieni **nextHopType** na **sieć wirtualną** dla trasy. Sprawdzanie będzie następnie **wyświetlić sieci wirtualnej** jako **nextHopType**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną i zdiagnozowano routing sieci z maszyny Wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku wychodzących połączeń maszyn wirtualnych można również określić opóźnienie oraz dozwolony i odrzucony ruch sieciowy między maszyną wirtualną a punktem końcowym przy użyciu funkcji [rozwiązywania problemów z połączeniem](network-watcher-connectivity-powershell.md) obserwatora sieci. Komunikację między maszyną wirtualną a punktem końcowym, taką jak adres IP lub adres URL, można monitorować w czasie, korzystając z funkcji monitora połączenia obserwatora sieci. Aby dowiedzieć się, jak to zrobić, zobacz [Monitorowanie połączenia sieciowego](connection-monitor.md).
