---
title: 'Przenoszenie obwodów z klasycznej do usługi Resource Manager — usługi ExpressRoute: Program PowerShell: Azure | Microsoft Docs'
description: Ta strona zawiera opis sposobu przenoszenie obwodu klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 984ccfa9bad99281418ba891ce188536ae13d8e5
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106770"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell

Aby użyć obwodu usługi ExpressRoute dla wdrożeń klasycznych, jak i modelem wdrażania usługi Resource Manager, należy przenieść obwód modelu wdrażania usługi Resource Manager. Poniższe sekcje ułatwiają przenoszenie obwodu za pomocą programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Sprawdź, czy masz najnowszą wersję modułów programu Azure PowerShell (co najmniej w wersji 1.0). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Upewnij się, że użytkownik przejrzał [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Zapoznaj się z informacjami, który znajduje się w obszarze [przenoszenie obwodu usługi ExpressRoute z modelu klasycznego do usługi Resource Manager](expressroute-move.md). Upewnij się, że w pełni rozumiesz limity i ograniczenia.
* Sprawdź, że obwód jest w pełni funkcjonalne w klasycznym modelu wdrażania.
* Upewnij się, że grupa zasobów, który został utworzony w modelu wdrażania usługi Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Przenoszenie obwodu usługi ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Zbieranie szczegółów obwodu z klasycznego modelu wdrażania

Zaloguj się do klasycznego środowiska platformy Azure i Zbierz klucza usługi.

1. Zaloguj się do swojego konta platformy Azure.

  ```powershell
  Add-AzureAccount
  ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. Importowanie modułów programu PowerShell dla platformy Azure i usługi ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
  Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
  ```

4. Użyj poniższego polecenia cmdlet, aby pobrać klucze usługi dla wszystkich obwodów usługi ExpressRoute. Po pobraniu klucze, należy skopiować **klucza usługi** obwodu, który chcesz przenieść do modelu wdrażania usługi Resource Manager.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Zaloguj się i Utwórz grupę zasobów

Zaloguj się do środowiska usługi Resource Manager i Utwórz nową grupę zasobów.

1. Zaloguj się do środowiska usługi Azure Resource Manager.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Zmodyfikuj fragmencie kodu poniżej, aby utworzyć nową grupę zasobów, jeśli nie masz jeszcze grupy zasobów.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Przenoszenie obwodu usługi ExpressRoute do modelu wdrażania usługi Resource Manager

Teraz można przystąpić do przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager. Przed kontynuowaniem zapoznaj się z informacjami w [przenoszenie obwodu usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](expressroute-move.md).

Aby przenieść obwodu, Modyfikuj, a następnie uruchom poniższy fragment kodu:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

W trybie klasycznym obwodu usługi ExpressRoute nie ma koncepcji jest powiązany z obszarem. Jednak w usłudze Resource Manager, każdy zasób musi być zamapowany na region platformy Azure. Określone w poleceniu cmdlet Move-AzureRmExpressRouteCircuit województwo z technicznego punktu widzenia może być dowolnym regionie. Do celów organizacji można wybrać region, który reprezentuje blisko swojej lokalizacji komunikacji równorzędnej.

> [!NOTE]
> Po zakończeniu przenoszenia nową nazwę, która znajduje się w poprzednim poleceniu cmdlet będzie wykorzystywana do adresowania zasobów. Zasadniczo można zmienić nazwy obwodu.
> 

## <a name="modify-circuit-access"></a>Modyfikowanie obwodu dostępu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Aby włączyć dostęp obwodu usługi ExpressRoute dla obu modeli wdrażania

Po przeniesieniu obwód usługi ExpressRoute klasycznego modelu wdrażania usługi Resource Manager, aby umożliwić dostęp do obu modeli wdrażania. Uruchom następujące polecenia cmdlet, aby umożliwić dostęp do obu modelach wdrażania:

1. Uzyskaj szczegółowe informacje obwodu.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ustaw "Zezwalaj na klasyczne operacje" na wartość TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Zaktualizuj obwodu. Po tej operacji zakończyło się pomyślnie, będzie można wyświetlić obwód w klasycznym modelu wdrażania.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Uruchom następujące polecenie cmdlet, aby uzyskać szczegółowe informacje z obwodem usługi ExpressRoute. Musi być można zobaczyć klucz usługi na liście.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Możesz teraz zarządzać łącza z obwodem usługi ExpressRoute za pomocą poleceń modelu wdrożenia klasycznego dla klasycznych sieci wirtualnych i poleceń usługi Resource Manager dla sieci wirtualnych usługi Resource Manager. Następujące artykuły ułatwiają zarządzanie łącza z obwodem usługi ExpressRoute:

    * [Łączenie sieci wirtualnej na obwód usługi ExpressRoute w modelu wdrażania usługi Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Łączenie sieci wirtualnej na obwód usługi ExpressRoute w klasycznym modelu wdrażania](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Aby wyłączyć dostęp obwodu usługi ExpressRoute do klasycznego modelu wdrażania

Uruchom następujące polecenia cmdlet, aby wyłączyć dostęp do klasycznego modelu wdrażania.

1. Uzyskaj szczegółowe informacje obwodu usługi ExpressRoute.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ustaw "Zezwalaj na klasyczne operacje" na wartość FALSE.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. Zaktualizuj obwodu. Po tej operacji zakończyło się pomyślnie, nie można wyświetlić obwód w klasycznym modelu wdrażania.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Łączenie sieci wirtualnej na obwód usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
