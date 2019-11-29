---
title: Ograniczenia i limity subskrypcji platformy Azure
description: Zawiera listę typowych limitów subskrypcji i usług platformy Azure, przydziałów i ograniczeń. Ten artykuł zawiera informacje o sposobach zwiększania limitów oraz wartości maksymalnych.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.service: cost-management-billing
ms.topic: article
ms.date: 11/18/2019
ms.author: byvinyal
ms.openlocfilehash: 8d538f0267a36cae9f8e97239dc7d3441b9ee374
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555128"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity subskrypcji i usługi Azure, przydziały i ograniczenia

Ten dokument zawiera listę najbardziej typowych limitów Microsoft Azure, które są również czasami nazywane przydziałami. Ten dokument nie obejmuje obecnie wszystkich usług platformy Azure. Wraz z upływem czasu lista zostanie rozszerzona i zaktualizowana w celu objęcia dodatkowych usług.

Aby dowiedzieć się więcej na temat cen platformy Azure, zobacz [Omówienie cen platformy Azure](https://azure.microsoft.com/pricing/). W tym miejscu możesz oszacować swoje koszty za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/). Możesz również przejść do strony szczegóły cennika dla konkretnej usługi, na przykład [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Aby uzyskać porady ułatwiające zarządzanie kosztami, zobacz [zapobieganie nieoczekiwanym kosztom w rozliczeniach i zarządzaniu kosztami platformy Azure](billing/billing-getting-started.md).

> [!NOTE]
> Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](azure-resource-manager/resource-manager-quota-errors.md). Limitów nie można zwiększyć powyżej wartości maksymalnego limitu pokazanej w poniższych tabelach. Jeśli nie ma żadnej kolumny maksymalnego limitu, zasób nie ma dopuszczalnych limitów.
>
> [Bezpłatna subskrypcja wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikuje się do zwiększenia limitu przydziału. Jeśli masz [bezpłatną subskrypcję wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do subskrypcji [płatnej zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Aby uzyskać więcej informacji, zobacz [uaktualnianie subskrypcji bezpłatnej wersji próbnej platformy Azure do subskrypcji płatnej zgodnie z rzeczywistym](billing/billing-upgrade-azure-subscription.md) użyciem i [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limity i Azure Resource Manager

Teraz można połączyć wiele zasobów platformy Azure w jedną grupę zasobów platformy Azure. W przypadku korzystania z grup zasobów limity, które były globalne, są zarządzane na poziomie regionalnym z Azure Resource Manager. Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [omówienie Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Na poniższej liście limitów Nowa tabela odzwierciedla wszelkie różnice w limitach w przypadku używania Azure Resource Manager. Na przykład istnieje tabela **limitów subskrypcji** i **limity subskrypcji — Azure Resource Manager** tabeli. Gdy limit dotyczy obu scenariuszy, jest wyświetlany tylko w pierwszej tabeli. O ile nie wskazano inaczej, limity są globalne dla wszystkich regionów.

> [!NOTE]
> Przydziały zasobów w grupach zasobów platformy Azure są dostępne dla poszczególnych regionów, a nie dla subskrypcji, w ramach których są przydziały zarządzania usługami. Użyjmy przydziałów vCPU jako przykładu. Aby zażądać zwiększenia limitu przydziału z obsługą procesorów wirtualnych vCPU, należy określić liczbę procesorów wirtualnych vCPU, które mają być używane w regionach. Następnie należy wykonać określone żądanie dla grupy zasobów platformy Azure vCPU przydziały dla żądanych ilości i regionów. Jeśli konieczne jest użycie 30 procesorów wirtualnych vCPU w Europie Zachodniej, aby uruchomić aplikację, należy zażądać 30 procesorów wirtualnych vCPU w Europie Zachodniej. Limit przydziału vCPU nie został zwiększony w żadnym innym regionie — tylko Europa Zachodnia ma przydział 30-vCPU.
> <!-- -->
> W związku z tym należy określić, jakie limity przydziału grupy zasobów platformy Azure muszą być dla obciążenia w jednym regionie. Następnie Zażądaj tej kwoty w każdym regionie, w którym chcesz wdrożyć. Aby uzyskać pomoc w ustalaniu bieżących przydziałów dla określonych regionów, zobacz [Rozwiązywanie problemów z wdrażaniem](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Ograniczenia dotyczące usługi

* [Usługa Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automatyzacja](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [usług Azure Cloud Services](#azure-cloud-services-limits)
* [Wyszukiwanie poznawcze platformy Azure](#azure-cognitive-search-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [System DNS platformy Azure](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning](#azure-machine-learning-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Usługa sygnałów platformy Azure](#azure-signalr-service-limits)
* [Tworzenie kopii zapasowych](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Usługa Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Usługa front-drzwi](#azure-front-door-service-limits)
* [Menedżer tożsamości](#identity-manager-limits)
* [Centrala IoT](#iot-central-limits)
* [IoT Hub](#iot-hub-limits)
* [Usługa IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Usługa Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Sieć](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure bastionu](#azure-bastion-limits)
  * [System DNS platformy Azure](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Moduł równoważenia obciążenia](#load-balancer)
  * [Network Watcher](#network-watcher-limits)
  * [Publiczny adres IP](#publicip-address)
  * [Link prywatny](#private-link-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
  * [Wirtualna sieć WAN](#virtual-wan-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Grupa zasobów](#resource-group-limits)
* [Kontrola dostępu oparta na rolach](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Magazyn](#storage-limits)
* [System StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subskrypcja](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Zestawy skalowania maszyn wirtualnych](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limity subskrypcji

#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limity subskrypcji — zarządzanie usługami platformy Azure (klasyczny model wdrażania)

[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity subskrypcji — Azure Resource Manager

W przypadku używania Azure Resource Manager i grup zasobów platformy Azure obowiązują następujące ograniczenia. Nie są wyświetlane limity, które nie uległy zmianie przy użyciu Azure Resource Manager. Te limity można znaleźć w poprzedniej tabeli.

Aby uzyskać informacje na temat limitów odczytu i zapisu interfejsu API Menedżer zasobów, zobacz [ograniczanie Menedżer zasobów żądań](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grupy zasobów

[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limity Virtual Machines

#### <a name="virtual-machines-limits"></a>Limity Virtual Machines

[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity Virtual Machines — Azure Resource Manager

W przypadku używania Azure Resource Manager i grup zasobów platformy Azure obowiązują następujące ograniczenia.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Limity galerii obrazów udostępnionych

Istnieją limity dla każdej subskrypcji dotyczące wdrażania zasobów przy użyciu udostępnionych galerii obrazów:

- 100 udostępnione Galerie obrazów na subskrypcję na region
- 1 000 definicji obrazów na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region

### <a name="virtual-machine-scale-sets-limits"></a>Limity zestawów skalowania maszyn wirtualnych

[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity Container Instances

[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limity Container Registry

W poniższej tabeli przedstawiono funkcje i limity [warstw usług](./container-registry/container-registry-skus.md)podstawowa, standardowa i Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limity usługi Azure Kubernetes

[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Limity Azure Machine Learning

Najnowsze wartości przydziałów obliczeń Azure Machine Learning można znaleźć na [stronie przydziału Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limity sieci

[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limity ExpressRoute

[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Limity wirtualnej sieci WAN

[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Limity Application Gateway

W poniższej tabeli przedstawiono jednostki SKU V1, v2, standard i WAF, chyba że określono inaczej.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limity Network Watcher

[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Limity linków prywatnych

[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Limity Traffic Manager

[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Limity usługi Azure bastionu

[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Limity Azure DNS

[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limity zapory platformy Azure

[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limity usługi Azure Front-drzwiczk

[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limity magazynu

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limity dostawcy zasobów magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limity magazynu obiektów blob platformy Azure

[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limity Azure Files

Aby uzyskać więcej informacji na temat limitów Azure Files, zobacz [Azure Files cele dotyczące skalowalności i wydajności](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limity Azure File Sync

[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limity magazynu kolejki platformy Azure

[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limity magazynu tabel platformy Azure

[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limity dysku maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Zarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Niezarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limity Cloud Services platformy Azure

[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Limity Cognitive Services platformy Azure

[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Limity App Service

Poniższe App Service limity obejmują limity dla Web Apps, Mobile Apps i API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Limity funkcji

[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limity harmonogramu

[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limity partii

[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limity BizTalk Services

W poniższej tabeli przedstawiono limity dla usługi Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limity Azure Cosmos DB

Aby uzyskać Azure Cosmos DB limitów, zobacz [limity w Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Aby uzyskać Azure Database for MySQL limitów, zobacz [ograniczenia w Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Aby uzyskać Azure Database for PostgreSQL limitów, zobacz [ograniczenia w Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Limity Wyszukiwanie poznawcze platformy Azure

Warstwy cenowe określają pojemność i limity usługi wyszukiwania. Warstwy obejmują:

* **Bezpłatna** usługa wielodostępna, współdzielona z innymi subskrybentami platformy Azure, jest przeznaczona do oceny i małych projektów deweloperskich.
* Funkcja **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych w mniejszej skali, z maksymalnie trzema replikami dla obciążeń zapytań o wysokiej dostępności.
* **Standard**, który obejmuje wysoką gęstość S1, S2, S3 i S3, jest przeznaczony dla większych obciążeń produkcyjnych. W warstwie Standardowa istnieje wiele poziomów, dzięki czemu można wybrać konfigurację zasobów najlepiej zgodną z Twoim profilem obciążenia.

**Limity na subskrypcję**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limity dla usługi wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej na temat limitów na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytania na sekundę, klucze, żądania i odpowiedzi, zobacz [limity usługi w usłudze Azure wyszukiwanie poznawcze](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limity Media Services

[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limity Content Delivery Network

[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limity Mobile Services

[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Limity Azure Monitor

#### <a name="alerts"></a>Alerty

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Grupy akcji

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Rejestruj zapytania i język

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Limity Notification Hubs

[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limity Event Hubs

[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limity Service Bus

[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-central-limits"></a>Limity IoT Central
[!INCLUDE [iot-central-limits](../includes/iot-central-limits.md)]

### <a name="iot-hub-limits"></a>Limity IoT Hub

[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limity IoT Hub Device Provisioning Service

[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limity Data Factory

[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limity Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limity Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limity Database Migration Service

[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limity Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limity Active Directory

[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Limity Event Grid

[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limity Azure Maps

[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limity Azure Policy

[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limity systemu StorSimple

[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Limity kopii zapasowych

[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limity usługi Azure sygnalizujące

[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limity usługi Site Recovery

[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Limity API Management

[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Pamięć podręczna systemu Azure dla limitów Redis

[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limity Key Vault

[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limity Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limity automatyzacji

[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limity programu Identity Manager

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limity kontroli dostępu opartej na rolach

[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limity SQL Database

Aby uzyskać SQL Database limitów, zobacz [SQL Database limitów zasobów dla pojedynczych baz danych](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database limitów zasobów dla pul elastycznych i baz danych w puli](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), a następnie [limity zasobów SQL Database dla wystąpień zarządzanych](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limity SQL Data Warehouse

Aby uzyskać SQL Data Warehouse limitów, zobacz [SQL Data Warehouse limitów zasobów](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Zobacz także

* [Informacje o limitach i ograniczeniach platformy Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rozmiary dla Cloud Services platformy Azure](cloud-services/cloud-services-sizes-specs.md)
