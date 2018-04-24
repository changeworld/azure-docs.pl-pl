---
title: "Znajdź następny przeskok z Azure sieci obserwatora następnego przeskoku - portalu Azure | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak znaleźć, co to jest typ następnego przeskoku i adres ip za pomocą następnego przeskoku przy użyciu portalu Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Sprawdź, jaki typ następnego przeskoku jest przy użyciu funkcji w następnym przeskoku w obserwatora sieciowego Azure przy użyciu portalu

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-next-hop-portal.md)
> - [Program PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-next-hop-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-next-hop-rest.md)

Następny przeskok jest funkcją obserwatora sieciowego, który zapewnia możliwość get Typ następnego przeskoku i adres IP na podstawie określonej maszyny wirtualnej. Ta funkcja jest przydatna przy określaniu, jeśli ruchu wychodzącego maszyny wirtualnej są przesyłane za pośrednictwem bramy, Internetu lub sieci wirtualne na uzyskanie dostępu do miejsca docelowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule używa następnego przeskoku Typ następnego przeskoku i adres IP dla każdego zasobu. Aby dowiedzieć się więcej na temat następnego przeskoku, odwiedź stronę [następnego przeskoku omówienie](network-watcher-next-hop-overview.md).

W tym scenariuszu obejmują:

* Pobrać następnego przeskoku z maszyny wirtualnej.

## <a name="get-next-hop"></a>Pobierz następnego skoku

### <a name="step-1"></a>Krok 1

Przejdź do zasobu obserwatora sieciowego w portalu Azure.

### <a name="step-2"></a>Krok 2

Kliknij przycisk **następnego przeskoku** w okienku nawigacji wybierz maszynę wirtualną i interfejsu sieciowego, wypełnij źródłowego i docelowego adresu IP i kliknij przycisk **następnego przeskoku** przycisku.

> [!NOTE]
> Następnego przeskoku wymaga, aby zasobu maszyny Wirtualnej jest przydzielona do uruchomienia.

![Pobierz następnego przeskoku — omówienie][1]

### <a name="step-3"></a>Krok 3

Po zakończeniu zadania znajdują się wyniki. Adres IP i typ urządzenia, które jest następnego przeskoku, zostanie wyświetlony. W poniższej tabeli przedstawiono dostępne wartości zwracane w portalu.

**Typ następnego przeskoku**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

Jeśli niestandardowa trasa został użyty do kierowania ten ruch, trasy zdefiniowane przez użytkownika (przez) wyświetlany jest również z wynikami.

![Następny przeskok wyniki][2]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak programowo Przejrzyj ustawienia sieciowe grupy zabezpieczeń, odwiedzając [NSG inspekcji z obserwatora sieciowego](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














