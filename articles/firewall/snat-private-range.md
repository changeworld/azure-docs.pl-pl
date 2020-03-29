---
title: Zakresy prywatnych adresów IP zapory azure SNAT
description: Można skonfigurować zakresy prywatne adresów IP, aby zapora nie snat ruchu do tych adresów IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064813"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Zakresy prywatnych adresów IP zapory azure SNAT

Zapora azure nie SNAT z reguł sieci, gdy docelowy adres IP znajduje się w zakresie prywatnych adresów IP na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Reguły aplikacji są zawsze stosowane przy użyciu [przezroczystego serwera proxy,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) niezależnie od docelowego adresu IP.

Jeśli twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, usługa Azure Firewall sNATs ruchu do jednego z zapory prywatnych adresów IP w usłudze AzureFirewallSubnet. Można jednak skonfigurować zaporę platformy Azure, aby **nie** snat zakres publicznego adresu IP.

## <a name="configure-snat-private-ip-address-ranges"></a>Konfigurowanie zakresów prywatnych adresów IP SNAT

Za pomocą programu Azure PowerShell można określić zakres adresów IP, że zapora nie będzie SNAT.

### <a name="new-firewall"></a>Nowa zapora sieciowa

W przypadku nowej zapory polecenie programu Azure PowerShell jest następujące:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges jest rozwijany do bieżących ustawień domyślnych na Zaporze platformy Azure, podczas gdy inne zakresy są dodawane do niego.

Aby uzyskać więcej informacji, zobacz [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Istniejąca zapora

Aby skonfigurować istniejącą zaporę, użyj następujących poleceń programu Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Szablony

Do `additionalProperties` sekcji można dodać następujące elementy:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md).