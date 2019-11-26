---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485499"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. Learn how to [view your current resource usage against your subscription limits](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> We recently increased all default limits to their maximum limits. If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Zasób | Default/maximum limit | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3,000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| Concurrent TCP or UDP flows per NIC of a virtual machine or role instance |500,000 |
| Network interface cards |65,536 |
| Sieciowe grupy zabezpieczeń |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| IP addresses and ranges specified for source or destination in a security group |4,000 |
| Grupy zabezpieczeń aplikacji |3,000 |
| Application security groups per IP configuration, per NIC |20 |
| IP configurations per application security group |4,000 |
| Application security groups that can be specified within all security rules of a network security group |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| Virtual network TAPs |100 |
| Network interface TAP configurations per virtual network TAP |100 |

#### <a name="publicip-address"></a>Public IP address limits
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Public IP addresses - dynamic | 1,000 for Basic. |Skontaktuj się z pomocą techniczną. |
| Public IP addresses - static | 1,000 for Basic. |Skontaktuj się z pomocą techniczną. |
| Public IP addresses - static | 1,000 for Standard.|Skontaktuj się z pomocą techniczną. |
| Public IP prefix length | /28 | Skontaktuj się z pomocą techniczną. |

#### <a name="load-balancer"></a>Load balancer limits
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Learn how to [view your current resource usage against your subscription limits](../articles/networking/check-usage-against-limits.md).

| Zasób | Default/maximum limit |
| --- | --- |
| Moduły równoważenia obciążenia | 1000 | 
| Rules per resource, Basic | 250 |
| Rules per resource, Standard | 1,500 | 
| Rules per IP configuration | 299 |
| Rules per NIC | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. Learn how to [view your current resource usage against your subscription limits](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |100 |100 |
| Lokalne lokacje sieciowe |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4,096 |4,096 |
| Concurrent TCP or UDP flows per NIC of a virtual machine or role instance |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| Network Security Groups (NSGs) |200 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |1000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| Publiczne adresy IP (dynamiczne) |500 |500 |
| Zastrzeżone publiczne adresy IP |500 |500 |
| Publiczne adresy VIP na wdrożenie |5 |Kontakt z pomocą techniczną |
| Private VIP (internal load balancing) per deployment |1 |1 |
| Endpoint access control lists (ACLs) |50 |50 |
