---
title: Sieci topologii dla migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się konfiguracje źródłowa i docelowa usługa migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9fcee103854209016d73e29b598c9f33d35c4b6c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316871"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologie sieci migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure
W tym artykule opisano różne topologie sieci, które usługa migracji bazy danych Azure może współpracować z zapewnienie obsługi kompleksowe migracji z lokalnymi serwerami SQL do wystąpienia zarządzane bazy danych SQL Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure wystąpienia bazy danych SQL zarządzane skonfigurowane dla obciążeń hybrydowego 
Jeśli Twoje Azure wystąpienia bazy danych SQL zarządzanych jest podłączony do sieci lokalnej, należy użyć tej topologii. To rozwiązanie zapewnia najbardziej uproszczony routingu sieciowego i zwraca maksymalną przepustowość podczas migracji.

![Topologia sieci dla obciążeń hybrydowego](media\resource-network-topologies\hybrid-workloads.png)

**Wymagania**
- W tym scenariuszu Azure wystąpienia bazy danych SQL zarządzane i wystąpienie usługi migracji bazy danych Azure są tworzone w tej samej sieci Wirtualnej Azure, ale korzystają z różnych podsieci.  
- W tym scenariuszu sieci Wirtualnej jest również połączona z siecią lokalną przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure wystąpienia bazy danych SQL zarządzane odizolowanych od sieci lokalnej
Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jednego z następujących scenariuszy:
- Azure SQL bazy danych zarządzanych wystąpienie jest odizolowana od łączności z lokalnymi, ale wystąpienie usługi migracji bazy danych Azure jest podłączony do sieci lokalnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) są stosowane i chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem Azure wystąpienia bazy danych SQL zarządzane.
- Sieci wirtualne używane dla usługi Azure SQL bazy danych zarządzanych wystąpienia i usługa Azure bazy danych migracji znajdują się w różnych subskrypcji.

![Topologia sieci dla wystąpienia zarządzane odizolowanych od sieci lokalnej](media\resource-network-topologies\mi-isolated-workload.png)

**Wymagania**
- Sieć Wirtualną, która korzysta z usługi migracji bazy danych Azure w tym scenariuszu należy również połączona z siecią lokalną przy użyciu (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurowanie [sieć Wirtualną sieć równorzędna](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między sieciami Wirtualnymi, używany do wystąpienia zarządzane bazy danych SQL Azure i usługa Azure bazy danych migracji.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migracje chmury z chmurą: udostępniony sieci Wirtualnej

Użyj tej topologii, jeśli źródło programu SQL Server znajduje się w maszynie Wirtualnej platformy Azure i udostępnia tej samej sieci Wirtualnej wystąpienia zarządzane bazy danych SQL Azure i usługi migracji bazy danych Azure.

![Topologia sieci dla migracji w chmurze z chmurą z udostępnionym sieci wirtualnej](media\resource-network-topologies\cloud-to-cloud.png)

**Wymagania**
- Nie dodatkowe wymagania.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Chmury w celu migracji chmury: izolowane sieci Wirtualnej

Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jednego z następujących scenariuszy:
- Azure wystąpienia bazy danych SQL zarządzanych jest obsługiwana w izolowanej sieci Wirtualnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) są stosowane i chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem Azure wystąpienia bazy danych SQL zarządzane.
- Sieci wirtualne używane dla wystąpienia zarządzane bazy danych SQL Azure i usługa Azure migracji bazy danych znajdują się w różnych subskrypcji.

![Topologia sieci dla migracji w chmurze z chmurą z izolowanej sieci wirtualnej](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Wymagania**
- Konfigurowanie [sieć Wirtualną sieć równorzędna](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między sieciami Wirtualnymi, używany do wystąpienia zarządzane bazy danych SQL Azure i usługa Azure bazy danych migracji.


## <a name="see-also"></a>Zobacz też
- [Migrowanie serwera SQL do zarządzanego wystąpienia bazy danych Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Przegląd wymagań wstępnych dotyczących używania usługi Azure bazy danych migracji](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Kolejne kroki
Omówienie usługi migracji bazy danych Azure i regionalnych dostępność w publicznej wersji zapoznawczej, zobacz artykuł [co to jest podgląd z migracji bazy danych Azure](dms-overview.md). 