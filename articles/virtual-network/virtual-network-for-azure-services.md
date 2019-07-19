---
title: Sieć wirtualna dla usług platformy Azure
titlesuffix: Azure Virtual Network
description: Poznaj zalety wdrażania zasobów w sieci wirtualnej. Zasoby w sieciach wirtualnych mogą komunikować się ze sobą i zasobami lokalnymi bez ruchu przechodzącego przez Internet.
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
ms.openlocfilehash: 80d89914f33273fcb033ab47098a8864b11974c9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876165"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integracja sieci wirtualnej dla usług platformy Azure

Integracja usług platformy Azure z usługą Azure Virtual Network umożliwia prywatnym dostęp do usługi z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.
Usługi platformy Azure w sieci wirtualnej można zintegrować z następującymi opcjami:
- Wdrażanie dedykowanych wystąpień usługi w sieci wirtualnej. Usługi mogą być następnie dostępne do prywatnego dostępu w ramach sieci wirtualnej i z sieci lokalnych.
- Rozszerzanie sieci wirtualnej do usługi za pomocą punktów końcowych usługi. Punkty końcowe usługi umożliwiają zabezpieczenie poszczególnych zasobów usług do sieci wirtualnej.

Aby zintegrować wiele usług platformy Azure z siecią wirtualną, możesz połączyć jeden lub więcej z powyższych wzorców. Można na przykład wdrożyć usługę HDInsight w sieci wirtualnej i zabezpieczyć konto magazynu w podsieci HDInsight za pomocą punktów końcowych usługi.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Wdrażanie usług platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md)można komunikować się z zasobami usługi prywatnie, za pomocą prywatnych adresów IP.

![Usługi wdrożone w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w ramach sieci wirtualnej mogą komunikować się ze sobą prywatnie, za pomocą prywatnych adresów IP. Przykładowo bezpośrednie przesyłanie danych między usługą HDInsight i SQL Server uruchomione na maszynie wirtualnej w sieci wirtualnej.
- Zasoby lokalne mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem [sieci VPN typu lokacja-lokacja (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być połączone za pomocą [komunikacji równorzędnej](virtual-network-peering-overview.md) , aby umożliwić komunikację między sieciami wirtualnymi przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są zwykle w pełni zarządzane przez usługę platformy Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie z obciążeniem.
- Wystąpienia usługi są wdrażane w podsieci w sieci wirtualnej. Przychodzący i wychodzący dostęp do sieci dla podsieci musi być otwarty za pomocą [sieciowych grup zabezpieczeń](security-overview.md#network-security-groups), zgodnie ze wskazówkami dostarczonymi przez usługę.
- Niektóre usługi również nakładają ograniczenia dotyczące podsieci, w których są wdrażane, ograniczają stosowanie zasad, trasy lub łączenie maszyn wirtualnych i zasobów usług w tej samej podsieci. Sprawdź każdą z usług zgodnie z określonymi ograniczeniami, ponieważ mogą one ulec zmianie w czasie. Przykładami takich usług są Azure NetApp Files, dedykowany moduł HSM Azure Container Instances, App Service. 
- Opcjonalnie usługi mogą wymagać delegowanej [podsieci](virtual-network-manage-subnet.md#add-a-subnet) jako jawnego identyfikatora, który może hostować określoną usługę w podsieci. Dzięki delegowaniu usługi uzyskują jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci delegowanej.
- Zapoznaj się z przykładem odpowiedzi interfejsu API REST w [sieci wirtualnej z delegowaną](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)podsiecią. Kompleksowa Lista usług, które korzystają z delegowanego modelu podsieci, można uzyskać za pośrednictwem interfejsu API [dostępne delegacje](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) .

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które można wdrożyć w sieci wirtualnej

|Kategoria|Usługa| Podsieć dedykowana
|-|-|-|
| Wystąpienia obliczeniowe | Maszyny wirtualne: System [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Usługa w chmurze](https://msdn.microsoft.com/library/azure/jj156091): Tylko sieć wirtualna (klasyczna)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nie <br/> Nie <br/> Nie <br/> No²
| Sieć | [Application Gateway — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Zapora platformy Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Wirtualne urządzenia sieciowe](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Tak <br/> Yes <br/> Yes <br/> Nie
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie zarządzane usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Yes <br/> Tak <br/> 
|Analiza | [Usługa Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Tożsamość | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nie <br/>
| Containers | [Usługa Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie kontenera platformy Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Aparat Azure Container Service](https://github.com/Azure/acs-engine) z [dodatkiem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) platformy Azure Virtual Network CNI|No²<br/> Yes <br/><br/> Nie
| sieć Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Yes <br/> Yes <br/> Yes
| Przez | [Dedykowany moduł HSM platformy Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/>
| | |

"dedykowany" oznacza, że w tej podsieci można wdrożyć tylko zasoby określone dla usługi i nie można ich łączyć z maszyną wirtualną/VMSSs klienta <br/> zalecane, ale nie wymaganie obowiązkowe narzucone przez usługę.


## <a name="service-endpoints-for-azure-services"></a>Punkty końcowe usługi dla usług platformy Azure

Nie można wdrożyć niektórych usług platformy Azure w sieciach wirtualnych. Możesz ograniczyć dostęp do niektórych zasobów usług tylko do określonych podsieci sieci wirtualnej, jeśli zdecydujesz się na to, włączając punkt końcowy usługi sieci wirtualnej.  Dowiedz się więcej o [punktach końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)oraz usługach, dla których można włączyć punkty końcowe.
