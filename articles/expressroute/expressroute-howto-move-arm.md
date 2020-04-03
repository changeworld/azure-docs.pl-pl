---
title: 'Azure ExpressRoute: Przenoszenie klasycznych obwodów do Menedżera zasobów'
description: Na tej stronie opisano sposób przenoszenia obwodu klasycznego do modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.openlocfilehash: d3014aae44b8d63be67cd0d31f996470aeda40df
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616277"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Przenoszenie obwodów usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell

Aby użyć obwodu usługi ExpressRoute zarówno dla modeli wdrażania klasycznego, jak i modelu wdrażania Menedżera zasobów, należy przenieść obwód do modelu wdrażania Menedżera zasobów. Poniższe sekcje ułatwią przenoszenie obwodu za pomocą programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Sprawdź, czy zainstalowano moduły klasyczne i Az Azure PowerShell lokalnie na komputerze. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Przed rozpoczęciem konfiguracji upewnij się, że zostały [sprawdzone wymagania wstępne,](expressroute-prerequisites.md) [wymagania dotyczące routingu](expressroute-routing.md)i [przepływy pracy.](expressroute-workflows.md)
* Przejrzyj informacje podane w obszarze [Przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md). Upewnij się, że w pełni rozumiesz ograniczenia i ograniczenia.
* Sprawdź, czy obwód jest w pełni operacyjny w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, która została utworzona w modelu wdrażania Menedżera zasobów.

## <a name="move-an-expressroute-circuit"></a>Przenoszenie obwodu usługi ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Zbieranie szczegółów obwodu z klasycznego modelu wdrażania

Zaloguj się do klasycznego środowiska platformy Azure i zbierz klucz usługi.

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

4. Użyj polecenia cmdlet poniżej, aby uzyskać klucze serwisowe dla wszystkich obwodów usługi ExpressRoute. Po pobraniu kluczy skopiuj **klucz usługi** obwodu, który chcesz przenieść do modelu wdrażania Menedżera zasobów.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Zaloguj się i utwórz grupę zasobów

Zaloguj się do środowiska Menedżera zasobów i utwórz nową grupę zasobów.

1. Zaloguj się do środowiska usługi Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Zmodyfikuj poniższy fragment kodu, aby utworzyć nową grupę zasobów, jeśli nie masz jeszcze grupy zasobów.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Przeniesienie obwodu usługi ExpressRoute do modelu wdrażania Menedżera zasobów

Teraz można przystąpić do przenoszenia obwodu usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania Menedżera zasobów. Przed kontynuowaniem zapoznaj się z informacjami podanymi w [obszarze Przenoszenie obwodu usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów](expressroute-move.md).

Aby przenieść obwód, zmodyfikuj i uruchom następujący fragment kodu:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

W trybie klasycznym obwód usługi ExpressRoute nie ma pojęcia, że jest powiązany z regionem. Jednak w Menedżerze zasobów każdy zasób musi być mapowany do regionu platformy Azure. Region określony w poleceniu cmdlet Move-AzExpressRouteCircuit może technicznie być dowolnym regionem. Do celów organizacyjnych można wybrać region, który ściśle reprezentuje twoją lokalizację komunikacji równorzędnej.

> [!NOTE]
> Po zakończeniu przenoszenia nowa nazwa wymieniona w poprzednim pomieści zostanie użyta do rozwiązania problemu zasobu. Zasadniczo zmieniono nazwę obwodu.
> 

## <a name="modify-circuit-access"></a>Modyfikowanie dostępu do obwodu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Aby włączyć dostęp do obwodu usługi ExpressRoute dla obu modeli wdrażania

Po przeniesieniu klasycznego obwodu usługi ExpressRoute do modelu wdrażania Menedżera zasobów można włączyć dostęp do obu modeli wdrażania. Uruchom następujące polecenia cmdlet, aby umożliwić dostęp do obu modeli wdrażania:

1. Uzyskaj szczegółowe informacje o obwodzie.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ustaw wartość "Zezwalaj na operacje klasyczne" na TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Zaktualizuj obwód. Po pomyślnym zakończeniu tej operacji będzie można wyświetlić obwód w klasycznym modelu wdrażania.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Uruchom następujące polecenie cmdlet, aby uzyskać szczegółowe informacje o obwodzie usługi ExpressRoute. Musi być widoczny na liście klucz usługi.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Teraz można zarządzać łączami do obwodu usługi ExpressRoute przy użyciu klasycznych poleceń modelu wdrażania dla klasycznych sieci wirtualnych i poleceń Menedżera zasobów dla sieci wirtualnych Menedżera zasobów. Poniższe artykuły ułatwiają zarządzanie łączami do obwodu usługi ExpressRoute:

    * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute w modelu wdrażania Menedżera zasobów](expressroute-howto-linkvnet-arm.md)
    * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute w klasycznym modelu wdrażania](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Aby wyłączyć dostęp obwodu usługi ExpressRoute do klasycznego modelu wdrażania

Uruchom następujące polecenia cmdlet, aby wyłączyć dostęp do klasycznego modelu wdrażania.

1. Szczegółowe informacje na temat obwodu usługi ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ustaw wartość "Zezwalaj na operacje klasyczne" na WARTOŚĆ FAŁSZ.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Zaktualizuj obwód. Po pomyślnym zakończeniu tej operacji nie będzie można wyświetlić obwodu w klasycznym modelu wdrażania.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
