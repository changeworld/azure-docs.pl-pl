---
title: 'Wirtualna sieć WAN: tworzenie tabeli tras centrum wirtualnego do sieci WUS: Azure PowerShell'
description: Wirtualna tabela tras koncentratora wirtualnego sieci WAN w celu kierowania ruchu do wirtualnego urządzenia sieciowego.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645110"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Tworzenie tabeli tras centrum wirtualnego w celu kierowania ruchu do wirtualnego urządzenia sieciowego

W tym artykule pokazano, jak sterować ruchem z centrum wirtualnego do wirtualnego urządzenia sieciowego. 

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table/vwanroute.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci WAN
* Tworzenie koncentratora
* Tworzenie połączeń sieci wirtualnej koncentratora
* Tworzenie trasy koncentratora
* Tworzenie tabeli tras
* Stosowanie tabeli tras

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sprawdź, czy spełniasz następujące kryteria:

1. Masz wirtualne urządzenie sieciowe (NVA). Jest to wybrane oprogramowanie innej firmy, które jest zazwyczaj aprowiowane z portalu Azure Marketplace w sieci wirtualnej.
2. Do interfejsu sieciowego urządzenia WUS przypisano prywatny adres IP. 
3. Nie można wdrożyć urządzenia wus nie w centrum wirtualnym. Musi być wdrożony w oddzielnej sieci wirtualnej. W tym artykule sieci wirtualnej NVA jest określany jako "DMZ VNet".
4. "Dmz VNet" może mieć jedną lub wiele sieci wirtualnych podłączonych do niego. W tym artykule ta sieci wirtualnej jest określany jako "Pośrednie szprychy sieci wirtualnej". Te sieci wirtualne mogą być podłączone do sieci wirtualnej DMZ przy użyciu komunikacji równorzędnej sieci wirtualnej.
5. Sprawdź, czy masz już utworzone 2 sieci wirtualne. Będą one używane jako sieci wirtualne szprychy. W tym artykule przestrzenie adresowe szprychy sieci wirtualnej to 10.0.2.0/24 i 10.0.3.0/24. Jeśli potrzebujesz informacji na temat tworzenia sieci wirtualnej, zobacz [Tworzenie sieci wirtualnej przy użyciu programu PowerShell](../virtual-network/quick-create-powershell.md).
6. Upewnij się, że nie ma żadnych bram sieci wirtualnej w sieciach wirtualnych.

## <a name="1-sign-in"></a><a name="signin"></a>1. Zaloguj się

Upewnij się, że zainstalowano najnowszą wersję poleceń cmdlet programu PowerShell menedżera zasobów. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Jest to ważne, ponieważ wcześniejsze wersje poleceń cmdlet nie zawierają bieżących wartości potrzebnych w tym ćwiczeniu.

1. Otwórz konsolę programu PowerShell z podwyższonymi uprawnieniami i zaloguj się na swoje konto platformy Azure. To polecenie cmdlet monituje o poświadczenia logowania. Po zalogowaniu pobiera ustawienia konta, dzięki czemu są one dostępne dla programu Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Pobierz listę subskrypcji platformy Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Tworzenie zasobów

1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Utwórz wirtualną sieć WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Utwórz koncentrator wirtualny.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Tworzenie połączeń

Tworzenie połączeń sieci wirtualnej koncentratora z sieci wirtualnej pośredniej szprychy i sieci wirtualnej DMZ do koncentratora wirtualnego.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Tworzenie wirtualnej trasy koncentratora

W tym artykule przestrzenie adresowe sieci wirtualnej z pośrednią szprychą są 10.0.2.0/24 i 10.0.3.0/24, a prywatny adres IP interfejsu sieciowego DMZ NVA to 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Tworzenie tabeli tras wirtualnego koncentratora

Utwórz tabelę tras koncentratora wirtualnego, a następnie zastosuj do niej utworzoną trasę.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. Zatwierdzanie zmian

Zaobbuń zmiany w centrum wirtualnym.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
