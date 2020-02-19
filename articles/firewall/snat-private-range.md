---
title: Zakresy prywatnych adresów IP zapory systemu Azure
description: Można skonfigurować zakresy prywatne adresów IP, aby zapora nie mogła przywiązać ruchu do tych adresów IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444461"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Zakresy prywatnych adresów IP zapory systemu Azure

Zapora platformy Azure nie ma protokołu IPSec, gdy docelowy adres IP należy do zakresu prywatnego adresu IP na [organizację IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure przywróci ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Można jednak skonfigurować zaporę platformy Azure w taki sposób, aby **nie** przywiązać do tego zakresu publicznych adresów IP.

## <a name="configure-snat-private-ip-address-ranges"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania

Można użyć Azure PowerShell, aby określić zakres adresów IP, który Zapora nie będzie poddany.

### <a name="new-firewall"></a>Nowa Zapora

W przypadku nowej zapory polecenie Azure PowerShell jest następujące:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges jest rozwinięty do bieżących ustawień domyślnych w zaporze platformy Azure, podczas gdy inne zakresy są do niej dodawane.

Aby uzyskać więcej informacji, zobacz polecenie [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Istniejąca Zapora

Aby skonfigurować istniejącą zaporę, użyj następujących poleceń Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Szablony

Do sekcji `additionalProperties` można dodać następujące elementy:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).