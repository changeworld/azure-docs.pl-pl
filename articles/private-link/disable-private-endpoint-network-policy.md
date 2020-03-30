---
title: Wyłączanie zasad sieciowych dla prywatnych punktów końcowych na platformie Azure
description: Dowiedz się, jak wyłączyć zasady sieciowe dla prywatnych punktów końcowych.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453017"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Wyłączanie zasad sieciowych dla prywatnych punktów końcowych

Zasady sieciowe, takie jak sieciowe grupy zabezpieczeń (NSG), nie są obsługiwane dla prywatnych punktów końcowych. Aby wdrożyć prywatny punkt końcowy w danej podsieci, w tej podsieci wymagane jest jawne ustawienie wyłączenia. To ustawienie ma zastosowanie tylko do prywatnego punktu końcowego. W przypadku innych zasobów w podsieci dostęp jest kontrolowany na podstawie definicji reguł zabezpieczeń sieciowych grup zabezpieczeń (NSG). 
 
Podczas korzystania z portalu do tworzenia prywatnego punktu końcowego, to ustawienie jest automatycznie wyłączane jako część procesu tworzenia. Wdrożenie przy użyciu innych klientów wymaga dodatkowego kroku, aby zmienić to ustawienie. Można wyłączyć ustawienie przy użyciu powłoki chmury z witryny Azure portal lub lokalnych instalacji platformy Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub użyć szablonów usługi Azure Resource Manager.  
 
W poniższych przykładach `PrivateEndpointNetworkPolicies` opisano sposób wyłączania sieci wirtualnej o nazwie *myVirtualNetwork* z *domyślną* podsieci hostowanym w grupie zasobów o nazwie *myResourceGroup*.

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji opisano sposób wyłączania zasad prywatnego punktu końcowego podsieci przy użyciu programu Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W tej sekcji opisano sposób wyłączania zasad prywatnych punktów końcowych podsieci przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Korzystanie z szablonu
W tej sekcji opisano sposób wyłączania zasad prywatnych punktów końcowych podsieci przy użyciu szablonu usługi Azure Resource Manager.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](private-endpoint-overview.md)
 
