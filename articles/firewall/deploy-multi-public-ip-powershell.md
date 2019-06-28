---
title: Wdrażanie zapory platformy Azure przy użyciu wiele publicznych adresów IP przy użyciu programu Azure PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zapory usługi Azure za pomocą wiele publicznych adresów IP przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/21/2019
ms.author: victorh
ms.openlocfilehash: 9ec37197376c815c4fb9072164520a707b02be2b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312721"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Wdrażanie zapory platformy Azure przy użyciu wiele publicznych adresów IP przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Zaporę platformy Azure przy użyciu wiele publicznych adresów IP jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz wdrożyć zapory platformy Azure za pomocą do 600 publicznych adresów IP.

Ta funkcja umożliwia następujące scenariusze:

- **DNAT** — wielu wystąpień standardowego portu można tłumaczyć na serwerach wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, może dokonywać translacji portu TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dodatkowe porty są dostępne dla połączeń SNAT wychodzących, zmniejszyć prawdopodobieństwo SNAT wyczerpanie portów. W tej chwili zapory usługi Azure losowo wybiera publiczny adres IP źródłowego na potrzeby połączenia. Jeśli masz wszystkie podrzędne filtrowania w sieci, należy zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

W poniższych przykładach programu Azure PowerShell pokazano, jak można dodawać, usuwać i konfigurować publiczne adresy IP dla zapory usługi Azure.

> [!NOTE]
> W publicznej wersji zapoznawczej możesz dodać lub usunąć publiczny adres IP do zapory uruchomione, istniejące połączenie przychodzące za pomocą reguł DNAT mogą nie działać 40 – 120 sekund. Nie można usunąć publiczny adres IP przypisany do zapory, chyba, że Zapora jest cofnięty lub usunięty.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Dodawanie publicznego adresu IP do istniejącej zapory

W tym przykładzie, publiczny adres IP *azFwPublicIp1* jak dołączone do zapory.

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

W tym przykładzie, publiczny adres IP *azFwPublicIp1* jako odłączona od zapory.

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

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
