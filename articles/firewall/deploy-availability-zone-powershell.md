---
title: Wdrażanie zapory platformy Azure Dzięki strefom dostępności przy użyciu programu Azure PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zapory platformy Azure Dzięki strefom dostępności przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276914"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Wdrażanie zapory platformy Azure Dzięki strefom dostępności przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Zaporę platformy Azure Dzięki strefom dostępności jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podczas wdrażania na wielu strefach dostępności na potrzeby zwiększonej dostępności można skonfigurować zaporę platformy Azure.

Ta funkcja umożliwia następujące scenariusze:

- Można zwiększyć dostępność przez 99,99% czasu. Aby uzyskać więcej informacji, zobacz zapory usługi Azure [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Czas działania 99,99% umowy SLA jest oferowana po wybraniu co najmniej dwóch strefach dostępności.
- Można również skojarzyć zapory usługi Azure do określonej strefy tylko ze względów odległości między elementami przy użyciu usług standardowa SLA 99,95% czasu.

Aby uzyskać więcej informacji o strefach dostępności zapory platformy Azure, zobacz [co to jest zapory usługi Azure?](overview.md)

W poniższym przykładzie programu Azure PowerShell pokazuje, jak może wdrożyć zapory platformy Azure Dzięki strefom dostępności.

## <a name="create-a-firewall-with-availability-zones"></a>Tworzenie zapory przy użyciu strefy dostępności

W tym przykładzie tworzy zaporę w stref 1, 2 i 3.

Po utworzeniu standardowy publiczny adres IP jest określona nie określonej strefy. Domyślnie to tworzy strefowo nadmiarowe adresu IP. Standardowe publiczne adresy IP można skonfigurować w wszystkie strefy lub jedną strefę.

Ważne jest dowiedzieć się, ponieważ nie może mieć zapory w strefie 1, a adres IP, które znajdują się w strefie 2. Jednak może mieć zapory w strefie 1 i adresów IP na wszystkich stref lub zaporą i adres IP, które znajdują się w tej samej strefie pojedynczego celów odległości między elementami.

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

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
