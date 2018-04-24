---
title: "Znajdź następny przeskok z Azure sieci obserwatora następnego przeskoku - Azure CLI 1.0 | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak znaleźć, co to jest typ następnego przeskoku i adres ip za pomocą następnego przeskoku przy użyciu wiersza polecenia platformy Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Sprawdź, jaki typ następnego przeskoku jest przy użyciu funkcji w następnym przeskoku w obserwatora sieciowego Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-next-hop-portal.md)
> - [Program PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-next-hop-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-next-hop-rest.md)

Następny przeskok jest funkcją obserwatora sieciowego, który zapewnia możliwość get Typ następnego przeskoku i adres IP na podstawie określonej maszyny wirtualnej. Ta funkcja jest przydatna przy określaniu, jeśli ruchu wychodzącego maszyny wirtualnej są przesyłane za pośrednictwem bramy, Internetu lub sieci wirtualne na uzyskanie dostępu do miejsca docelowego.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu użyjesz wiersza polecenia platformy Azure można znaleźć Typ następnego przeskoku i adres IP.

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule używa następnego przeskoku, funkcja obserwatora sieciowego, który znajduje się typ następnego przeskoku i adres IP dla każdego zasobu. Aby dowiedzieć się więcej na temat następnego przeskoku, odwiedź stronę [następnego przeskoku omówienie](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Pobierz następnego skoku

Uzyskanie następnego przeskoku nazywamy `azure netowrk watcher next-hop` polecenia cmdlet. Polecenie cmdlet jest przekazywana obserwatora sieciowego grupy zasobów, NetworkWatcher maszynę wirtualną identyfikator, źródłowego adresu IP i docelowego adresu IP. W tym przykładzie docelowy adres IP jest z maszyną wirtualną w innej sieci wirtualnej. Brak bramy sieci wirtualnej między dwiema sieciami wirtualnymi. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Jeśli maszyna wirtualna ma wiele kart sieciowych i przesyłanie dalej IP jest włączona na żadnym z kart sieciowych, a następnie parametr karty Sieciowej (-i identyfikator karty sieciowej) musi być określona. W przeciwnym razie jest opcjonalne.

## <a name="review-results"></a>Przegląd wyników

Po zakończeniu znajdują się wyniki. A także typ zasobu, który jest zwracany jest adres IP następnego przeskoku.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
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
