---
title: Rozwiązywanie problemów z bramy sieci wirtualnej platformy Azure i połączenia - PowerShell | Dokumentacja firmy Microsoft
description: Ta strona wyjaśniono, jak użyć obserwatora sieciowego Azure Rozwiązywanie problemów z polecenia cmdlet programu PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 0a967759e477d213dd0a33298a4079fc48caa81f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777683"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Rozwiązywanie problemów z Brama sieci wirtualnej i połączeń przy użyciu programu PowerShell obserwatora sieci platformy Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Obserwatora sieciowego zawiera wiele funkcji w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jeden z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobów można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, obserwatora sieciowego sprawdza kondycję Brama sieci wirtualnej lub połączenie i zwraca jego wyniki.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

Lista odwiedziny typy obsługiwanych bramy [bramy obsługiwane typy](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Przegląd

Rozwiązywanie problemów z zasobów umożliwia rozwiązywanie problemów, które wynikają z bramy sieci wirtualnej i połączenia. Po wysłaniu żądania do zasobów dotyczących rozwiązywania problemów, dzienniki są proszeni i inspekcji. Po zakończeniu kontroli wyniki są zwracane. Żądania dotyczące rozwiązywania problemów są długotrwałe żądania zasobów, może to potrwać kilka minut do zwrócenia wyniku. Dzienniki na rozwiązywanie problemów z są przechowywane w kontenerze na koncie magazynu, który jest określony.

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Pierwszym krokiem jest pobrać wystąpienia obserwatora sieciowego. `$networkWatcher` Zmienna jest przekazywana do `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenia cmdlet w kroku 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy sieci wirtualnej

W tym przykładzie Rozwiązywanie problemów z zasobów jest są uruchomione na połączenie. Można również przekazać go Brama sieci wirtualnej.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobów zwraca dane dotyczące kondycji zasobu, zapisuje dzienniki na konto magazynu, należy sprawdzić. W tym kroku utworzymy konta magazynu, jeśli istnieje już konto magazynu można go.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchom Rozwiązywanie problemów z zasobów obserwatora sieciowego

Rozwiązywanie problemów z zasobami za pomocą `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenia cmdlet. Polecenie cmdlet jest przekazywana obiektów obserwatora sieciowego, identyfikator połączenia lub brama sieci wirtualnej, identyfikator konta magazynu i ścieżki do przechowywania wyników.

> [!NOTE]
> `Start-AzureRmNetworkWatcherResourceTroubleshooting` Polecenia cmdlet jest długo działające i może potrwać kilka minut.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Po uruchomieniu polecenia cmdlet obserwatora sieciowego przegląda zasobów, aby sprawdzić kondycję. Zwraca wyniki do powłoki, a zapisuje dzienniki wyników w określone konto magazynu.

## <a name="understanding-the-results"></a>Opis wyników

Tekst akcji zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można podjąć akcję, link jest dostarczany z dodatkowych wskazówek. W przypadku których nie ma dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć sprawę pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i dostępnych odwiedź [Rozwiązywanie problemów z obserwatora sieciowego — omówienie](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kontami magazynu azure, zapoznaj się [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kolejnym narzędziem, który może służyć jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj przy użyciu następującego łącza: [Eksploratora usługi Storage](http://storageexplorer.com/)

## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienia zostały zmienione które zatrzymania połączenia sieci VPN, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń, które mogą być zagrożona.
