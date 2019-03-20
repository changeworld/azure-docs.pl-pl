---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552993"
---
Jeśli nie masz jeszcze sieci wirtualnej, możesz ją szybko utworzyć za pomocą programu PowerShell. Możesz również utworzyć sieć wirtualną w witrynie Azure Portal.

* Sprawdź, czy przestrzeń adresowa utworzona dla sieci wirtualnej nie pokrywa się z żadnymi zakresami adresów innych sieci wirtualnych, z którymi chcesz się połączyć, ani z przestrzeniami adresowymi sieci lokalnej. 
* Jeśli masz już sieć wirtualną, sprawdź, czy spełnia ona wymagane kryteria i nie ma bramy sieci wirtualnej.

Możesz łatwo utworzyć sieć wirtualną, klikając polecenie „Wypróbuj” w tym artykule, co spowoduje otwarcie konsoli programu PowerShell. Dopasuj wartości, a następnie skopiuj polecenia i wklej je do okna konsoli.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Dostosuj polecenia programu PowerShell, a następnie utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Dostosuj polecenia programu PowerShell, aby utworzyć sieć wirtualną zgodną z Twoim środowiskiem.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```