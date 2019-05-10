---
title: Sieć wirtualna dla usług platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o korzyściach związanych z wdrażaniem zasobów w sieci wirtualnej. Zasoby w sieciach wirtualnych mogą komunikować się ze sobą i zasobów lokalnych, bez ruch przechodzący przez Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409504"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integracja sieci wirtualnej dla usług platformy Azure

Integrowanie usług platformy Azure z usługą Azure virtual network umożliwia prywatny dostęp do usługi z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.
Integracja z usługami platformy Azure, w sieci wirtualnej przy użyciu następujących opcji:
- Wdrażanie dedykowanych wystąpień usługi w sieci wirtualnej. Usługi mają prywatnie dostęp w sieci wirtualnej i z sieciami lokalnymi.
- Rozszerzanie sieci wirtualnej do usługi za pośrednictwem punktów końcowych usługi. Punkty końcowe usługi umożliwiają zasobom konkretną usługę być chronione w sieci wirtualnej.

Aby zintegrować wiele usług platformy Azure z siecią wirtualną, możesz połączyć co najmniej jedną z powyższych wzorców. Na przykład można wdrożyć HDInsight w sieci wirtualnej i zabezpieczenia konta magazynu do podsieci HDInsight za pośrednictwem punktów końcowych usługi.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Wdrażaj usługi platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md), można komunikować się z zasobami usługi prywatnie, przy użyciu prywatnych adresów IP.

![Usług wdrożonych w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w sieci wirtualnej mogą komunikować się ze sobą prywatnie, przy użyciu prywatnych adresów IP. Przykład bezpośrednio przesyłania danych między HDInsight i programu SQL Server uruchomionego na maszynie wirtualnej w sieci wirtualnej.
- Zasoby mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem lokalnej [Site-to-Site VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być [skomunikowane równorzędnie](virtual-network-peering-overview.md) umożliwiające z zasobami w sieciach wirtualnych do komunikowania się ze sobą przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej zwykle są w pełni zarządzane przez usługę Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie przy obciążeniu.
- Wystąpienia usługi są wdrażane w podsieci sieci wirtualnej. Dla ruchu przychodzącego i dostępu sieciowego ruchu wychodzącego dla podsieci muszą być otwarte przez [sieciowe grupy zabezpieczeń](security-overview.md#network-security-groups), na wskazówki udostępniane przez usługę.
- Niektóre usługi również nakładają ograniczenia dotyczące podsieci, w której są wdrożone, ograniczając stosowania zasad, tras lub łączenie maszyn wirtualnych i zasobów usługi w ramach tej samej podsieci. Skontaktuj się z każdej usługi w szczególnych ograniczeń, ponieważ mogą one ulec zmianie wraz z upływem czasu. Przykłady takich usług to usługi Azure Files NetApp, dedykowany modułu HSM usługi Azure Container Instances, usługa App Service. 
- Opcjonalnie można wymagać usługi [delegowane podsieci](virtual-network-manage-subnet.md#add-a-subnet) jako jawne identyfikatora podsieci hostować określonej usługi. Delegowanie, usługi uzyskać jawnych uprawnień, aby utworzyć zasoby specyficzne dla usługi w podsieci delegowanego.
- Zobacz przykład odpowiedzi interfejsu API REST na [sieć wirtualną z podsiecią delegowanego](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). Pełną listę usług, które korzystają z modelu delegowanego podsieci można uzyskać za pośrednictwem [dostępne delegacji](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) interfejsu API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które mogą być wdrażane w sieci wirtualnej

|Category|Usługa| Dedicated¹ podsieci
|-|-|-|
| Wystąpienia obliczeniowe | Maszyny wirtualne: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): Sieć wirtualna (klasyczna) tylko<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nie <br/> Nie <br/> Nie <br/> No²
| Sieć | [Application Gateway — Zapora aplikacji sieci Web](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Zaporę platformy Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Wirtualne urządzenia sieciowe](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Tak <br/> Yes <br/> Yes <br/> Nie
|Dane|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie zarządzane usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Tak <br/> Tak <br/> 
|Analiza | [Usługa Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Tożsamość | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nie <br/>
| Containers | [Usługa Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie kontenera platformy Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Aparat usługi w usłudze Azure kontenera](https://github.com/Azure/acs-engine) za pomocą wtyczki Azure wirtualnych sieci CNI [wtyczki](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Tak <br/><br/> Nie
| Sieć Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Yes <br/> Tak
| Hostowany | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/>
| | |

¹ "dedykowana" oznacza, że tylko usługi określonych zasobów mogą być wdrażane w tej podsieci i nie można połączyć z maszyny Wirtualnej/VMSSs klienta <br/> ² zalecane, ale nie wymagane nałożone przez usługę.


## <a name="service-endpoints-for-azure-services"></a>Punkty końcowe usługi dla usług platformy Azure

Nie można wdrożyć niektórych usług platformy Azure w sieciach wirtualnych. Jeśli wybierzesz, po włączeniu punktu końcowego usługi sieci wirtualnej, możesz ograniczyć dostęp do niektórych zasobów usługi do podsieci sieci wirtualnej tylko określone.  Dowiedz się więcej o [punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)i usług, które można włączyć dla punktów końcowych.
