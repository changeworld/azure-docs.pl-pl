---
title: Wdrażanie zapory platformy Azure przy użyciu wiele publicznych adresów IP przy użyciu programu Azure PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zapory usługi Azure za pomocą wiele publicznych adresów IP przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703983"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Wdrażanie zapory platformy Azure przy użyciu wiele publicznych adresów IP przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Zaporę platformy Azure przy użyciu wiele publicznych adresów IP jest dostępna za pośrednictwem programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, REST i szablony. Interfejs użytkownika portalu jest dodawany do regionów stopniowo i będą dostępne we wszystkich regionach, po zakończeniu wdrożenia.

Możesz wdrożyć zapory platformy Azure, z maksymalnie 100 publicznych adresów IP.

Ta funkcja umożliwia następujące scenariusze:

- **DNAT** — wielu wystąpień standardowego portu można tłumaczyć na serwerach wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, może dokonywać translacji portu TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dodatkowe porty są dostępne dla połączeń SNAT wychodzących, zmniejszyć prawdopodobieństwo SNAT wyczerpanie portów. W tej chwili zapory usługi Azure losowo wybiera publiczny adres IP źródłowego na potrzeby połączenia. Jeśli masz wszystkie podrzędne filtrowania w sieci, należy zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

W poniższych przykładach programu Azure PowerShell pokazano, jak można skonfigurować, dodawać i usuwać publiczne adresy IP dla zapory usługi Azure.

> [!NOTE]
> Pierwszy element ipConfiguration nie można usunąć z zapory usługi Azure publiczny adres strony konfiguracji IP. Jeśli chcesz zmodyfikować adres IP, można użyć programu Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Tworzenie zapory przy użyciu co najmniej dwóch publicznych adresów IP

W tym przykładzie tworzy zaporę dołączony do sieci wirtualnej *sieci wirtualnej* za pomocą dwóch publicznych adresów IP.

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

W tym przykładzie, publiczny adres IP *azFwPublicIp1* jest dołączony do zapory.

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

W tym przykładzie, publiczny adres IP *azFwPublicIp1* jest odłączona od zapory.

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

* [Samouczek: Monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
