---
title: Topologie sieci dla migracji wystąpienia zarządzanego sql
titleSuffix: Azure Database Migration Service
description: Poznaj konfiguracje źródłowe i docelowe dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254955"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie sieci dla migracji wystąpienia zarządzanego usługi Azure SQL DB przy użyciu usługi migracji bazy danych Azure

W tym artykule omówiono różne topologie sieci, z którymi usługa migracji bazy danych Azure może pracować w celu zapewnienia kompleksowego środowiska migracji z lokalnych serwerów SQL do wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Wystąpienie zarządzanego usługi Azure SQL Database skonfigurowane dla obciążeń hybrydowych 

Użyj tej topologii, jeśli wystąpienie zarządzanej bazy danych SQL platformy Azure jest połączone z siecią lokalną. Takie podejście zapewnia najbardziej uproszczony routing sieci i zapewnia maksymalną przepływność danych podczas migracji.

![Topologia sieci dla obciążeń hybrydowych](media/resource-network-topologies/hybrid-workloads.png)

**Wymagania**

- W tym scenariuszu wystąpienie zarządzanej bazy danych SQL platformy Azure i wystąpienie usługi migracji bazy danych Azure są tworzone w tej samej sieci wirtualnej platformy Microsoft Azure, ale używają różnych podsieci.  
- Sieć wirtualna używana w tym scenariuszu jest również połączona z siecią lokalną za pomocą usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Wystąpienie zarządzanego usługi Azure SQL Database odizolowane od sieci lokalnej

Użyj tej topologii sieci, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Wystąpienie zarządzanej usługi Azure SQL Database jest izolowane od łączności lokalnej, ale wystąpienie usługi migracji bazy danych Azure jest połączone z siecią lokalną.
- Jeśli zasady kontroli dostępu opartej na rolach (RBAC) są w miejscu i należy ograniczyć użytkowników do uzyskiwania dostępu do tej samej subskrypcji, która jest gospodarzem wystąpienia zarządzanego usługi Azure SQL Database.
- Sieci wirtualne używane dla wystąpienia zarządzanego bazy danych AZURE i usługi migracji bazy danych Azure są w różnych subskrypcjach.

![Topologia sieci dla wystąpienia zarządzanego odizolowana od sieci lokalnej](media/resource-network-topologies/mi-isolated-workload.png)

**Wymagania**

- Sieć wirtualna używana przez usługę Azure Database Migration Service w tym scenariuszu musi byćhttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) również połączona z siecią lokalną przy użyciu sieci VPN (lub [sieci VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
- Konfigurowanie [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla wystąpienia zarządzanego usługi Azure SQL Database i usługi migracji bazy danych Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migracje między chmurami a chmurą: współużytkowana sieć wirtualna

Użyj tej topologii, jeśli źródłowy program SQL Server jest hostowany w maszynie Wirtualnej platformy Azure i współużytkuje tę samą sieć wirtualną z wystąpieniem zarządzanym bazy danych SQL platformy Azure i usługą migracji bazy danych Azure.

![Topologia sieci dla migracji między chmurami a chmurą za pomocą udostępnionej sieci wirtualnej](media/resource-network-topologies/cloud-to-cloud.png)

**Wymagania**

- Brak dodatkowych wymagań.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migracje z chmury do chmury: izolowana sieć wirtualna

Użyj tej topologii sieci, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Wystąpienie zarządzanej usługi Azure SQL Database jest aprowidzone w izolowanej sieci wirtualnej.
- Jeśli zasady kontroli dostępu opartej na rolach (RBAC) są w miejscu i należy ograniczyć użytkowników do uzyskiwania dostępu do tej samej subskrypcji, która jest gospodarzem wystąpienia zarządzanego usługi Azure SQL Database.
- Sieci wirtualne używane dla wystąpienia zarządzanego usługi Azure SQL Database i usługi migracji bazy danych Azure są w różnych subskrypcjach.

![Topologia sieci dla migracji między chmurami z izolowanymi sieciami wirtualnymi](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Wymagania**

- Konfigurowanie [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla wystąpienia zarządzanego usługi Azure SQL Database i usługi migracji bazy danych Azure.

## <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| **NAZWA**   | **Portu** | **Protokół** | **Źródła** | **Docelowy** | **Działania** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Dowolne      | Dowolne          | PODSIEĆ DMS | Dowolne             | Zezwalaj      |

## <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| **NAZWA**                  | **Portu**                                              | **Protokół** | **Źródła** | **Docelowy**           | **Działania** | **Powód reguły**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| zarządzanie                | 443,9354                                              | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | Komunikacja płaszczyzny zarządzania za pośrednictwem usługi Service Bus i magazynu obiektów blob platformy Azure. <br/>(Jeśli komunikacja równorzędna firmy Microsoft jest włączona, ta reguła może nie być potrzebna).                                                             |
| Diagnostyka               | 12000                                                 | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | Usługa DMS używa tej reguły do zbierania informacji diagnostycznych do celów rozwiązywania problemów.                                                                                                                      |
| Serwer źródłowy SQL         | 1433 (lub port IP TCP, który sql server nasłuchuje) | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Łączność źródłową programu SQL Server z dms <br/>(Jeśli masz łączność lokacja-lokacja, ta reguła może nie być potrzebna).                                                                                       |
| Wystąpienie o nazwie SQL Server | 1434                                                  | UDP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Łączność źródła wystąpienia o nazwie programu SQL Server z systemem DMS <br/>(Jeśli masz łączność lokacja-lokacja, ta reguła może nie być potrzebna).                                                                        |
| Udział SMB                 | 445                                                   | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Udział sieciowy SMB dla DMS do przechowywania plików kopii zapasowych bazy danych do migracji do serwerów MI i SQL usługi Azure SQL na maszynie Wirtualnej Platformy Azure <br/>(Jeśli masz łączność lokacja-lokacja, ta reguła może nie być potrzebna). |
| DMS_subnet                | Dowolne                                                   | Dowolne          | Dowolne        | DMS_Subnet                | Zezwalaj      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zobacz też

- [Migrowanie wystąpienia zarządzanego programu SQL Server do bazy danych SQL Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Omówienie wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi migracji bazy danych platformy Azure, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure?](dms-overview.md).
- Aby uzyskać aktualne informacje na temat regionalnej dostępności usługi migracji bazy danych platformy Azure, zobacz [produkty dostępne dla regionu](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) strony.
