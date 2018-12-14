---
title: Dane macierzy migracji i narzędzia i usługi — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usług i narzędzi dostępnych do migracji baz danych i do obsługi różnych etapach procesu migracji.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/13/2018
ms.openlocfilehash: 2eb263c6776453c4cae217168af969221485bfa3
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386795"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Usługi i narzędzia dostępne dla scenariuszy migracji danych

Ten artykuł zawiera macierz Microsoft i usługi innych firm oraz dostępnych uzyskanymi różne bazy danych i scenariusze migracji danych i zadań specjalistycznych narzędzi.

Poniższe tabele zidentyfikować usługi i narzędzia, które można użyć, aby pomyślnie zaplanować migrację danych i do wykonania różnych etapach.

> [!NOTE]
> W poniższych tabelach elementy oznaczone gwiazdką (*) reprezentują narzędzi innych firm.

## <a name="business-justification-phase"></a>Uzasadnienie biznesowej — etap

| **Element źródłowy** | **Docelowy** | **Odkryj /**<br/>**Spis** | **Element docelowy i jednostki SKU**<br/>**Zalecenie** | **Całkowity koszt posiadania/zwrot z inwestycji i**<br/>**Przypadek biznesowy** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oprogramowanie SQL Server | MAGAZYN DANYCH SQL |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL USŁUG PULPITU ZDALNEGO | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor *](https://www.migvisor.com/) |  |
| Oracle | MAGAZYN DANYCH SQL | [Zestaw narzędzi do mapy](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB dla MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB dla MySQL |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB dla PostgreSQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| Postgresql w warstwie usług pulpitu zdalnego | Azure DB dla PostgreSQL |  |  | [Kalkulator całkowitego kosztu posiadania](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| Dostęp | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Faza migracji wstępnej

| **Element źródłowy** | **Docelowy** | **Dostęp do danych aplikacji**<br/>**Ocena warstwy** | **Baza danych**<br/>**Ocena** | **Wydajność**<br/>**Ocena** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | MAGAZYN DANYCH SQL |  |  |  |
| SQL USŁUG PULPITU ZDALNEGO | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | MAGAZYN DANYCH SQL |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB |  | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB dla MySQL |  |  |  |
| RDS MySQL | Azure DB dla MySQL |  |  |  |
| PostgreSQL | Azure DB dla PostgreSQL |  |  |  |
| Postgresql w warstwie usług pulpitu zdalnego | Azure DB dla PostgreSQL |  |  |  |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Dostęp | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Faza migracji

| **Element źródłowy** | **Docelowy** | **Schemat** | **Dane**<br/>**(W trybie offline)** | **Dane**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oprogramowanie SQL Server | MAGAZYN DANYCH SQL |  |  |  |
| SQL USŁUG PULPITU ZDALNEGO | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | MAGAZYN DANYCH SQL | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | Azure DB dla PostgreSQL |  |  |  |
| MongoDB | Cosmos DB | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis danych *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB dla MySQL | [MySQL zrzutu *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB dla MySQL | [MySQL zrzutu *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB dla PostgreSQL | [PG zrzutu *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Postgresql w warstwie usług pulpitu zdalnego | Azure DB dla PostgreSQL | [PG zrzutu *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Dostęp | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [PROGRAM SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [USŁUGA DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Firmy Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Faza po migracji

| **Element źródłowy** | **Docelowy** | **Optymalizacja** |
| --- | --- | --- |
| Oprogramowanie SQL Server | Azure SQL DB | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Warstwa bazy danych Azure SQL | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | Maszyna wirtualna Azure SQL | [Chmura — Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| Oprogramowanie SQL Server | MAGAZYN DANYCH SQL |  |
| SQL USŁUG PULPITU ZDALNEGO | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Oracle | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Oracle | MAGAZYN DANYCH SQL |  |
| Oracle | Azure DB dla PostgreSQL |  |
| MongoDB | Cosmos DB | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| MySQL | Azure DB dla MySQL |  |
| RDS MySQL | Azure DB dla MySQL |  |
| PostgreSQL | Azure DB dla PostgreSQL |  |
| Postgresql w warstwie usług pulpitu zdalnego | Azure DB dla PostgreSQL |  |
| DB2 | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Dostęp | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| Sybase | Usługi Azure SQL DB, MI, z maszyny Wirtualnej |  |
| | | |

## <a name="next-steps"></a>Kolejne kroki

Omówienie usługi Azure Database Migration Service, zobacz [co to jest podgląd usługi migracji bazy danych Azure](dms-overview.md).
