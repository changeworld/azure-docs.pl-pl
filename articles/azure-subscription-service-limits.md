---
title: Subskrypcja platformy Azure, limity przydziału i ograniczenia
description: Zawiera listę typowych subskrypcji platformy Azure i limity, przydziały i ograniczenia. W tym informacje na temat zwiększania limitów wraz z maksymalne wartości.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 10/19/2018
ms.author: byvinyal
ms.openlocfilehash: c22f3dbc06dd43a31bf43634437c7f377dac8813
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457310"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity subskrypcji i usługi Azure, przydziały i ograniczenia
W tym dokumencie przedstawiono niektóre typowe limity Microsoft Azure, które są czasami nazywane limity przydziału. Ten dokument obecnie nie obejmuje wszystkich usług platformy Azure. Wraz z upływem czasu lista zostaną rozwinięte i zaktualizowana w celu pokrycia więcej platformy.

Odwiedź stronę [omówienie cennika usługi Azure](https://azure.microsoft.com/pricing/) Aby dowiedzieć się więcej na temat cennika systemu Azure. Tam możesz Oszacuj koszty przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) lub odwiedź stronę z cennikiem usługi (na przykład [maszyn wirtualnych Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Aby uzyskać porady pomagające w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing/billing-getting-started.md).

> [!NOTE]
> Jeśli chcesz zwiększyć limit lub powyżej limitu przydziału **domyślny Limit**, [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](azure-resource-manager/resource-manager-quota-errors.md). Nie można zwiększyć limity powyżej **maksymalny Limit** pokazanymi w poniższych tabelach. Jeśli ma nie **maksymalny Limit** kolumny, a następnie zasób nie ma limitów zmienianych.
>
> [Bezpłatna wersja próbna subskrypcji](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu lub przydziału. Jeśli masz [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) subskrypcji. Aby uzyskać więcej informacji, zobacz [uaktualnienia próbnej wersji platformy Azure do płatności](billing/billing-upgrade-azure-subscription.md) i [subskrypcji bezpłatnej wersji próbnej — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Limity i usługa Azure Resource Manager
Teraz istnieje możliwość łączenia wielu zasobów platformy Azure w jednej grupie zasobów platformy Azure. Podczas korzystania z grup zasobów, limity, które raz były globalne stają się zarządzane na poziomie regionalnym za pomocą usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

W obszarze poniżej ograniczenia dodano nową tabelę odzwierciedla żadnych różnic w granicach, korzystając z usługi Azure Resource Manager. Na przykład istnieje **limity subskrypcji** tabeli i **limity subskrypcji — usługi Azure Resource Manager** tabeli. Gdy limit ma zastosowanie do obu scenariuszy, jego jest wyświetlana tylko w pierwszej tabeli. O ile nie wskazano inaczej, limity są globalne we wszystkich regionach.

> [!NOTE]
> Należy podkreślić przydziały dla zasobów w grupach zasobów platformy Azure są regionu dostępny dla Twojej subskrypcji i nie są każdej subskrypcji, ponieważ przydziały zarządzania usługi. Jako przykładu użyjemy limity przydziału procesorów wirtualnych. Jeśli potrzebujesz zażądać zwiększenia limitu przydziału z obsługą procesorów wirtualnych Vcpu, musisz zdecydować, jak wiele procesorów wirtualnych, które chcesz użyć w jakich regionach, a następnie dokonaj określonego żądania dla limity przydziału procesorów wirtualnych grupy zasobów platformy Azure dotyczące wielkości i regionów, które mają. W związku z tym w razie potrzeby na potrzeby uruchamiania aplikacji ma 30 procesorów wirtualnych Vcpu w regionie Europa Zachodnia, należy zażądać specjalnie 30 procesorów wirtualnych Vcpu w regionie Europa Zachodnia. Ale nie ma limit przydziału procesorów wirtualnych rosną z każdym innym regionie — tylko Europa Zachodnia, będzie mieć limit przydziału procesorów wirtualnych 30.
> <!-- -->
> W rezultacie może warto wziąć pod uwagę przy wyborze rozwiązania, które limity przydziału grupy zasobów platformy Azure muszą zostać dla obciążenia w dowolnym regionie, co i zażądać kwota w każdym regionie, w którym rozważane jest wdrożenie. Zobacz [Rozwiązywanie problemów dotyczących wdrożenia](resource-manager-common-deployment-errors.md) Aby uzyskać dodatkową pomoc, odnajdywanie bieżące limity przydziału dla konkretnych regionów.
>
>

## <a name="service-specific-limits"></a>Limity specyficzne dla usługi
* [Usługa Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatyzacja](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Tworzenie kopii zapasowych](#backup-limits)
* [Batch](#batch-limits)
* [Usługa Batch — sztuczna inteligencja](#batch-ai-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Usługa Kubernetes](#kubernetes-service-limits)
* [Fabryka danych](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Usługa migracji bazy danych](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [Zaporę platformy Azure](#azure-firewall-limits)
* [Drzwi](#azure-front-door-service-limits)
* [IoT Hub](#iot-hub-limits)
* [Usługa IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Usługa Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Tożsamość zarządzana](#managed-identity-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Usługi Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Sieć](#networking-limits)
* [Usługa Network Watcher](#network-watcher-limits)
* [Usługa Centrum powiadomień](#notification-hub-service-limits)
* [Grupa zasobów](#resource-group-limits)
* [Kontrola dostępu oparta na rolach](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Wyszukiwanie](#search-limits)
* [Service Bus](#service-bus-limits)
* [SignalR Service](#signalr-service-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [Usługa StorSimple systemu](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subskrypcja](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Zestawy skalowania maszyn wirtualnych](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limity subskrypcji
#### <a name="subscription-limits---azure-service-management-classic-resources"></a>Limity subskrypcji — Zarządzanie usługami platformy Azure (klasycznymi zasobami)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity subskrypcji — usługi Azure Resource Manager
Poniższe limity mają zastosowanie, podczas korzystania z usługi Azure Resource Manager i grup zasobów platformy Azure. Limity, które nie zostały zmienione za pomocą usługi Azure Resource Manager nie są wymienione poniżej. Można znaleźć w poprzednich tabelach te limity.

Aby uzyskać informacje na temat interfejsu API usługi Resource Manager Odczyt i zapis limity, zobacz [żądania ograniczania przepustowości usługi Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grupy zasobów
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limity maszyn wirtualnych
#### <a name="virtual-machine-limits"></a>Limity maszyny wirtualnej
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity maszyn wirtualnych — usługi Azure Resource Manager
Poniższe limity mają zastosowanie, podczas korzystania z usługi Azure Resource Manager i grup zasobów platformy Azure. Limity, które nie zostały zmienione za pomocą usługi Azure Resource Manager nie są wymienione poniżej. Można znaleźć w poprzednich tabelach te limity.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limity Virtual Machine Scale Sets
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity wystąpień kontenerów
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ogranicza rejestru kontenerów
W poniższej tabeli przedstawiono funkcje i limity Basic, Standard i Premium [warstwy usług](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Limity usługi Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limity sieci
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limity sieci
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Ogranicza bramy Application Gateway
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limity usługi Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limity usługi Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limity DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limity zapory platformy Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limity drzwiami frontowymi usługi platformy Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limity przestrzeni dyskowej
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Więcej informacji na temat limitów konta magazynu, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage/common/storage-scalability-targets.md).

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

Zobacz [rozmiarów maszyn wirtualnych](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dodatkowe szczegóły.

#### <a name="managed-virtual-machine-disks"></a>Zarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dyski niezarządzane maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Limity usług w chmurze
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limity usługi App Service
Następujące limity usługi App Service obejmują limity dla aplikacji sieci Web, aplikacje mobilne i aplikacje API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limity usługi Scheduler
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limity usługi Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="batch-ai-limits"></a>Limity usługi Batch AI
[!INCLUDE [azure-batch-ai-limits](../includes/azure-batch-ai-limits.md)]

### <a name="biztalk-services-limits"></a>Limity usługi BizTalk Services
W poniższej tabeli przedstawiono limity usługi Azure Biztalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Usługa Azure Cosmos DB ogranicza
Usługa Azure Cosmos DB jest w skali globalnej bazy danych, w którym przepływności i magazynu mogą być skalowane do obsługi cokolwiek aplikacja wymaga. Jeśli masz jakieś pytania dotyczące skalowania usługi Azure Cosmos DB zapewnia, Wyślij wiadomość e-mail do askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Dla usługi Azure Database for MySQL ograniczeń, zobacz [ograniczenia w usłudze Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Dla usługi Azure Database for postgresql w warstwie ograniczeń, zobacz [ograniczenia w usłudze Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="search-limits"></a>Limity wyszukiwania
Warstwy cenowe określać pojemność i limity usługi wyszukiwania. Warstwy obejmują:

* *Bezpłatne* wielodostępnych współużytkowaną z innymi subskrybentami systemu Azure, przeznaczony do oceny i tworzenia małych projektów.
* *Podstawowe* zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę przy użyciu maksymalnie trzech replik dla obciążeń związanych z zapytaniami o wysokiej dostępności.
* *Standardowa (S1, S2, S3, wysoka gęstość S3)* jest w przypadku większych obciążeń produkcyjnych. W ramach warstwy standard istnieje wiele poziomów, tak, aby można było wybrać konfiguracji zasobu, który najlepiej odpowiada profilu obciążenia.

**Limity dla subskrypcji**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limity dla usługi wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej o limitach na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytań na sekundę, klucze, żądań i odpowiedzi, zobacz [limitów w usłudze Azure Search usług](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limity usługi Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limity usługi CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limity usług Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limity monitora
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limity usługi Centrum powiadomień
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

### <a name="azure-event-grid-limits"></a>Limity usługi Azure Event Grid
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

### <a name="signalr-service-limits"></a>Limity usługi SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limity usługi Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights limity
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limity usługi API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limity usługi Azure Redis Cache
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limity usługi Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limity usługi Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Zarządzane ograniczenia tożsamości
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limity kontroli dostępu opartej na rolach
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limity bazy danych SQL
Limity bazy danych SQL, zobacz [limity zasobów bazy danych SQL dla pojedynczych baz danych](sql-database/sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów bazy danych SQL dla pul elastycznych i baz danych w puli](sql-database/sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="sql-data-warehouse-limits"></a>Limity SQL Data Warehouse
Limity SQL Data Warehouse, zobacz [limity zasobów magazynu danych SQL](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Zobacz także
[Opis limity platformy Azure i zwiększa](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Maszyna wirtualna i rozmiary usług w chmurze dla platformy Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Rozmiary usług Cloud Services](cloud-services/cloud-services-sizes-specs.md)
