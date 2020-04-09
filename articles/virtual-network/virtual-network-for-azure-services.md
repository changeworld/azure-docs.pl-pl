---
title: Sieć wirtualna dla usług platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o korzyściach płynących z wdrażania zasobów w sieci wirtualnej. Zasoby w sieciach wirtualnych mogą komunikować się ze sobą i zasobami lokalnymi bez ruchu przechodzącego przez Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 70266a1280b90b4573073d633a918f701f9ee8c2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878276"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md)można komunikować się z zasobami usługi prywatnie za pośrednictwem prywatnych adresów IP.

![Usługi wdrożone w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w sieci wirtualnej mogą komunikować się ze sobą prywatnie za pośrednictwem prywatnych adresów IP. Na przykład bezpośrednie przesyłanie danych między programem HDInsight a programem SQL Server uruchomionym na maszynie wirtualnej w sieci wirtualnej.
- Zasoby lokalne mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem [sieci VPN typu lokacja lokacja (brama SIECI VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [usługi ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Sieci wirtualne można [równorzędnie,](virtual-network-peering-overview.md) aby umożliwić zasoby w sieciach wirtualnych do komunikowania się ze sobą przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są zazwyczaj w pełni zarządzane przez usługę platformy Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie z obciążeniem.
- Wystąpienia usługi są wdrażane w podsieci w sieci wirtualnej. Przychodzący i wychodzący dostęp do sieci dla podsieci musi być otwarty za pośrednictwem [sieciowych grup zabezpieczeń](security-overview.md#network-security-groups), zgodnie ze wskazówkami dostarczonymi przez usługę.
- Niektóre usługi nakładają również ograniczenia na podsieć, w których są wdrażane, ograniczając stosowanie zasad, tras lub łącząc maszyny wirtualne i zasoby usługi w tej samej podsieci. Skontaktuj się z każdą usługą w sprawie konkretnych ograniczeń, ponieważ mogą one ulec zmianie w czasie. Przykładami takich usług są usługi Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- Opcjonalnie usługi mogą wymagać [delegowanej podsieci](virtual-network-manage-subnet.md#add-a-subnet) jako jawnego identyfikatora, który podsieć może obsługiwać określoną usługę. Delegocj, usługi uzyskać jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci delegowane.
- Zobacz przykład odpowiedzi interfejsu API REST w [sieci wirtualnej z delegowaną podsiecią](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Pełną listę usług korzystających z modelu podsieci delegowanej można uzyskać za pośrednictwem interfejsu API [dostępne przedstawicielstwa.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które mogą być wdrażane w sieci wirtualnej

|Kategoria|Usługa| Dedykowany<sup>1</sup>sup></sup> 1 podsieć
|-|-|-|
| Wystąpienia obliczeniowe | Maszyny wirtualne: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Usługa w chmurze:](https://msdn.microsoft.com/library/azure/jj156091)tylko sieć wirtualna (klasyczna)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nie <br/> Nie <br/> Nie <br/> Nr<sup>2</sup>sup>2</sup>
| Sieć | [Brama aplikacji — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Wirtualne urządzenia sieciowe](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Tak <br/> Tak <br/> Tak <br/> Nie
|Dane|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie zarządzane usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Tak <br/> Tak <br/> 
|Analiza | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nr<sup>2</sup>sup>2</su<sup>2</sup>> <br/> Nr<sup>2</sup> <br/> 
| Tożsamość | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nie <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie kontenera platformy Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Aparat usługi kontenerów platformy Azure](https://github.com/Azure/acs-engine) z [wtyczką](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI sieci wirtualnej platformy Azure<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nr<sup>2</sup>sup>2</sup><br/> Tak <br/><br/> Nie <br/> Tak
| sieć Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Środowisko usługi aplikacji](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/> Tak <br/> Tak
| Hostowanej | [Dedykowany moduł HSM platformy Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/>
| | |

<sup>1</sup> "Dedykowane" oznacza, że w tej podsieci można wdrażać tylko zasoby specyficzne dla usług i nie można ich łączyć z maszynami wirtualnymi/VMSS klienta <br/> 
<sup>2</sup> Zaleca się, aby usługi te były świadczone w dedykowanej podsieci, ale nie było to obowiązkowym wymogiem nałożonym przez usługę.
