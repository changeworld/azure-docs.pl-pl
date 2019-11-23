---
title: Punkty końcowe usługi dla sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć bezpośredni dostęp do zasobów platformy Azure z sieci wirtualnej przy użyciu punktów końcowych usługi.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378691"
---
# <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**Ogólnie dostępne**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**Publiczna wersja zapoznawcza**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

Najbardziej aktualne powiadomienia można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Najważniejsze korzyści

Punkty końcowe usługi oferują następujące korzyści:

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure. 

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Zachowywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem tunelowania wymuszanego, bez wywierania wpływu na ruch usługi. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Prosta konfiguracja i mniejsze koszty ogólne zarządzania**: nie potrzebujesz już zastrzeżonych publicznych adresów IP w swoich sieciach wirtualnych, aby zabezpieczać zasoby platformy Azure za pośrednictwem zapory adresów IP. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>Ograniczenia

- Funkcja jest dostępna tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Punkty końcowe są włączone w podsieciach skonfigurowanych w sieciach wirtualnych platformy Azure. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- W przypadku usługi Azure SQL punkt końcowy usługi dotyczy tylko ruchu usługi platformy Azure w regionie sieci wirtualnej. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. For more information, see [Azure paired regions](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- Punkt końcowy usługi dla sieci wirtualnej zapewnia tożsamość sieci wirtualnej w usłudze platformy Azure. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- Obecnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP. Dzięki punktom końcowym usługi ruch usługi jest przełączany na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej. To przełączenie umożliwia dostęp do usług bez konieczności stosowania zastrzeżonych publicznych adresów IP w zaporach adresów IP.

   >[!NOTE]
   > Dzięki punktom końcowym usługi źródłowe adresy IP maszyn wirtualnych w podsieci dla ruchu w usłudze mogą przełączyć się z używania publicznych adresów IPv4 na używanie prywatnych adresów IPv4. Istniejące reguły zapory usługi platformy Azure używające publicznych adresów IP platformy Azure przestaną działać po tym przełączeniu. Przed rozpoczęciem konfigurowania punktów końcowych usługi sprawdź, czy reguły zapory usługi platformy Azure zezwalają na ten przełącznik. Może również wystąpić tymczasowe przerwanie ruchu usługi z tej podsieci podczas konfigurowania punktów końcowych usługi. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. If you want to allow traffic from on-premises, you must also allow public (typically, NAT) IP addresses from your on-premises or ExpressRoute. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Zabezpieczanie usług platformy Azure w sieciach wirtualnych](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurowanie

- Configure service endpoints on a subnet in a virtual network. Punkty końcowe współpracują z wystąpieniami obliczeniowymi dowolnego typu uruchomionymi w danej podsieci.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- W przypadku usługi Azure SQL Database sieci wirtualne muszą znajdować się w tym samym regionie, co zasób usługi platformy Azure. Jeśli korzystasz z magazynów GRS i RA-GRS w usłudze Azure Storage, konto podstawowe powinno znajdować się w tym samym regionie, co sieć wirtualna. For all other services, you can secure Azure service resources to virtual networks in any region. 
- Sieć wirtualna, w której konfigurowany jest punkt końcowy, może należeć do tej samej subskrypcji, co zasób usługi platformy Azure, lub innej. Aby uzyskać więcej informacji na temat uprawnień wymaganych do konfigurowania punktów końcowych i zabezpieczania usług platformy Azure, zobacz [Aprowizowanie](#provisioning).
- W przypadku obsługiwanych usług można zabezpieczyć nowe lub istniejące zasoby w sieciach wirtualnych za pomocą punktów końcowych usługi.

### <a name="considerations"></a>Zagadnienia do rozważenia

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. Wszystkie otwarte połączenia TCP z usługą są zamykane podczas tego przełączania. Upewnij się, że żadne krytyczne zadania nie działają podczas włączania lub wyłączania punktu końcowego w usłudze dla podsieci. Upewnij się również, czy aplikacje mogą automatycznie połączyć się z usługami platformy Azure po przełączeniu adresu IP.

  Przełączenie adresu IP ma wpływ tylko na ruch usługi z sieci wirtualnej. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. Jeśli masz istniejące reguły zapory używające publicznych adresów IP platformy Azure dla usług platformy Azure, te reguły przestają działać po przełączeniu na użycie prywatnych adresów sieci wirtualnej.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Sieciowe grupy zabezpieczeń z punktami końcowymi usługi:
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. Aby uzyskać więcej informacji, zobacz [Azure Service tags for NSGs (Tagi usług platformy Azure dla sieciowych grup zabezpieczeń)](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenariusze

- **Równorzędne lub połączone sieci wirtualne albo wiele sieci wirtualnych**: aby zabezpieczyć usługi platformy Azure w wielu podsieciach sieci wirtualnej lub wielu sieciach wirtualnych, możesz niezależnie włączyć punkty końcowe usługi w każdej z tych podsieci i zabezpieczyć zasoby usługi platformy Azure we wszystkich tych podsieciach.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. Następnie możesz zastosować punkty końcowe usługi do podsieci, w której wdrożono sieciowe urządzenie wirtualne, i zabezpieczyć zasób usługi platformy Azure tylko w tej podsieci. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. Zasoby usługi platformy Azure można zabezpieczyć w podsieciach [usługi zarządzanej](virtual-network-for-azure-services.md), konfigurując punkt końcowy usługi w podsieci usługi zarządzanej.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- Weryfikowanie źródłowego adresu IP każdego żądania obsługi w diagnostyce usługi. Wszystkie nowe żądania z punktami końcowymi usługi pokazują pozycję „źródłowy adres IP” dla żądania jako prywatny adres sieci wirtualnej przypisany do klienta wysyłającego żądanie z sieci wirtualnej. Bez punktu końcowego ten adres będzie publicznym adresem IP platformy Azure.
- Wyświetlanie skutecznych tras w dowolnym interfejsie sieciowym w podsieci. Trasa do usługi:
  - Pokazuje dokładniej określoną domyślną trasę do zakresu prefiksów adresów danej usługi.
  - Ma element nextHopType o wartości *VirtualNetworkServiceEndpoint*.
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> Trasy punktu końcowego usługi zastępują trasy BGP lub UDR dla dopasowania prefiksu adresu usługi platformy Azure. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Inicjowanie obsługi administracyjnej

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure należą do różnych subskrypcji, zasoby muszą znajdować się w tej samej dzierżawie usługi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny i limity

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. Zasady punktu końcowego usługi zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Często zadawane pytania

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Następne kroki

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Zasady punktu końcowego usługi dla sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Szablon usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

