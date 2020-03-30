---
title: Wdrażanie zestawów skalowania maszyn wirtualnych z ipv6 na platformie Azure
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć zestawy skalowania maszyny wirtualnej z IPv6 w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164993"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Wdrażanie zestawów skalowania maszyny wirtualnej z ipv6 na platformie Azure (wersja zapoznawcza)

W tym artykule pokazano, jak wdrożyć zestaw skalowania maszyny wirtualnej z dwoma stosami (IPv4 + IPv6) z podwójnym modułem równoważenia obciążenia zewnętrznego stosu w sieci wirtualnej platformy Azure. Proces tworzenia zestawu skalowania maszyny wirtualnej obsługującej iPv6 jest prawie identyczny z procesem tworzenia poszczególnych maszyn wirtualnych opisanych [w tym miejscu.](ipv6-configure-standard-load-balancer-template-json.md) Kroki, które są podobne do tych opisanych dla poszczególnych maszyn wirtualnych:
1.  Tworzenie publicznych usług IPv4 i IPv6.
2.  Utwórz moduł równoważenia obciążenia z dwoma stosami.  
3.  Tworzenie reguł sieciowej grupy zabezpieczeń (NSG).  

Jedynym krokiem, który różni się od poszczególnych maszyn wirtualnych jest utworzenie konfiguracji interfejsu sieciowego (NIC), który używa zasobu zestawu skalowania maszyny wirtualnej: networkProfile/networkInterfaceConfigurations. Struktura JSON jest podobna do obiektu Microsoft.Network/networkInterfaces używanego dla poszczególnych maszyn wirtualnych z dodatkiem ustawiania karty sieciowej i konfiguracji IpConfiguration IPv4 jako interfejsu podstawowego przy użyciu **atrybutu "primary": true,** jak pokazano w poniższym przykładzie:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Przykładowy szablon zestawu skalowania maszyny wirtualnej JSON

Aby wdrożyć podwójny stos (IPv4 + IPv6) Virtual Machine Scale Set z podwójnym stosem zewnętrznego modułu równoważenia obciążenia i przykładowego szablonu widoku sieci wirtualnej [w tym miejscu](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md).
