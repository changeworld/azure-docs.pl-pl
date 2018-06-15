---
title: Skonfiguruj punkty końcowe węzła w puli partii zadań Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować lub wyłączyć dostępu do portów SSH lub RDP na węzłach obliczeniowych w puli partii zadań Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: 5898206761e5029f94b6d1f1b48223481ae2ca13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358731"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurowanie lub wyłączanie dostępu zdalnego do wyliczenia węzłów w puli partii zadań Azure

Domyślnie Umożliwia wsadowe [użytkownika węzła](/rest/api/batchservice/computenode/adduser) z łącznością sieciową nawiązać zewnętrznie węźle obliczeń w puli partii. Na przykład użytkownik może łączyć przez RDP (Remote Desktop) na portu 3389 na węźle obliczeń w puli systemu Windows. Podobnie Domyślnie użytkownik może łączyć przez Secure Shell (SSH) w węźle obliczeń w puli Linux port 22. 

W danym środowisku konieczne może być ograniczenia lub wyłączyć te ustawienia domyślne dostępu zewnętrznego. Te ustawienia można modyfikować za pomocą interfejsów API partii ustawić [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) właściwości. 

## <a name="about-the-pool-endpoint-configuration"></a>Konfiguracja punktu końcowego puli — informacje
Konfiguracja punktu końcowego, który składa się z co najmniej jeden [pule adresów translatora adresów sieciowych](/rest/api/batchservice/pool/add#inboundnatpool) portów frontonu. (Nie należy mylić z puli partii węzłów obliczeniowych pulą translatora adresów Sieciowych). Skonfigurowaniu każda pula NAT na zastąpienie domyślnych ustawień połączenia w węzłach obliczeń puli. 

Każdej konfiguracji puli NAT zawiera co najmniej jedną [sieci reguł zabezpieczeń grupy (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Każdej reguły NSG zezwala lub nie zezwala na przesyłanie konkretnego ruchu sieciowego do punktu końcowego. Można akceptować lub odrzucać cały ruch, ruch identyfikowane przez [usługi tag](../virtual-network/security-overview.md#service-tags) (na przykład "Internet"), lub ruch z określonych adresów IP lub podsieci.

### <a name="considerations"></a>Zagadnienia do rozważenia
* Konfiguracja punktu końcowego pula jest częścią puli [konfiguracji sieci](/rest/api/batchservice/pool/add#NetworkConfiguration). Konfigurację sieci można opcjonalnie dołączyć ustawienia puli, aby dołączyć do [sieci wirtualnej platformy Azure](batch-virtual-network.md). Jeśli skonfigurowano puli w sieci wirtualnej można utworzyć reguły NSG, które używają ustawień adresu w sieci wirtualnej.
* Po skonfigurowaniu puli translacji adresów Sieciowych, można skonfigurować wiele reguł NSG. Reguły są sprawdzane według ważności. Gdy reguła ma zastosowanie, żadne inne reguły nie są sprawdzane pod kątem dopasowania.


## <a name="example-deny-all-rdp-traffic"></a>Przykład: Odmowa cały ruch RDP

Poniższy fragment kodu C# przedstawiono sposób konfigurowania punktu końcowego protokołu RDP w węzłach obliczeń w puli systemu Windows, aby zablokować cały ruch sieciowy. Punkt końcowy korzysta z puli frontonu porty w zakresie *60000 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Przykład: Odmowa cały ruch SSH z Internetu

Poniższy fragment kodu języka Python pokazano, jak skonfigurować punkt końcowy SSH w węzłach obliczeń w puli systemu Linux, aby zablokować cały ruch internetowy. Punkt końcowy korzysta z puli frontonu porty w zakresie *4000 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Przykład: Zezwalaj na ruch RDP z określonego adresu IP

Poniższy fragment kodu C# przedstawiono sposób konfigurowania punktu końcowego protokołu RDP w węzłach obliczeń w puli systemu Windows, aby zezwolić na dostęp RDP tylko z adresu IP *198.51.100.7*. Drugi reguły NSG nie zezwala na ruch, który jest niezgodny z adresu IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Przykład: Zezwalać na ruch protokołu SSH z określonej podsieci

Poniższy fragment kodu języka Python pokazano, jak skonfigurować punkt końcowy SSH w węzłach obliczeń w puli systemu Linux, aby zezwolić na dostęp tylko z podsieci *192.168.1.0/24*. Drugi reguły NSG nie zezwala na ruch, który jest niezgodny z podsiecią.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji dotyczących reguły NSG na platformie Azure, zobacz [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](../virtual-network/security-overview.md).

- Aby uzyskać szczegółowe informacje o partii, zobacz [Programowanie równoległe na dużą skalę obliczeniowe rozwiązań w partii](batch-api-basics.md).

