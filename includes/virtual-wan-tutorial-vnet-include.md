---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744435"
---
Aby szybko utworzyć sieć wirtualną, możesz kliknąć "Try It" w tym artykule, aby otworzyć konsolę programu PowerShell. Dopasuj wartości, a następnie skopiuj polecenia i wklej je do okna konsoli. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](/powershell/azure/new-azureps-module-az). Aby uzyskać instrukcje instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

Sprawdź, czy przestrzeń adresowa utworzona dla sieci wirtualnej nie pokrywa się z żadnymi zakresami adresów innych sieci wirtualnych, z którymi chcesz się połączyć, ani z przestrzeniami adresowymi sieci lokalnej.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli nie masz jeszcze grupy zasobów, które chcesz użyć, należy utworzyć nową. Dostosuj poleceń programu PowerShell w celu odzwierciedlenia nazwy grupy zasobów, której chcesz użyć, a następnie uruchom następujące polecenie cmdlet:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Dostosuj polecenia programu PowerShell, aby utworzyć sieć wirtualną, która jest zgodna w danym środowisku.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```