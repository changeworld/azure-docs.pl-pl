---
title: Rozwiązywanie problemów z bramą i połączeniami sieci wirtualnej platformy Azure — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak używać polecenia cmdlet programu Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0723ddc9b0e2f15d5c8e51c96d51f58f1313493a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673667"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami przy użyciu programu Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-troubleshoot-manage-rest.md)

Kontrola sieci zapewnia wiele możliwości, ponieważ odnosi się do zrozumienia zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. Rozwiązywanie problemów z zasobami można wywołać za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, Network Watcher sprawdza kondycję bramy sieci wirtualnej lub połączenia i zwraca jego wyniki.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

Aby uzyskać listę obsługiwanych typów bram, odwiedź stronę [Obsługiwane typy bramy](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z zasobami zapewnia możliwość rozwiązywania problemów, które pojawiają się z bram sieci wirtualnej i połączeń. Po wysłaniu żądania do rozwiązywania problemów z zasobami dzienniki są wyszukiwane i kontrolowane. Po zakończeniu kontroli wyniki są zwracane. Żądania rozwiązywania problemów z zasobami są długotrwałymi żądaniami, co może potrwać wiele minut, aby zwrócić wynik. Dzienniki z rozwiązywania problemów są przechowywane w kontenerze na koncie magazynu, który jest określony.

## <a name="retrieve-network-watcher"></a>Pobieranie obserwatora sieci

Pierwszym krokiem jest pobranie wystąpienia Obserwatora sieci. Zmienna `$networkWatcher` jest przekazywana do polecenia `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet w kroku 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy sieci wirtualnej

W tym przykładzie rozwiązywanie problemów z zasobami jest uruchomiony na połączenie. Można również przekazać go bramy sieci wirtualnej.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobami zwraca dane dotyczące kondycji zasobu, zapisuje również dzienniki na koncie magazynu do przejrzenia. W tym kroku tworzymy konto magazynu, jeśli istnieje istniejące konto magazynu, możesz go użyć.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchamianie rozwiązywania problemów z zasobami Obserwatora sieci

Rozwiązywanie problemów `Start-AzNetworkWatcherResourceTroubleshooting` z zasobami za pomocą polecenia cmdlet. Przekazujemy polecenie cmdlet obiektu Kontrola sieci, identyfikator bramy połączenia lub sieci wirtualnej, identyfikator konta magazynu i ścieżkę do przechowywania wyników.

> [!NOTE]
> Polecenie `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet jest długotrwałe i może potrwać kilka minut.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Po uruchomieniu polecenia cmdlet, Network Watcher przegląda zasób, aby zweryfikować kondycję. Zwraca wyniki do powłoki i przechowuje dzienniki wyników na określonym koncie magazynu.

## <a name="understanding-the-results"></a>Zrozumienie wyników

Tekst akcji zawiera ogólne wskazówki dotyczące sposobu rozwiązania problemu. Jeśli można podjąć działanie w przypadku problemu, łącze jest dostarczane z dodatkowymi wskazówkami. W przypadku, gdy nie ma żadnych dodatkowych wskazówek, odpowiedź zawiera adres URL, aby otworzyć przypadek pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i co jest zawarte, odwiedź [omówienie rozwiązywania problemów z obserwatorem sieci](network-watcher-troubleshoot-overview.md)

Instrukcje dotyczące pobierania plików z kont usługi Azure Storage można znaleźć w [obszarze Wprowadzenie do usługi Azure Blob Storage przy użyciu programu .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, które może być używane, jest Eksplorator magazynu. Więcej informacji na temat Eksploratora magazynu można znaleźć tutaj pod następującym linkiem: [Eksplorator magazynu](https://storageexplorer.com/)

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienia zostały zmienione, które zatrzymują łączność sieci VPN, zobacz [Zarządzanie grupami zabezpieczeń sieci,](../virtual-network/manage-network-security-group.md) aby wyśledzić grupę zabezpieczeń sieci i reguły zabezpieczeń, które mogą być kwestionowane.
