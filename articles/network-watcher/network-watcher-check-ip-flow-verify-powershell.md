---
title: Sprawdź ruchu z adresem IP obserwatora sieci Azure przepływ weryfikacji - programu PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób sprawdzania, jeśli ruch do i z maszyny wirtualnej jest dozwolona lub odmowa przy użyciu programu PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7c2d5e0811f7a5e1f865992be1d5a2c189f10374
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809931"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Sprawdź, czy ruch jest dozwolony lub odrzucany do lub z maszyny Wirtualnej z przepływem IP Sprawdź składnik Azure obserwatora sieciowego

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [Program PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Sprawdź przepływ IP jest funkcją obserwatora sieciowego, który pozwala sprawdzić, jeśli ruch jest dozwolony na lub z maszyny wirtualnej. Ten scenariusz przydaje się pobrać bieżący stan tego, czy maszyny wirtualne mogą komunikować się z zasobu zewnętrznego i wewnętrznej bazy danych. Sprawdź przepływ IP pozwala sprawdzić, czy reguł sieciowej grupy zabezpieczeń (NSG) są prawidłowo skonfigurowane i rozwiązywania problemów z przepływów, które są blokowane przez reguły NSG. Inną przyczyną przy użyciu protokołu IP przepływu Sprawdź jest zapewnienie ruchu, który ma być zablokowana jest prawidłowo blokowane przez grupy NSG.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) do tworzenia obserwatora sieciowego lub mieć istniejące wystąpienie obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Ten scenariusz używa IP przepływ Sprawdź, aby sprawdzić, czy maszyna wirtualna może komunikować się znanego adresu IP usługi Bing. Jeśli ruch jest niedozwolone, zwraca zasady zabezpieczeń, która odrzuciła ten ruch. Aby dowiedzieć się więcej na temat przepływu IP sprawdzić, odwiedź stronę [przepływu IP Sprawdź — omówienie](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Pierwszym krokiem jest pobrać wystąpienia obserwatora sieciowego. `$networkWatcher` Zmienna jest przekazywana do adresu IP przepływu Sprawdź polecenia cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Przepływ IP Sprawdź testy ruchu do lub z adresu IP na maszynie wirtualnej lub zdalnego miejsca docelowego. Identyfikator maszyny wirtualnej jest wymagany dla polecenia cmdlet. Jeśli znasz już identyfikator maszyny wirtualnej do użycia, możesz pominąć ten krok.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Pobierz karty interfejsu Sieciowego

Wymagany jest adres IP karty sieciowej na maszynie wirtualnej. Pobrać dołączony do maszyny wirtualnej, w poleceniu występującym karty interfejsu sieciowego. Jeśli znasz już adres IP, który ma zostać przetestowana na maszynie wirtualnej, można pominąć ten krok.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Sprawdź uruchomienia przepływu IP

Uruchom `Test-AzureRmNetworkWatcherIPFlow` polecenia cmdlet, aby przetestować ruchu. W tym przykładzie pierwszego adresu IP na karcie Sieciowej pierwszy jest używany.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> Sprawdź przepływ IP wymaga, aby zasobu maszyny Wirtualnej jest przydzielona do uruchomienia.

## <a name="review-results"></a>Przejrzyj wyniki

Po uruchomieniu `Test-AzureRmNetworkWatcherIPFlow` wyniki są zwracane, w poniższym przykładzie jest wyników zwróconych z poprzedniego kroku.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli ruch jest blokowane i nie należy, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń zdefiniowane.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













