---
title: Diagnozowanie maszyny wirtualnej problemu z routingiem sieciowym - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak diagnozować maszyny wirtualnej problemu z routingiem sieciowym za pomocą następnego przeskoku możliwości usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 08d273ce6e6ecb1b10d3c39a0954d430a3cb674a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730749"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnozowanie maszyny wirtualnej problemu z routingiem sieciowym - programu Azure PowerShell

W tym artykule Wdróż maszynę wirtualną (VM), a następnie sprawdź komunikaty do adresu IP i adres URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, ten artykuł wymaga programu Azure PowerShell `Az` modułu. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.



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

Aby przetestować komunikacji sieciowej przy użyciu usługi Network Watcher, należy najpierw włączyć usługi network watcher w regionie, w którym znajduje się w maszynie Wirtualnej, która ma zostać przetestowana i następnie za pomocą usługi Network Watcher następnego przeskoku możliwości testowania komunikacji.

## <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już usługę network watcher włączone w regionie wschodnie stany USA, należy użyć [Get AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) można pobrać usługi network watcher. Poniższy przykład pobiera istniejącą usługę Network Watcher o nazwie *NetworkWatcher_eastus* znajdującą się w grupie zasobów *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Jeśli nie masz jeszcze usługi network watcher włączone w regionie wschodnie stany USA, należy użyć [New AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) do utworzenia usługi network watcher w regionie wschodnie stany USA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować routing z maszyny Wirtualnej, użyj [Get AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) polecenie, aby określić następnego przeskoku routingu, gdy ruch jest przeznaczony dla określonego adresu.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po kilku sekundach, dane wyjściowe informujący o tym, **Typ następnego przeskoku** jest **Internet**oraz że **RouteTableId** jest **trasa systemowa**. Ten wynik poinformuje Cię o tym, że jest prawidłową trasę do lokalizacji docelowej.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Dane wyjściowe zwracane informujący o tym, **Brak** jest **Typ następnego przeskoku**oraz że **RouteTableId** jest również **trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby analizować dalsze routingu, zapoznaj się z obowiązujące trasy dla interfejsu sieciowego z [Get AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) polecenia:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Jest zwracany danych wyjściowych, który zawiera następujący tekst:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak widać w danych wyjściowych poprzedniego, z trasą **AddressPrefix** z **0.0.0.0/0** kieruje cały ruch, które nie są przeznaczone dla adresów w obrębie innej trasy prefiksy adresów z następnym przeskokiem **Internet**. Jak widać również w danych wyjściowych, mimo że ma domyślną trasę do prefiksu 172.16.0.0/12, która zawiera 172.31.0.100 adres, **Typ następnego przeskoku** jest **Brak**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodano 172.16.0.0/12 zakresu adresów przestrzeni adresowej sieci wirtualnej, zmieni się Azure **Typ następnego przeskoku** do **sieć wirtualna** dla danej trasy. Następnie pokazywałaby sprawdzenie **sieć wirtualna** jako **Typ następnego przeskoku**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będą już potrzebne, możesz użyć [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono Maszynę wirtualną i zdiagnozować, routing w sieci z maszyny Wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Dla połączeń wychodzących maszyny Wirtualnej, można również określić opóźnienie i dozwolonych i niedozwolonych ruch sieciowy między maszyną Wirtualną i punkt końcowy korzystający z usługi Network Watcher [Rozwiązywanie problemów z połączeniami](network-watcher-connectivity-powershell.md) możliwości. Można monitorować komunikację między maszyny Wirtualnej i punktu końcowego, takie jak adres IP lub adres URL, wraz z upływem czasu przy użyciu funkcji monitor połączeń usługi Network Watcher. Aby dowiedzieć się więcej, zobacz temat [monitorowanie połączenia sieciowego](connection-monitor.md).
