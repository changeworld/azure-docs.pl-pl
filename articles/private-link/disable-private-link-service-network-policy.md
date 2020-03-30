---
title: 'Wyłączanie zasad sieciowych dla źródłowego adresu IP usługi Azure Private Link '
description: Dowiedz się, jak wyłączyć zasady sieciowe dla prywatnej łącza platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453007"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Wyłączanie zasad sieciowych dla źródłowego adresu IP usługi Private Link

Aby wybrać źródłowy adres IP dla usługi Private Link, w podsieci wymagane jest jawne ustawienie wyłączenia. `privateLinkServiceNetworkPolicies` To ustawienie ma zastosowanie tylko do określonego prywatnego adresu IP wybranego jako źródłowy adres IP usługi Private Link. W przypadku innych zasobów w podsieci dostęp jest kontrolowany na podstawie definicji reguł zabezpieczeń sieciowych grup zabezpieczeń (NSG). 
 
Podczas korzystania z dowolnego klienta platformy Azure (PowerShell, cli lub szablonów), dodatkowy krok jest wymagane, aby zmienić tę właściwość. Zasady można wyłączyć przy użyciu powłoki chmury z witryny Azure portal lub lokalnych instalacji platformy Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub użyć szablonów usługi Azure Resource Manager.  
 
Wykonaj poniższe czynności, aby wyłączyć zasady sieci usługi łączy prywatnych dla sieci wirtualnej o nazwie *myVirtualNetwork* z *domyślną* podsiecią hostowanej w grupie zasobów o nazwie *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji opisano sposób wyłączania zasad prywatnego punktu końcowego podsieci przy użyciu programu Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W tej sekcji opisano sposób wyłączania zasad prywatnych punktów końcowych podsieci przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](private-endpoint-overview.md)
 
