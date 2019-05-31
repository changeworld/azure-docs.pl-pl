---
title: Subskrypcja platformy Azure, limity przydziału i ograniczenia
description: Zawiera listę typowych subskrypcji platformy Azure i limity, przydziały i ograniczenia. Ten artykuł zawiera informacje na temat zwiększania limitów wraz z maksymalne wartości.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/17/2019
ms.author: byvinyal
ms.openlocfilehash: 3bdc8580749761242b1fc811b2182d73a2d46328
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238714"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity subskrypcji i usługi Azure, przydziały i ograniczenia
W tym dokumencie przedstawiono niektóre typowe limity Microsoft Azure, które są czasami nazywane limity przydziału. Ten dokument obecnie nie obejmuje wszystkich usług platformy Azure. Wraz z upływem czasu lista zostanie rozwinięte i aktualizowane na pokrycie większej liczby usług.

Aby dowiedzieć się więcej na temat cennika systemu Azure, zobacz [platformy Azure, omówienie cennika](https://azure.microsoft.com/pricing/). Można tam Oszacuj koszty przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/). Możesz także przejść na stronę z cennikiem dla określonej usługi, na przykład [maszyn wirtualnych Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Aby uzyskać porady pomagające w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing/billing-getting-started.md).

> [!NOTE]
> Jeśli chcesz zwiększyć limit lub limitu przydziału powyżej domyślny limit [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](azure-resource-manager/resource-manager-quota-errors.md). Nie można zwiększyć limity powyżej wartości maksymalny limit pokazano w poniższych tabelach. W przypadku żadnej kolumny maksymalny limit zasób nie ma limitów dostosowywalnych.
>
> [Bezpłatna wersja próbna subskrypcji](https://azure.microsoft.com/offers/ms-azr-0044p) nie są uprawnieni do zwiększenia limitu lub przydziału. Jeśli masz [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) subskrypcji. Aby uzyskać więcej informacji, zobacz [Uaktualnij swoją subskrypcję bezpłatnej wersji próbnej platformy Azure do subskrypcji płatności](billing/billing-upgrade-azure-subscription.md) i [subskrypcji bezpłatnej wersji próbnej — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limity i usługa Azure Resource Manager
Teraz jest możliwa do łączenia wielu zasobów platformy Azure w grupie jednego zasobu platformy Azure. Korzystając z grup zasobów, limity, które raz były globalne stają się zarządzane na poziomie regionalnym za pomocą usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Na liście poniżej granic nowej tabeli odzwierciedla żadnych różnic w granicach, korzystając z usługi Azure Resource Manager. Na przykład istnieje **limity subskrypcji** tabeli i **limity subskrypcji — usługi Azure Resource Manager** tabeli. Gdy limit ma zastosowanie do obu scenariuszy, jego jest wyświetlana tylko w pierwszej tabeli. O ile nie wskazano inaczej, limity są globalne we wszystkich regionach.

> [!NOTE]
> Przydziały dla zasobów w grupach zasobów platformy Azure są regionu dostępny dla Twojej subskrypcji, nie subskrypcji, ponieważ przydziały zarządzania usługi. Jako przykładu użyjemy limity przydziału procesorów wirtualnych. Aby zażądać zwiększenia limitu przydziału z obsługą procesorów wirtualnych, należy zdecydować, jak wiele procesorów wirtualnych, które chcesz użyć w jakich regionach. Następnie upewnij się określonego żądania dla limity przydziału procesorów wirtualnych grupy zasobów platformy Azure dotyczące wielkości i regionów, które chcesz. Jeśli musisz użyć 30 procesorów wirtualnych Vcpu w regionie Europa Zachodnia do uruchamiania aplikacji ma specjalne żądanie 30 procesorów wirtualnych Vcpu w regionie Europa Zachodnia. Limit przydziału procesorów wirtualnych nie jest zwiększenie w każdym innym regionie — tylko Europa Zachodnia ma limit przydziału procesorów wirtualnych 30.
> <!-- -->
> W rezultacie zdecyduj, limity przydziału grupy zasobów platformy Azure należy dla obciążenia w dowolnym regionie jeden. Następnie żądać tego kwotę w każdym regionie, w której chcesz wdrożyć. Aby uzyskać pomoc w jak ustalić bieżące limity przydziału dla konkretnych regionów, zobacz [Rozwiązywanie problemów z wdrożeniem](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limity specyficzne dla usługi
* [Usługa Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatyzacja](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [System DNS platformy Azure](#azure-dns-limits)
* [Zaporę platformy Azure](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Usługi Azure Machine Learning](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Tworzenie kopii zapasowych](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Fabryka danych](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Usługa migracji bazy danych](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Usługa drzwi](#azure-front-door-service-limits)
* [Programu Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [Usługa IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Usługa Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Sieć](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [System DNS platformy Azure](#azure-dns-limits)
  * [Usługa Azure drzwi](#azure-front-door-service-limits)
  * [Zaporę platformy Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Publiczny adres IP](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Grupa zasobów](#resource-group-limits)
* [Kontrola dostępu oparta na rolach](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subskrypcja](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Zestawy skalowania maszyn wirtualnych](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limity subskrypcji
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limity subskrypcji — Zarządzanie usługami platformy Azure (klasycznego modelu wdrażania)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity subskrypcji — usługi Azure Resource Manager
Poniższe limity mają zastosowanie, gdy używasz usługi Azure Resource Manager i grupy zasobów platformy Azure. Limity, które nie zostały zmienione za pomocą usługi Azure Resource Manager nie są wyświetlane. Zob. Poprzednia tabela tych limitów.

Aby uzyskać informacje na temat interfejsu API usługi Resource Manager Odczyt i zapis limity, zobacz [żądania ograniczania przepustowości usługi Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grupy zasobów
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limity maszyn wirtualnych
#### <a name="virtual-machines-limits"></a>Limity maszyn wirtualnych
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity maszyn wirtualnych — usługi Azure Resource Manager
Poniższe limity mają zastosowanie, gdy używasz usługi Azure Resource Manager i grupy zasobów platformy Azure. Limity, które nie zostały zmienione za pomocą usługi Azure Resource Manager nie są wyświetlane. Zob. Poprzednia tabela tych limitów.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Udostępnione limity galerii obrazów

Istnieją limity subskrypcji, przez wdrażanie zasobów przy użyciu współdzielonego, galerie obrazów:
- 100 galerie udostępnionego obrazu na subskrypcję na region
- 1000 obrazów definicje na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region

### <a name="virtual-machine-scale-sets-limits"></a>Limity zestawów skalowania maszyn wirtualnych
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity wystąpień kontenerów
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ogranicza rejestru kontenerów
W poniższej tabeli przedstawiono funkcje i limity Basic, Standard i Premium [warstwy usług](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limity usługi Azure Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Limity usługi Machine Learning platformy Azure
Ostatnie wartości dla przydziały obliczeniowego usługi Azure Machine Learning można znaleźć w [strony limitu przydziału usługi Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limity sieci
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limity usługi ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Ogranicza bramy Application Gateway

Poniższa tabela ma zastosowanie do v1, v2, standardowa i jednostki SKU zapory aplikacji sieci Web, chyba że określono inaczej.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limity usługi Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limity usługi Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Limity usługi Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limity zapory platformy Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limity drzwiami frontowymi usługi platformy Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limity przestrzeni dyskowej
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limity dostawcy zasobów magazynu 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limity magazynu obiektów Blob platformy Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limity plików platformy Azure
Aby uzyskać więcej informacji o limitach usługi Azure Files, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limity usługi Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limity magazynu usługi Azure Queue
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limity przestrzeni dyskowej w usłudze Azure Table
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limity dysku maszyny wirtualnej
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Zarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dyski niezarządzane maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limity usługi Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limity usługi App Service
Następujące limity usługi App Service obejmują limity dla aplikacji sieci Web, aplikacje mobilne i aplikacje API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Ograniczenia funkcji
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limity usługi Scheduler
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limity usługi Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limity usługi BizTalk Services
W poniższej tabeli przedstawiono limity usługi Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Usługa Azure Cosmos DB ogranicza
Limity usługi Azure Cosmos DB, zobacz [limity w usłudze Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Dla usługi Azure Database for MySQL ograniczeń, zobacz [ograniczenia w usłudze Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Dla usługi Azure Database for postgresql w warstwie ograniczeń, zobacz [ograniczenia w usłudze Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Limity usługi Azure Search
Warstwy cenowe określać pojemność i limity usługi wyszukiwania. Warstwy obejmują:

* **Bezpłatne** usługą z wieloma dzierżawcami, współużytkowane z innymi subskrybentami systemu Azure, jest przeznaczony dla oceny małych projektów i rozwoju.
* **Podstawowe** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę przy użyciu maksymalnie trzech replik dla obciążeń związanych z zapytaniami o wysokiej dostępności.
* **Standardowa**, która obejmuje S1, S2 i S3 i S3 High Density jest w przypadku większych obciążeń produkcyjnych. W ramach warstwy Standard istnieje wiele poziomów, tak, aby można było wybrać konfiguracji zasobu, który najlepiej odpowiada profilu obciążenia.

**Limity dla subskrypcji**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limity dla usługi wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej o limitach na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytań na sekundę, klucze, żądań i odpowiedzi, zobacz [limitów w usłudze Azure Search usług](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limity usługi Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Ogranicza Content Delivery Network
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limity usług Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limity monitora
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Limity centra powiadomień
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limity centrów zdarzeń
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limity usługi Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limity usługi IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limity IoT Hub Device Provisioning Service
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limity usługi Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limity usługi Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limity Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limity usługi Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limity usługi Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limity usługi Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Limity usługi Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limity usługi platformy Azure Maps
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limity usługi Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limity systemu StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limity usługi log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limity kopii zapasowej
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limity usługi Azure SignalR Service
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limity usługi Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights limity
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limity usługi API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Pamięć podręczna systemu Azure, limity pamięci podręcznej Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limity usługi Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limity usługi Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limity usługi Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limity programu Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limity kontroli dostępu opartej na rolach
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limity bazy danych SQL
Limity bazy danych SQL, zobacz [limity zasobów bazy danych SQL Database dla pojedynczych baz danych](sql-database/sql-database-vcore-resource-limits-single-databases.md), [limity zasobów bazy danych SQL dla pul elastycznych i baz danych w puli](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), i [limity zasobów bazy danych SQL dla wystąpienia zarządzanego](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limity SQL Data Warehouse
Limity SQL Data Warehouse, zobacz [limity zasobów SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Zobacz także
- [Zrozumieć limity platformy Azure i zwiększa](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Maszyna wirtualna i w chmurze rozmiary usługi na platformie Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Rozmiary dla usługi Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
