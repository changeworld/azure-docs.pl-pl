---
title: Diagnozowanie maszyny wirtualnej routingu problem z siecią - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak zdiagnozować problem routingu sieci maszyny wirtualnej przy użyciu funkcji w następnym przeskoku obserwatora sieci platformy Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnozowanie maszyny wirtualnej routingu problem z siecią - programu Azure PowerShell

W tym artykule należy wdrożyć maszynę wirtualną (VM), a następnie sprawdź komunikacji z adresu IP i adres URL. Należy określić przyczynę awarii łączności i jak można go usunąć.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga środowiska AzureRM PowerShell wersji modułu 5.4.1 lub nowszym. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable AzureRM`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny Wirtualnej, należy utworzyć grupę zasobów, aby zawierała maszyny Wirtualnej. Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Tworzenie maszyny Wirtualnej z [nowe AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj pozostałe kroki dopiero po utworzeniu maszyny Wirtualnej i programu PowerShell zwraca dane wyjściowe.

## <a name="test-network-communication"></a>Test łączności sieciowej

Aby przetestować komunikacji sieciowej z obserwatora sieciowego, należy najpierw włączyć obserwatora sieciowego, w regionie, w którym znajduje się w maszynie Wirtualnej, która ma zostać przetestowana, a następnie użyć obserwatora sieciowego następnego przeskoku możliwości do testowania łączności.

## <a name="enable-network-watcher"></a>Włącz obserwatora sieciowego

Jeśli masz już obserwatora sieciowego, włączona w regionie wschodnie stany USA, użyj [Get AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) można pobrać obserwatora sieciowego. Poniższy przykład pobiera istniejących obserwatora sieciowego o nazwie *NetworkWatcher_eastus* w *NetworkWatcherRG* grupy zasobów:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Jeśli nie masz jeszcze obserwatora sieciowego, włączona w regionie wschodnie stany USA, użyj [AzureRmNetworkWatcher nowy](/powershell/module/azurerm.network/new-azurermnetworkwatcher) utworzyć obserwatora sieciowego w regionie wschodnie stany USA:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Użyj następnego skoku

Azure automatycznie tworzy trasy do domyślnej lokalizacji docelowych. Można utworzyć trasy niestandardowe, które zastępują trasy domyślnej. Czasami trasy niestandardowe może spowodować, że komunikacja się nie powieść. Aby przetestować routingu z maszyny Wirtualnej, użyj [Get AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) polecenie w celu ustalenia następnego przeskoku routingu ruchu jest przeznaczony dla określonego adresu.

Test komunikacji wychodzącej z maszyny Wirtualnej do jednego z adresów IP dla www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po kilku sekundach, dane wyjściowe informuje o który **Typ następnego przeskoku** jest **Internet**oraz że **RouteTableId** jest **trasa systemowa**. Wynik tego informuje o tym, czy jest prawidłową trasę do miejsca docelowego.

Przetestuj komunikacja wychodząca z maszyny Wirtualnej 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Dane wyjściowe zwrócone informuje o który **Brak** jest **Typ następnego przeskoku**oraz że **RouteTableId** jest również **trasa systemowa**. Wynik tego informuje o tym, gdy istnieje trasa prawidłowego systemu docelowego, brak nie następnego przeskoku do kierowania ruchem do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetl szczegóły trasy

Do analizy, dalsze routingu, przejrzyj skuteczne trasy dla interfejsu sieciowego z [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) polecenia:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Zwrócono dane wyjściowe, który zawiera następujący tekst:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak widać w poprzednim danych wyjściowych z trasą **AaddressPrefix** z **0.0.0.0/0** rozsyła cały ruch, które nie są przeznaczone dla adresów w obrębie prefiksy adresów innych tras z następnego przeskoku **Internet**. Jak również widoczna w danych wyjściowych, jednak istnieje trasa domyślna do prefiksu 172.16.0.0/12, który zawiera 172.31.0.100 adres **Typ następnego przeskoku** jest **Brak**. Azure tworzy trasę domyślną elementom 172.16.0.0/12, ale nie określono typu następnego przeskoku, dopóki ma powodu do. Jeśli na przykład zakres adresów 172.16.0.0/12 dodano do przestrzeni adresowej sieci wirtualnej, Azure zmiany **Typ następnego przeskoku** do **sieci wirtualnej** dla danej trasy. Sprawdzenie czy następnie następuje wyświetlenie **sieci wirtualnej** jako **Typ następnego przeskoku**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzyć Maszynę wirtualną i zdiagnozować, routingu sieciowego z maszyny Wirtualnej. Wiesz, że Azure tworzy kilka tras domyślnych i przetestowane routingu do dwóch różnych miejsc docelowych. Dowiedz się więcej o [routing na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) oraz sposób [tworzenia niestandardowych tras](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Dla połączeń wychodzących maszyny Wirtualnej, można również określić opóźnienie i dozwolonych i zabronionych ruch sieciowy między maszyny Wirtualnej i punktu końcowego za pomocą Monitora sieci [Rozwiązywanie problemów z połączenia](network-watcher-connectivity-powershell.md) możliwości. Można monitorować komunikację między maszyny Wirtualnej i punktu końcowego, takie jak adres IP lub adres URL, w czasie przy użyciu możliwości monitor połączenia obserwator sieci. Aby dowiedzieć się więcej, zobacz temat [monitorować połączenia sieciowego](connection-monitor.md).