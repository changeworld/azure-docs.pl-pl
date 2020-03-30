---
title: Wdrażanie zapory platformy Azure z wieloma publicznymi adresami IP przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zaporę platformy Azure z wieloma publicznymi adresami IP przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ad54b60d8f15e36636f887015d97967740123669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195865"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Wdrażanie usługi Azure Firewall z wieloma publicznymi adresami IP przy użyciu programu Azure PowerShell

Ta funkcja umożliwia następujące scenariusze:

- **DNAT** — można przetłumaczyć wiele standardowych wystąpień portów na serwery wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, możesz przetłumaczyć port TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dostępne są dodatkowe porty dla wychodzących połączeń SNAT, co zmniejsza ryzyko wyczerpania portów SNAT. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP do użycia dla połączenia. Jeśli masz jakiekolwiek filtrowanie podrzędne w sieci, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.
 
Zapora azure z wieloma publicznymi adresami IP jest dostępna za pośrednictwem witryny Azure portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure, REST i szablonów. Zapora azure z maksymalnie 100 publicznych adresów IP.

Poniższe przykłady programu Azure PowerShell pokazują, jak można skonfigurować, dodać i usunąć publiczne adresy IP dla Zapory platformy Azure.

> [!NOTE]
> Nie można usunąć pierwszej konfiguracji ipConfiguration ze strony konfiguracji publicznego adresu IP zapory platformy Azure. Jeśli chcesz zmodyfikować adres IP, możesz użyć programu Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Tworzenie zapory z co najmniej dwoma publicznymi adresami IP

W tym przykładzie tworzy zaporę dołączoną do sieci wirtualnej sieci *wirtualnej* z dwoma publicznymi adresami IP.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Dodawanie publicznego adresu IP do istniejącej zapory

W tym przykładzie publiczny adres IP *azFwPublicIp1* jest dołączony do zapory.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Usuwanie publicznego adresu IP z istniejącej zapory

W tym przykładzie publiczny adres IP *azFwPublicIp1* jest odłączony od zapory.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Następne kroki

* [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
