---
title: Limity subskrypcji platformy Azure i przydziały | Dokumentacja firmy Microsoft
description: Zawiera listę typowych subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia. W tym informacje na temat zwiększania limitów wraz z wartości maksymalnej.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: byvinyal
ms.openlocfilehash: 9e4ea16193824e63298b19d4401c110385fa7feb
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700196"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity subskrypcji i usługi Azure, przydziały i ograniczenia
Ten dokument przedstawia niektóre z najczęściej limity Microsoft Azure, nazywane również przydziałów. Ten dokument obecnie nie obejmuje wszystkich usług platformy Azure. Wraz z upływem czasu listy zostanie rozwinięty, a zaktualizowane, aby pokrywał się z platformy.

Odwiedź stronę [omówienie cennik usługi Azure](https://azure.microsoft.com/pricing/) Aby dowiedzieć się więcej o cenach systemu Azure. Istnieje, można oszacować koszty przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) lub poprzez odwiedzenie szczegóły stronie dotyczącej cen usługi (na przykład [maszyn wirtualnych systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Aby uzyskać wskazówki pomagające w zarządzaniu kosztów, zobacz [uniknąć kosztów nieoczekiwany rozliczenia Azure i kosztów zarządzania](billing/billing-getting-started.md).

> [!NOTE]
> Jeśli chcesz podnieść limit lub powyżej limitu przydziału **domyślny Limit**, [otwarcia żądania pomocy technicznej online klienta bez dodatkowych opłat](azure-resource-manager/resource-manager-quota-errors.md). Nie można zgłosić limity powyżej **maksymalny Limit** wartość pokazana w poniższych tabelach. W przypadku nie **maksymalny Limit** kolumny, a następnie zasób nie ma regulowany limity.
>
> [W warstwie bezpłatna wersja próbna subskrypcji](https://azure.microsoft.com/offers/ms-azr-0044p) nie są uprawnione do zwiększenia limitu lub przydziału. Jeśli masz [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), można uaktualnić do [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) subskrypcji. Aby uzyskać więcej informacji, zobacz [uaktualnienia bezpłatna wersja próbna platformy Azure na płatność za rzeczywiste użycie](billing/billing-upgrade-azure-subscription.md) i [subskrypcji bezpłatnej wersji próbnej — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Limity i usługa Azure Resource Manager
Obecnie istnieje możliwość łączenia wielu zasobów platformy Azure w jednej grupy zasobów platformy Azure. Podczas korzystania z grup zasobów, ograniczeń, które raz były globalne stanie zarządzaną na poziomie regionalnym z Menedżerem zasobów Azure. Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

W obszarze poniżej ograniczenia dodano nową tabelę odzwierciedlają różnice w granicach, korzystając z usługi Azure Resource Manager. Na przykład istnieje **limity subskrypcji** tabeli i **limity subskrypcji — usługi Azure Resource Manager** tabeli. Gdy limit dotyczy oba scenariusze, jego jest wyświetlana tylko w pierwszej tabeli. O ile nie wskazano inaczej, limity są globalne we wszystkich regionach.

> [!NOTE]
> Należy wyróżnić przydziałów dla zasobów w grupach zasobów platformy Azure są na regionem dostępny dla Twojej subskrypcji i nie są na subskrypcji, jak przydziały usługi zarządzania. Na przykład użyjmy vCPU przydziałów. Jeśli potrzebujesz zażądać zwiększenia limitu przydziału o obsługę Vcpu, należy zdecydować, jak wiele Vcpu, które chcesz użyć, w których regionach, a następnie wprowadź określone żądanie dla grupy zasobów platformy Azure vCPU przydziały dla kwoty i regionów, które mają. W związku z tym jeśli musisz użyć 30 Vcpu w Europie zachodnie do uruchamiania aplikacji istnieje, należy w szczególności zażądać 30 Vcpu w Europa Zachodnia. Ale nie ma limit przydziału vCPU zwiększyć w innym regionie — tylko Europa Zachodnia ma limit przydziału 30 vCPU.
> <!-- -->
> W związku z tym może warto wziąć pod uwagę przy wyborze co przydziałami grupy zasobów platformy Azure musi być dla obciążenia w dowolnym regionie, co i żądania wielkość w każdym regionie, w którym rozważane jest wdrożenie. Zobacz [Rozwiązywanie problemów dotyczących wdrożenia](resource-manager-common-deployment-errors.md) Aby uzyskać dodatkową pomoc odnajdywania bieżącego przydziałami dla konkretnych regionów.
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
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Tworzenie kopii zapasowych](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Usługa Kubernetes](#container-service-aks-limits)
* [Fabryka danych](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Usługa migracji bazy danych](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Usługa IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Usługa Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Zarządzane tożsamości](#managed-identity-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Usługi mobilne](#mobile-services-limits)
* [Monitorowanie](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Sieć](#networking-limits)
* [Obserwatora sieciowego](#network-watcher-limits)
* [Usługa Centrum powiadomień](#notification-hub-service-limits)
* [Grupa zasobów](#resource-group-limits)
* [Kontrola dostępu oparta na rolach](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Wyszukiwanie](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subskrypcja](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Zestawy skalowania maszyny wirtualnej](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limity subskrypcji
#### <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity subskrypcji — usługi Azure Resource Manager
Następujące ograniczenia mają zastosowanie podczas korzystania z usługi Azure Resource Manager i grupy zasobów platformy Azure. Ograniczenia, które nie zostały zmienione z usługi Azure Resource Manager nie są wymienione poniżej. Można znaleźć w poprzedniej tabeli tych ograniczeń.

Aby uzyskać informacje na temat obsługi limity żądań Resource Manager, zobacz [ograniczania Resource Manager zażąda](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grupy zasobów
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limity maszyny wirtualne
#### <a name="virtual-machine-limits"></a>Limity maszyny wirtualnej
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Maszyny wirtualne limity — usługi Azure Resource Manager
Następujące ograniczenia mają zastosowanie podczas korzystania z usługi Azure Resource Manager i grupy zasobów platformy Azure. Ograniczenia, które nie zostały zmienione z usługi Azure Resource Manager nie są wymienione poniżej. Można znaleźć w poprzedniej tabeli tych ograniczeń.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limity zestawy skalowania maszyny wirtualnej
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity wystąpień kontenera
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limity rejestru kontenera
W poniższej tabeli przedstawiono funkcje i limity podstawowa, standardowa i Premium [warstw usług](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Ograniczenia usługi Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limity sieci
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limity sieci
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Ogranicza bramy aplikacji
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Ogranicza obserwatora sieciowego
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limity Menedżera ruchu
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limity DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limity magazynu
Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limity magazynu obiektów Blob platformy Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limity plików platformy Azure
Aby uzyskać więcej informacji na temat ograniczeń usługi pliki Azure, zobacz [elementy docelowe skalowalności i wydajności usługi pliki Azure](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limity synchronizacji plików platformy Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limity magazynu kolejek Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limity magazynu tabel platformy Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limity dysku maszyny wirtualnej
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Zobacz [rozmiarów maszyn wirtualnych](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dodatkowe szczegóły.

#### <a name="managed-virtual-machine-disks"></a>Dyski zarządzane maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dyski maszyny wirtualnej niezarządzanej

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Limity usługi w chmurze
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Ograniczenia usługi aplikacji
Następujące limity usługi App Service obejmują ograniczenia dla aplikacji sieci Web, aplikacje mobilne, aplikacje API Apps i Logic Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limity harmonogramu
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limity partii
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limity usługi BizTalk Services
W poniższej tabeli przedstawiono limity dotyczące usług Biztalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Ogranicza Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos jest skali globalnej bazy danych, w którym przepływność i magazyn mogą być skalowane do obsługi, niezależnie od aplikacja wymaga. Jeśli masz pytania dotyczące skalowania bazy danych rozwiązania Cosmos Azure zapewnia, Wyślij wiadomość e-mail na adres askcosmosdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Limity usługi Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Limity wyszukiwania
Warstw cenowych określać pojemność i limity usługi wyszukiwania. Warstwy obejmują:

* *Bezpłatne* usługę wielodostępną, współużytkowana z innymi subskrybenci platformy Azure, przeznaczonych do oceny i małych tworzenia projektów.
* *Podstawowe* zawiera dedykowany zasobów obliczeniowych dla obciążeń produkcyjnych na mniejszą skalę, z maksymalnie trzech replik dla obciążeń zapytania wysokiej dostępności.
* *Standardowa (S1, S2, S3, o wysokiej gęstości S3)* jest przeznaczony dla większych obciążeń produkcyjnych. Wiele poziomów istnieje w ramach planu standard, w którym można wybrać konfiguracji zasobu, który najlepiej odpowiada Twoim profilu obciążenia.

**Limity dla subskrypcji**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limity dla usługi wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej na temat limitów na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytania na sekundę, klucze żądań i odpowiedzi, zobacz [usługi limity w usłudze Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limity usługi Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limity CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limity usług Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limity monitora
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Ograniczenia usługi Centrum powiadomień
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limity centra zdarzeń
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limity usługi Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Centrum IoT limitami
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limity inicjowania obsługi usługi IoT Hub urządzeń
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limity fabryki danych
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Ogranicza usługi Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limity usługi Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Ograniczenia usługi bazy danych migracji
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Ogranicza usługi analiza strumienia
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Ogranicza usługi Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Ogranicza Azure siatki zdarzeń
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure limitów mapy
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>Limity systemu StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Ogranicza analizy dzienników
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limity kopii zapasowej
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limity usługi Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights limity
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Zarządzanie interfejsami API limity
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limity pamięci podręcznej Redis Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limity magazynu kluczy
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limity automatyzacji
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Limity tożsamości zarządzanych
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limity kontroli dostępu opartej na rolach
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limity bazy danych SQL
Limitów bazy danych SQL, zobacz [limity zasobów bazy danych SQL](sql-database/sql-database-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limity magazynu danych SQL
Limitów SQL Data Warehouse, zobacz [limity zasobów magazynu danych SQL](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Zobacz także
[Opis ograniczeń Azure i zwiększa](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Maszyny wirtualnej i rozmiary usługi chmury dla platformy Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Rozmiary dla usług w chmurze](cloud-services/cloud-services-sizes-specs.md)
