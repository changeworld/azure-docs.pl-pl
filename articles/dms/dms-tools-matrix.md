---
title: Macierz narzędzi Azure Database Migration Service Tools
description: Dowiedz się więcej na temat usług i narzędzi dostępnych do migrowania baz danych i obsługi różnych faz procesu migracji.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 6259f365edeb3c3d94646a4b2ae019d0776dcc23
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860847"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Usługi i narzędzia dostępne dla scenariuszy migracji danych

Ten artykuł zawiera macierz usług i narzędzi firmy Microsoft i innych firm, które pomagają w różnych scenariuszach migracji bazy danych i danych oraz zadaniach specjalnych.

W poniższych tabelach zidentyfikowano usługi i narzędzia, których można użyć do pomyślnego zaplanowania migracji danych i ukończenia różnych faz.

> [!NOTE]
> W poniższych tabelach elementy oznaczone gwiazdką (*) reprezentują narzędzia innych firm.

## <a name="business-justification-phase"></a>Faza uzasadnienia biznesowego

| Źródło | Cel | Odnajdywanie<br/>Spis | Cel i jednostka SKU<br/>zaleca | TCO/zwrot z inwestycji i<br/>Analiza biznesowa |
| --- | --- | --- | --- | --- |
| SQL Server | Baza danych SQL Azure | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Baza danych Azure SQL | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Maszyna wirtualna Azure SQL | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | MAGAZYN DANYCH SQL | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) |  | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL RDS | Baza danych Azure SQL, MI, maszyna wirtualna |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Baza danych Azure SQL, MI, maszyna wirtualna | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | MAGAZYN DANYCH SQL | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer | [Zestaw narzędzi mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Baza danych Azure SQL, MI, maszyna wirtualna | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/) | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB dla MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB dla MySQL |  |  | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL RDS | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |  | [Kalkulator TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Baza danych Azure SQL, MI, maszyna wirtualna | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Dostęp | Baza danych Azure SQL, MI, maszyna wirtualna | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase — SAP ASE | Baza danych Azure SQL, MI, maszyna wirtualna | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Baza danych Azure SQL, MI, maszyna wirtualna |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Faza przed migracją

| Źródło | Cel | Dostęp do danych aplikacji<br/>Ocena warstwy | baza danych<br/>Ocena | Wydajność<br/>Ocena |
| --- | --- | --- | --- | --- |
| SQL Server | Baza danych SQL Azure | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Baza danych Azure SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Maszyna wirtualna Azure SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | MAGAZYN DANYCH SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| SQL RDS | Baza danych Azure SQL, MI, maszyna wirtualna | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Baza danych Azure SQL, MI, maszyna wirtualna | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [Asystencie migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | MAGAZYN DANYCH SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [Asystencie migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Baza danych Azure SQL, MI, maszyna wirtualna | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [Asystencie migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB dla MySQL |  |  |  |
| RDS MySQL | Azure DB dla MySQL |  |  |  |
| PostgreSQL | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |  |  |
| PostgreSQL RDS | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |  |  |
| DB2 | Baza danych Azure SQL, MI, maszyna wirtualna | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [Asystencie migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Dostęp | Baza danych Azure SQL, MI, maszyna wirtualna |  | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase — SAP ASE | Baza danych Azure SQL, MI, maszyna wirtualna | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [Asystencie migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Baza danych Azure SQL, MI, maszyna wirtualna |  | |  |
| | | | | |

## <a name="migration-phase"></a>Faza migracji

| Źródło | Cel | Schemat | Dane<br/>Stanie | Dane<br/>Sieci |
| --- | --- | --- | --- | --- |
| SQL Server | Baza danych SQL Azure | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Baza danych Azure SQL | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Maszyna wirtualna Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | MAGAZYN DANYCH SQL |  |  |  |
| SQL RDS | Baza danych Azure SQL, MI, maszyna wirtualna | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Baza danych Azure SQL, MI, maszyna wirtualna | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | MAGAZYN DANYCH SQL | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |  | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Dane Imanis *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Baza danych Azure SQL, MI, maszyna wirtualna | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB dla MySQL | [Zrzut MySQL *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB dla MySQL | [Zrzut MySQL *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer | [Zrzut PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL RDS | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer | [Zrzut PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Baza danych Azure SQL, MI, maszyna wirtualna | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Dostęp | Baza danych Azure SQL, MI, maszyna wirtualna | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase — SAP ASE | Baza danych Azure SQL, MI, maszyna wirtualna | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [ASYSTENCIE migracji](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP IQ | Baza danych Azure SQL, MI, maszyna wirtualna | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | |
| | | | | |

## <a name="post-migration-phase"></a>Faza po migracji

| Źródło | Cel | Optymalizowanie |
| --- | --- | --- |
| SQL Server | Baza danych SQL Azure | [Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Baza danych Azure SQL | [Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Maszyna wirtualna Azure SQL | [Atlas w chmurze *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | MAGAZYN DANYCH SQL |  |
| SQL RDS | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| Oracle | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| Oracle | MAGAZYN DANYCH SQL |  |
| Oracle | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| MySQL | Azure DB dla MySQL |  |
| RDS MySQL | Azure DB dla MySQL |  |
| PostgreSQL | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |
| PostgreSQL RDS | Azure DB dla PostgreSQL —<br/>Pojedynczy serwer |  |
| DB2 | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| Dostęp | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| Sybase — SAP ASE | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| Sybase-SAP IQ | Baza danych Azure SQL, MI, maszyna wirtualna |  |
| | | |

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
