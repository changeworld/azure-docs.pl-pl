---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004172"
---
Jeśli nie masz jeszcze sieci wirtualnej, możesz ją szybko utworzyć za pomocą programu PowerShell. Możesz również utworzyć sieć wirtualną w witrynie Azure Portal.

* Sprawdź, czy przestrzeń adresowa utworzona dla sieci wirtualnej nie pokrywa się z żadnymi zakresami adresów innych sieci wirtualnych, z którymi chcesz się połączyć, ani z przestrzeniami adresowymi sieci lokalnej. 
* Jeśli masz już sieć wirtualną, sprawdź, czy spełnia ona wymagane kryteria i nie ma bramy sieci wirtualnej.

Możesz łatwo utworzyć sieć wirtualną, klikając polecenie „Wypróbuj” w tym artykule, co spowoduje otwarcie konsoli programu PowerShell. Dopasuj wartości, a następnie skopiuj polecenia i wklej je do okna konsoli.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Dostosuj polecenia programu PowerShell, a następnie utwórz grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Dostosuj polecenia programu PowerShell, aby utworzyć sieć wirtualną zgodną z Twoim środowiskiem.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```