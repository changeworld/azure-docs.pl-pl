---
title: Dane macierzy migracji i narzędzia i usługi — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usług i narzędzi dostępnych do migracji baz danych i do obsługi różnych etapach procesu migracji.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 3b3bbe45c4850d1bb37a4d991e323d5f6d9a8a0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532426"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Usługi i narzędzia dostępne dla scenariuszy migracji danych

Ten artykuł zawiera macierz Microsoft i usługi innych firm oraz dostępnych uzyskanymi różne bazy danych i scenariusze migracji danych i zadań specjalistycznych narzędzi.

Poniższe tabele zidentyfikować usługi i narzędzia, które można użyć, aby pomyślnie zaplanować migrację danych i do wykonania różnych etapach.

> [!NOTE]
> W poniższych tabelach elementy oznaczone gwiazdką (*) reprezentują narzędzi innych firm.

## <a name="business-justification-phase"></a>Uzasadnienie biznesowej — etap

| **Element źródłowy** | **Docelowy** | **Odkryj /**<br/>**Spis** | **Element docelowy i jednostki SKU**<br/>**Zalecenie** | **Całkowity koszt posiadania/zwrot z inwestycji i**<br/>**Przypadek biznesowy** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | SQL DW |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor *](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB dla MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB dla MySQL |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB dla PostgreSQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Azure DB dla PostgreSQL |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| Access | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Faza migracji wstępnej

| **Element źródłowy** | **Docelowy** | **Dostęp do danych aplikacji**<br/>**Ocena warstwy** | **Baza danych**<br/>**Ocena** | **Wydajność**<br/>**Ocena** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | SQL DW |  |  |  |
| RDS SQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | SQL DW |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB |  | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB dla MySQL |  |  |  |
| RDS MySQL | Azure DB dla MySQL |  |  |  |
| PostgreSQL | Azure DB dla PostgreSQL |  |  |  |
| RDS PostgreSQL | Azure DB dla PostgreSQL |  |  |  |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Faza migracji

| **Element źródłowy** | **Docelowy** | **Schemat** | **Dane**<br/>**(W trybie offline)** | **Dane**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | SQL DW |  |  |  |
| RDS SQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB dla MySQL | [MySQL zrzutu *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB dla MySQL | [MySQL zrzutu *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB dla PostgreSQL | [PG zrzutu *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Azure DB dla PostgreSQL | [PG zrzutu *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Faza po migracji

| **Element źródłowy** | **Docelowy** | **Optymalizacja** |
| --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | SQL DW |  |
| RDS SQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Oracle | SQL DW |  |
| Oracle | Azure DB dla PostgreSQL |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| MySQL | Azure DB dla MySQL |  |
| RDS MySQL | Azure DB dla MySQL |  |
| PostgreSQL | Azure DB dla PostgreSQL |  |
| RDS PostgreSQL | Azure DB dla PostgreSQL |  |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Access | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| | | |

## <a name="next-steps"></a>Kolejne kroki

Omówienie usługi Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
