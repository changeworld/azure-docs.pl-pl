---
title: "Znajdź następny przeskok z Azure sieci obserwatora następnego przeskoku - Azure CLI 2.0 | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak znaleźć, co to jest typ następnego przeskoku i adres ip za pomocą następnego przeskoku przy użyciu wiersza polecenia platformy Azure."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 873bd0f8c98cfa67c77841df8aa53eb2b895cd54
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Sprawdź, jaki typ następnego przeskoku jest przy użyciu funkcji w następnym przeskoku w obserwatora sieciowego Azure używa interfejsu wiersza polecenia platformy Azure w wersji 2.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [Program PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-next-hop-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-next-hop-rest.md)

Następny przeskok jest funkcją obserwatora sieciowego, który zapewnia możliwość get Typ następnego przeskoku i adres IP na podstawie określonej maszyny wirtualnej. Ta funkcja jest przydatna przy określaniu, jeśli ruchu wychodzącego maszyny wirtualnej są przesyłane za pośrednictwem bramy, Internetu lub sieci wirtualne na uzyskanie dostępu do miejsca docelowego.

W tym artykule wykorzystano naszej nowej generacji interfejsu wiersza polecenia model wdrażania zarządzania zasobów, Azure CLI 2.0, który jest dostępny dla systemu Windows, Mac i Linux.

Aby wykonać kroki opisane w tym artykule, należy [instalowanie interfejsu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu użyjesz wiersza polecenia platformy Azure można znaleźć Typ następnego przeskoku i adres IP.

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule używa następnego przeskoku, funkcja obserwatora sieciowego, który znajduje się typ następnego przeskoku i adres IP dla każdego zasobu. Aby dowiedzieć się więcej na temat następnego przeskoku, odwiedź stronę [następnego przeskoku omówienie](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Pobierz następnego skoku

Uzyskanie następnego przeskoku nazywamy `az network watcher show-next-hop` polecenia cmdlet. Polecenie cmdlet jest przekazywana obserwatora sieciowego grupy zasobów, NetworkWatcher maszynę wirtualną identyfikator, źródłowego adresu IP i docelowego adresu IP. W tym przykładzie docelowy adres IP jest z maszyną wirtualną w innej sieci wirtualnej. Brak bramy sieci wirtualnej między dwiema sieciami wirtualnymi.

Jeśli nie zostało jeszcze, instalowania i konfigurowania najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login). Następnie uruchom następujące polecenie:

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
Jeśli maszyna wirtualna ma wiele kart sieciowych i przesyłanie dalej IP jest włączona na żadnym z kart sieciowych, a następnie parametr karty Sieciowej (-i identyfikator karty sieciowej) musi być określona. W przeciwnym razie jest opcjonalne.

## <a name="review-results"></a>Przegląd wyników

Po zakończeniu znajdują się wyniki. A także typ zasobu, który jest zwracany jest adres IP następnego przeskoku.

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
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
