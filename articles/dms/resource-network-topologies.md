---
title: Topologii na potrzeby migracji wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Database Migration Service sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, źródłowe i docelowe konfiguracje dla usługi Database Migration Service.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: cfbfd71f75cd3717392f9aa2c020cedda844d774
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803966"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologie sieci na potrzeby migracji wystąpienia zarządzanego Azure SQL DB przy użyciu usługi Azure Database Migration Service
W tym artykule omówiono różne topologie sieci, które Azure Database Migration Service można pracować zapewniając migracji kompleksowe środowisko z lokalnymi serwerami SQL do wystąpienia zarządzanego Azure SQL Database.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Usługa Azure wystąpienia zarządzanego SQL Database skonfigurowane dla obciążeń hybrydowych 
Użyj tej topologii, jeśli Twoja baza danych wystąpienia zarządzanego Azure SQL jest podłączony do sieci lokalnej. To podejście zapewnia najbardziej uproszczone routing w sieci i daje maksymalną przepustowość podczas migracji.

![Topologia sieci w przypadku obciążeń hybrydowych](media\resource-network-topologies\hybrid-workloads.png)

**Wymagania**
- W tym scenariuszu Azure SQL Database Managed Instance i wystąpienie usługi Azure Database Migration Service, które są tworzone w tej samej sieci Wirtualnej platformy Azure, ale używają różnych podsieci.  
- W tym scenariuszu sieci Wirtualnej również jest podłączony do sieci lokalnej przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Wystąpienie usługi Azure SQL Database zarządzane odizolowanych od sieci lokalnej
Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jedną z następujących scenariuszy:
- Azure SQL Database Managed Instance jest odizolowana od połączeń lokalnych, ale wystąpienie usługi Azure Database Migration Service jest podłączony do sieci lokalnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) znajdują się w miejscu, a chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem bazy danych wystąpienia zarządzanego Azure SQL.
- Sieci wirtualne używane dla bazy danych wystąpienia zarządzanego Azure SQL i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologii sieciowej dla wystąpienia zarządzanego odizolowanych od sieci lokalnej](media\resource-network-topologies\mi-isolated-workload.png)

**Wymagania**
- Sieć Wirtualną, która używa usługi Azure Database Migration Service, w tym scenariuszu również muszą być podłączone do sieci lokalnej za pomocą (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurowanie [wirtualne sieci równorzędne sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią Wirtualną użytą do wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migracje do chmury do chmury: udostępnione sieci Wirtualnej

Użyj tej topologii, jeśli źródła programu SQL Server znajduje się w Maszynie wirtualnej platformy Azure i udostępni tej samej sieci Wirtualnej wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service.

![Topologia sieci dla migracji chmury do chmury za pomocą udostępnionej sieci wirtualnej](media\resource-network-topologies\cloud-to-cloud.png)

**Wymagania**
- Nie dodatkowe wymagania.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migracje do chmury w chmurze: izolowane sieci Wirtualnej

Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jedną z następujących scenariuszy:
- Azure SQL Database Managed Instance jest aprowizowany w izolowanej sieci Wirtualnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) znajdują się w miejscu, a chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem bazy danych wystąpienia zarządzanego Azure SQL.
- Sieci wirtualne używane dla wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla migracji chmury do chmury za pomocą izolowanej sieci wirtualnej](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Wymagania**
- Konfigurowanie [wirtualne sieci równorzędne sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią Wirtualną użytą do wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service.


## <a name="see-also"></a>Zobacz też
- [Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej publicznej wersji zapoznawczej, zobacz artykuł [co to jest podgląd usługi migracji bazy danych Azure](dms-overview.md). 