---
title: Konfigurowanie punktów końcowych węzeł w puli Azure Batch | Dokumentacja firmy Microsoft
description: Jak skonfigurować lub wyłączyć dostęp do portów SSH lub RDP w węzłach obliczeniowych w puli usługi Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: 9b6b28b9f55623fbdff6ab80c889141c8815600f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071528"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli usługi Azure Batch

Domyślnie usługa Batch umożliwia [użytkownika węzła](/rest/api/batchservice/computenode/adduser) z łącznością sieciową, do łączenia z zewnątrz do węzła obliczeniowego w puli usługi Batch. Na przykład użytkownik może połączyć się przez RDP (Remote Desktop) na porcie 3389 do węzła obliczeniowego w puli Windows. Podobnie domyślnie, użytkownik może łączyć przez Secure Shell (SSH) na porcie 22 do węzła obliczeniowego w puli systemu Linux. 

W danym środowisku konieczne może być ograniczyć lub wyłączyć te domyślne ustawienia dostępu zewnętrznego. Te ustawienia można modyfikować za pomocą interfejsów API usługi Batch można ustawić [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) właściwości. 

## <a name="about-the-pool-endpoint-configuration"></a>Temat pul konfiguracji punktu końcowego
Konfiguracja punktu końcowego, który składa się z co najmniej jeden [pule translatora (NAT) adres sieci](/rest/api/batchservice/pool/add#inboundnatpool) portów frontonu. (Nie należy mylić z puli usługi Batch, węzłów obliczeniowych puli translatora adresów Sieciowych). Konfigurowanie każdej puli translatora adresów Sieciowych w celu zastąpienia domyślnych ustawień połączenia w węzłach obliczeniowych w puli. 

Każda Konfiguracja puli translatora adresów Sieciowych zawiera co najmniej jedną [reguł Sieciowej grupy zabezpieczeń sieciowych](/rest/api/batchservice/pool/add#networksecuritygrouprule). Każda reguła sieciowej grupy zabezpieczeń zezwala lub nie konkretnego ruchu sieciowego do punktu końcowego. Można wybrać udzielić lub odmówić cały ruch, ruch identyfikowane przez [tag usługi](../virtual-network/security-overview.md#service-tags) (na przykład "Internet"), lub ruchu z określonych adresów IP lub podsieci.

### <a name="considerations"></a>Zagadnienia do rozważenia
* Konfiguracja punktu końcowego puli jest częścią puli [konfiguracji sieci](/rest/api/batchservice/pool/add#networkconfiguration). Konfiguracja sieci może opcjonalnie obejmować ustawienia do dołączenia do puli aby [sieci wirtualnej platformy Azure](batch-virtual-network.md). Po skonfigurowaniu puli w sieci wirtualnej można utworzyć reguły sieciowej grupy zabezpieczeń, które używają ustawień adresów w sieci wirtualnej.
* Po skonfigurowaniu puli translatora adresów Sieciowych, można skonfigurować wiele reguł sieciowej grupy zabezpieczeń. Reguły są sprawdzane według ważności. Gdy reguła ma zastosowanie, żadne inne reguły nie są sprawdzane pod kątem dopasowania.


## <a name="example-deny-all-rdp-traffic"></a>Przykład: Odmów cały ruch RDP

Następujące C# fragment kodu przedstawia sposób konfigurowania punktu końcowego protokołu RDP w węzłach obliczeniowych w puli Windows, aby zablokować cały ruch sieciowy. Punkt końcowy korzysta z puli frontonu portów z zakresu *60000 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Przykład: Odmów cały ruch SSH z Internetu

Poniższy fragment kodu języka Python pokazuje, jak skonfigurować punkt końcowy SSH w węzłach obliczeniowych w puli systemu Linux, aby zablokować cały ruch z Internetu. Punkt końcowy korzysta z puli frontonu portów z zakresu *4000 4100*. 

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

Następujące C# fragment kodu przedstawia sposób konfigurowania punktu końcowego protokołu RDP w węzłach obliczeniowych w puli Windows, aby umożliwić dostęp RDP tylko z adresu IP *198.51.100.7*. Druga reguła sieciowej grupy zabezpieczeń nie zezwala na ruch, który nie jest zgodny z adresem IP.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Przykład: Zezwalaj na ruch SSH z określonej podsieci

Poniższy fragment kodu języka Python pokazuje, jak skonfigurować punkt końcowy SSH w węzłach obliczeniowych w puli systemu Linux, aby zezwolić na dostęp tylko z podsieci *192.168.1.0/24*. Druga reguła sieciowej grupy zabezpieczeń nie zezwala na ruch, który nie jest zgodny z podsieci.

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

- Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń na platformie Azure, zobacz [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).

- Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).

