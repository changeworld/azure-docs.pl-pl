---
title: Konfigurowanie punktów końcowych węzłów w puli usługi Azure Batch | Dokumenty firmy Microsoft
description: Jak skonfigurować lub wyłączyć dostęp do portów SSH lub RDP w węzłach obliczeniowych w puli usługi Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017228"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli usługi Azure Batch

Domyślnie usługa Batch umożliwia [użytkownikowi węzła](/rest/api/batchservice/computenode/adduser) z łącznością sieciową łączenie się zewnętrznie z węzłem obliczeniowym w puli usługi Batch. Na przykład użytkownik może połączyć się za pomocą pulpitu zdalnego (RDP) na porcie 3389 z węzłem obliczeniowym w puli systemu Windows. Podobnie domyślnie użytkownik może połączyć się przez Secure Shell (SSH) na porcie 22 do węzła obliczeniowego w puli systemu Linux. 

W twoim środowisku może być konieczne ograniczenie lub wyłączenie tych domyślnych ustawień dostępu zewnętrznego. Można zmodyfikować te ustawienia za pomocą batch API, aby ustawić [Właściwość PoolEndpointConfiguration.](/rest/api/batchservice/pool/add#poolendpointconfiguration) 

## <a name="about-the-pool-endpoint-configuration"></a>Konfiguracja punktu końcowego puli — informacje
Konfiguracja punktu końcowego składa się z co najmniej jednej [puli translacji adresów sieciowych (NAT) portów](/rest/api/batchservice/pool/add#inboundnatpool) frontonetu. (Nie należy mylić puli NAT z pulą partii węzłów obliczeniowych). Każdą pulę NAT można skonfigurować w celu zastąpienia domyślnych ustawień połączenia w węzłach obliczeniowych puli. 

Każda konfiguracja puli translatora i odpowiedzi zawiera co najmniej jedną [regułę sieciowej grupy zabezpieczeń .](/rest/api/batchservice/pool/add#networksecuritygrouprule) Każda reguła sieciowej sieciowej sieciowej zezwala lub odrzuca określony ruch sieciowy do punktu końcowego. Można zezwolić lub odmówić całego ruchu, ruchu identyfikowanego przez [tag usługi](../virtual-network/security-overview.md#service-tags) (na przykład "Internet") lub ruchu z określonych adresów IP lub podsieci.

### <a name="considerations"></a>Zagadnienia do rozważenia
* Konfiguracja punktu końcowego puli jest częścią [konfiguracji sieci](/rest/api/batchservice/pool/add#networkconfiguration)puli . Konfiguracja sieci może opcjonalnie zawierać ustawienia, aby dołączyć do puli do [sieci wirtualnej platformy Azure](batch-virtual-network.md). Jeśli pulę skonfigurowaną w sieci wirtualnej można utworzyć reguły sieciowej grupy danych sieciowych, które używają ustawień adresu w sieci wirtualnej.
* Podczas konfigurowania puli translator adresów sieciowych można skonfigurować wiele reguł sieciowej grupy. Zasady są sprawdzane w kolejności priorytetu. Gdy reguła ma zastosowanie, żadne inne reguły nie są sprawdzane pod kątem dopasowania.


## <a name="example-deny-all-rdp-traffic"></a>Przykład: Odmów całego ruchu RDP

Poniższy fragment kodu języka C# pokazuje, jak skonfigurować punkt końcowy RDP w węzłach obliczeniowych w puli systemu Windows, aby odmówić całego ruchu sieciowego. Punkt końcowy używa puli frontend portów w zakresie *60000 - 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Przykład: Odmów całego ruchu SSH z Internetu

Poniższy fragment kodu języka Python pokazuje, jak skonfigurować punkt końcowy SSH w węzłach obliczeniowych w puli systemu Linux, aby odmówić całego ruchu internetowego. Punkt końcowy używa puli frontend portów w zakresie *4000 - 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Przykład: zezwalaj na ruch RDP z określonego adresu IP

Poniższy fragment kodu języka C# pokazuje, jak skonfigurować punkt końcowy RDP w węzłach obliczeniowych w puli systemu Windows, aby umożliwić dostęp rdp tylko z adresu IP *198.51.100.7*. Druga reguła sieciowej sieciowej sieciowej odmawia ruch, który nie jest zgodny z adresem IP.

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

Poniższy fragment kodu języka Python pokazuje, jak skonfigurować punkt końcowy SSH w węzłach obliczeniowych w puli systemu Linux, aby umożliwić dostęp tylko z podsieci *192.168.1.0/24*. Druga reguła sieciowej sieciowej sieciowej odmawia ruch, który nie pasuje do podsieci.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń na platformie Azure, zobacz [Filtrowanie ruchu sieciowego za pomocą sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).

- Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).

