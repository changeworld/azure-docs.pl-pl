---
title: Wdrażanie zapory platformy Azure ze strefami dostępności przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zaporę platformy Azure ze strefami dostępności przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195923"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Wdrażanie usługi Azure Firewall ze strefami dostępności przy użyciu programu Azure PowerShell

Zaporę platformy Azure można skonfigurować podczas wdrażania, aby objąć wiele stref dostępności w celu zwiększenia dostępności.

Ta funkcja umożliwia następujące scenariusze:

- Możesz zwiększyć dostępność do 99,99% czasu pracy. Aby uzyskać więcej informacji, zobacz Umowę dotyczącą poziomu usług zapory azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% dostępności SLA jest oferowana po wybraniu dwóch lub więcej stref dostępności.
- Możesz również skojarzyć Zaporę platformy Azure z określoną strefą tylko ze względów zbliżeniowych, korzystając ze standardowej umowy SLA 99,95%.

Aby uzyskać więcej informacji na temat stref dostępności zapory platformy Azure, zobacz [Co to jest Zapora azure?](overview.md)

W poniższym przykładzie programu Azure PowerShell pokazano, jak można wdrożyć zaporę platformy Azure ze strefami dostępności.

## <a name="create-a-firewall-with-availability-zones"></a>Tworzenie zapory ze strefami dostępności

W tym przykładzie tworzy zaporę w strefach 1, 2 i 3.

Podczas tworzenia standardowego publicznego adresu IP nie określono określonej strefy. Spowoduje to utworzenie domyślnie nadmiarowego adresu IP strefy. Standardowe publiczne adresy IP można skonfigurować we wszystkich strefach lub w jednej strefie.

Warto wiedzieć, ponieważ nie można mieć zapory w strefie 1 i adresu IP w strefie 2. Ale możesz mieć zaporę w strefie 1 i adres IP we wszystkich strefach lub zaporę i adres IP w tej samej pojedynczej strefie do celów zbliżeniowych.

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

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
