---
title: Topologii na potrzeby migracji wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Database Migration Service sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, źródłowe i docelowe konfiguracje dla usługi Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808426"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie sieci na potrzeby migracji wystąpienia zarządzanego Azure SQL DB przy użyciu usługi Azure Database Migration Service

W tym artykule omówiono różne topologie sieci, które Azure Database Migration Service można pracować zapewniając migracji kompleksowe środowisko z lokalnymi serwerami SQL do wystąpienia zarządzanego Azure SQL Database.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Usługa Azure wystąpienia zarządzanego SQL Database skonfigurowane dla obciążeń hybrydowych 

Użyj tej topologii, jeśli Twoja baza danych wystąpienia zarządzanego Azure SQL jest podłączony do sieci lokalnej. To podejście zapewnia najbardziej uproszczone routing w sieci i daje maksymalną przepustowość podczas migracji.

![Topologia sieci w przypadku obciążeń hybrydowych](media/resource-network-topologies/hybrid-workloads.png)

**Wymagania**

- W tym scenariuszu wystąpienia usługi Azure SQL Database, zarządzanego i wystąpienie usługi Azure Database Migration Service, które są tworzone w tej samej sieci wirtualnej platformy Azure, ale używają różnych podsieci.  
- W tym scenariuszu sieci wirtualnej również jest podłączony do sieci lokalnej przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Wystąpienie usługi Azure SQL Database zarządzane odizolowanych od sieci lokalnej

Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jedną z następujących scenariuszy:

- Wystąpienie zarządzane usługi Azure SQL Database jest odizolowana od połączeń lokalnych, ale wystąpienie usługi Azure Database Migration Service jest podłączony do sieci lokalnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) znajdują się w miejscu, a chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem wystąpienia zarządzanego usługi Azure SQL Database.
- Sieci wirtualne, do zastosowania w przypadku wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologii sieciowej dla wystąpienia zarządzanego odizolowanych od sieci lokalnej](media/resource-network-topologies/mi-isolated-workload.png)

**Wymagania**

- Sieć wirtualną, która używa usługi Azure Database Migration Service, w tym scenariuszu również muszą być podłączone do sieci lokalnej za pomocą (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurowanie [wirtualne sieci równorzędne sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną użytą do usługi Azure SQL Database zarządzane wystąpienia i Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migracje do chmury do chmury: udostępnione sieci wirtualnej

Użyj tej topologii, jeśli źródła programu SQL Server znajduje się w Maszynie wirtualnej platformy Azure i udostępni tej samej sieci Wirtualnej wystąpienia usługi Azure SQL Database, zarządzanego i Azure Database Migration Service.

![Topologia sieci dla migracji do chmury do chmury za pomocą udostępnionej sieci wirtualnej](media/resource-network-topologies/cloud-to-cloud.png)

**Wymagania**

- Nie dodatkowe wymagania.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migracje do chmury w chmurze: izolowane sieci wirtualnej

Użyj tej topologii sieci, jeśli dane środowisko wymaga co najmniej jedną z następujących scenariuszy:

- Wystąpienie zarządzane usługi Azure SQL Database są aprowizowane w izolowanej sieci wirtualnej.
- Jeśli zasady kontroli dostępu na podstawie ról (RBAC) znajdują się w miejscu, a chcesz ograniczyć użytkownikom uzyskiwanie dostępu do tej samej subskrypcji, który jest hostem wystąpienia zarządzanego usługi Azure SQL Database.
- Sieci wirtualne używane dla wystąpienia zarządzanego Azure SQL Database i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla migracji do chmury do chmury za pomocą izolowanej sieci wirtualnej](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Wymagania**

- Konfigurowanie [wirtualne sieci równorzędne sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną użytą do usługi Azure SQL Database zarządzane wystąpienia i Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| **NAZWA**   | **PORT** | **PROTOCOL** | **ŹRÓDŁO** | **MIEJSCE DOCELOWE** | **AKCJA** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Dowolne      | Dowolne          | DMS SUBNET | Dowolne             | Zezwalaj      |

## <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| **NAZWA**                  | **PORT**                                              | **PROTOCOL** | **ŹRÓDŁO** | **MIEJSCE DOCELOWE**           | **AKCJA** | **Przyczyna reguły**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| zarządzanie                | 443,9354                                              | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | Komunikacja płaszczyzny zarządzania za pośrednictwem usługi service bus i usługi Azure blob storage. <br/>(Włączenie komunikacji równorzędnej firmy Microsoft, nie trzeba tej reguły.)                                                             |
| Diagnostyka               | 12000                                                 | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | Usługa DMS używa tej reguły do zbierania informacji diagnostycznych w celu rozwiązywania problemów.                                                                                                                      |
| Źródło programu SQL server         | 1433 (lub port TCP IP, który nasłuchuje program SQL Server) | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Połączenia źródła programu SQL Server z poziomu usługi DMS <br/>(W przypadku połączenia lokacja lokacja może nie potrzebujesz tej reguły.)                                                                                       |
| SQL Server, nazwane wystąpienie | 1434                                                  | UDP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Wystąpienie źródła łączności z usługi DMS nazwane programu SQL Server <br/>(W przypadku połączenia lokacja lokacja może nie potrzebujesz tej reguły.)                                                                        |
| Udział SMB                 | 445                                                   | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Udział sieciowy SMB przez usługę DMS do przechowywania kopii zapasowej plików bazy danych dla migracji do wystąpienia Zarządzanego usługi Azure SQL Database i serwerami programu SQL Server na maszynie Wirtualnej platformy Azure <br/>(W przypadku połączenia lokacja lokacja może nie potrzebujesz tej reguły). |
| DMS_subnet                | Dowolne                                                   | Dowolne          | Dowolne        | DMS_Subnet                | Zezwalaj      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zobacz także

- [Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure Database Migration Service, zobacz artykuł [co to jest usługa Azure Database Migration Service?](dms-overview.md).
- Aby uzyskać aktualne informacje o dostępności regionalnej usługi Azure Database Migration Service, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) strony.
