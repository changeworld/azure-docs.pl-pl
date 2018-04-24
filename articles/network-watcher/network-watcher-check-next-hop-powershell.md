---
title: "Znajdź następny przeskok z Azure sieci obserwatora następnego przeskoku - PowerShell | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak znaleźć, co to jest typ następnego przeskoku i adres ip za pomocą następnego przeskoku przy użyciu programu PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Sprawdź, jaki typ następnego przeskoku jest przy użyciu funkcji w następnym przeskoku w obserwatora sieciowego Azure przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-next-hop-portal.md)
> - [Program PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-next-hop-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-next-hop-rest.md)

Następny przeskok jest funkcją obserwatora sieciowego, który zapewnia możliwość get Typ następnego przeskoku i adres IP na podstawie określonej maszyny wirtualnej. Ta funkcja jest przydatna przy określaniu, jeśli ruchu wychodzącego maszyny wirtualnej są przesyłane za pośrednictwem bramy, Internetu lub sieci wirtualne na uzyskanie dostępu do miejsca docelowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu użyjesz portalu Azure można znaleźć Typ następnego przeskoku i adres IP.

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule używa następnego przeskoku, funkcja obserwatora sieciowego, który znajduje się typ następnego przeskoku i adres IP dla każdego zasobu. Aby dowiedzieć się więcej na temat następnego przeskoku, odwiedź stronę [następnego przeskoku omówienie](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Pierwszym krokiem jest pobrać wystąpienia obserwatora sieciowego. `$networkWatcher` Zmienna jest przekazywana do następnego przeskoku Sprawdź polecenia cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Uzyskiwanie maszyny wirtualnej

Następny przeskok zwraca następnego przeskoku i adres IP następnego przeskoku z maszyny wirtualnej. Identyfikator maszyny wirtualnej jest wymagany dla polecenia cmdlet. Jeśli znasz już identyfikator maszyny wirtualnej do użycia, możesz pominąć ten krok.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Następnego przeskoku wymaga, aby zasobu maszyny Wirtualnej jest przydzielona do uruchomienia.

## <a name="get-the-network-interfaces"></a>Pobierz interfejsy sieciowe

Adres IP karty sieciowej na maszynie wirtualnej jest potrzebne w tym przykładzie Pobieramy kart sieciowych w maszynie wirtualnej. Jeśli znasz już adres IP, który ma zostać przetestowana na maszynie wirtualnej, można pominąć ten krok.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Pobierz następnego skoku

Teraz nazywamy `Get-AzureRmNetworkWatcherNextHop` polecenia cmdlet. Polecenie cmdlet jest przekazywana obserwatora sieciowego, maszynę wirtualną identyfikator źródłowy adres IP i docelowego adresu IP. W tym przykładzie docelowy adres IP jest z maszyną wirtualną w innej sieci wirtualnej. Brak bramy sieci wirtualnej między dwiema sieciami wirtualnymi.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Przegląd wyników

Po zakończeniu znajdują się wyniki. A także typ zasobu, który jest zwracany jest adres IP następnego przeskoku. W tym scenariuszu jest publiczny adres IP bramy sieci wirtualnej.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

Poniższa lista zawiera aktualnie dostępne wartości Typ następnego przeskoku:

**Typ następnego przeskoku**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak programowo Przejrzyj ustawienia sieciowe grupy zabezpieczeń, odwiedzając [NSG inspekcji z obserwatora sieciowego](network-watcher-nsg-auditing-powershell.md)

















