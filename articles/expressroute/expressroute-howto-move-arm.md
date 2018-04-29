---
title: 'Przenieś obwody usługi ExpressRoute ze środowiska klasycznego do Menedżera zasobów: środowiska PowerShell: Azure | Dokumentacja firmy Microsoft'
description: Ta strona zawiera opis sposobu przenieść obwodu klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 37db218010288912bf75d00fd5868aab171aef71
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Przenieść obwody usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell

Aby używać obwodu usługi ExpressRoute dla klasycznego i modeli wdrażania usługi Resource Manager, należy przenieść obwodu do modelu wdrażania usługi Resource Manager. Poniższe sekcje pomóc Przenieś obwodu przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Sprawdź, czy masz najnowszą wersję modułów programu Azure PowerShell (co najmniej w wersji 1.0). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Upewnij się, że użytkownik przejrzał [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.
* Przejrzyj informacje, które są przekazywane pod [przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md). Upewnij się, że pełni rozumiesz limity i ograniczenia.
* Sprawdź, że obwód jest w pełni funkcjonalna w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, który został utworzony w modelu wdrażania usługi Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Przenieś obwodu usługi ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Zbieranie szczegółów obwód w klasycznym modelu wdrażania

Zaloguj się do klasycznego środowiska platformy Azure i zebrać klucza usługi.

1. Zaloguj się do konta platformy Azure.

  ```powershell
  Add-AzureAccount
  ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. Zaimportuj moduły programu PowerShell platformy Azure i usługi ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
  ```

4. Poniższe polecenie cmdlet umożliwia uzyskanie kluczy usługi dla wszystkich sieci obwody usługi ExpressRoute. Po pobraniu klucze, skopiuj **klucza usługi** obwodu, który chcesz przenieść do modelu wdrażania usługi Resource Manager.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Zaloguj się i utworzyć grupę zasobów

Zaloguj się do środowiska usługi Resource Manager i utworzyć nową grupę zasobów.

1. Zaloguj się do środowiska usługi Azure Resource Manager.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Zmodyfikuj fragment poniżej, aby utworzyć nową grupę zasobów, jeśli nie masz już grupę zasobów.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Przenieś obwodu ExpressRoute do modelu wdrażania usługi Resource Manager

Teraz można przystąpić do przenieść obwodu usługi ExpressRoute z klasycznym modelu wdrażania modelu wdrażania usługi Resource Manager. Przed kontynuowaniem zapoznaj się z informacjami podanymi w [przenoszenie obwodu usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](expressroute-move.md).

Aby przenieść obwodu, zmodyfikuj i uruchom następujący fragment kodu:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

> [!NOTE]
> Po zakończeniu przenoszenia nową nazwę, która znajduje się w poprzednim poleceniu cmdlet będzie wykorzystywana do adresowania zasobów. Zasadniczo można zmienić nazwy obwodu.
> 

## <a name="modify-circuit-access"></a>Modyfikowania dostępu do obwodu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Aby włączyć dostęp obwodu ExpressRoute oba modele wdrażania

Po przeniesieniu obwodu ExpressRoute klasycznego modelu wdrażania usługi Resource Manager, możesz włączyć dostęp do obu modeli wdrażania. Uruchom następujące polecenia cmdlet, aby umożliwić dostęp do obu modeli wdrażania:

1. Uzyskiwanie szczegółowych informacji obwodu.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ustaw "Zezwalaj na operacje klasycznego" na wartość TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Zaktualizuj obwodu. Po tej operacji zakończyło się pomyślnie, można wyświetlić w klasycznym modelu wdrażania obwodu.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Uruchom następujące polecenie cmdlet, aby uzyskać szczegóły obwodu usługi expressroute. Musi być widoczna na liście klucza usługi.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Możesz teraz zarządzać łącza do obwodu ExpressRoute, za pomocą poleceń modelu klasycznym wdrożenia klasyczne sieci wirtualne i polecenia Menedżera zasobów dla sieci wirtualnych Menedżera zasobów. Następujące artykuły ułatwiają zarządzanie łącza do obwodu ExpressRoute:

    * [Link sieci wirtualnej do obwodu usługi ExpressRoute w modelu wdrażania usługi Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Link do obwodu usługi ExpressRoute w klasycznym modelu wdrożenia sieci wirtualnej](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Aby wyłączyć dostęp obwodu ExpressRoute do klasycznym modelu wdrażania

Uruchom następujące polecenia cmdlet, aby wyłączyć dostęp do klasycznego modelu wdrażania.

1. Uzyskiwanie szczegółowych informacji z obwodem usługi ExpressRoute.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ustaw "Zezwalaj na operacje klasycznego" na wartość FALSE.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. Zaktualizuj obwodu. Po tej operacji zakończyło się pomyślnie, nie będzie mogła wyświetlać obwodu w klasycznym modelu wdrażania.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Link sieci wirtualnej do obwodu usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
