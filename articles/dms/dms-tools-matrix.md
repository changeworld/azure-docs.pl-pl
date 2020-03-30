---
title: Macierz narzędzi usługi migracji bazy danych platformy Azure
description: Dowiedz się więcej o usługach i narzędziach dostępnych do migracji baz danych i obsługi różnych faz procesu migracji.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/03/2020
ms.openlocfilehash: 2170612f9365ec9645b70c24236b865f106b646e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254824"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Usługi i narzędzia dostępne dla scenariuszy migracji danych

Ten artykuł zawiera macierz usług i narzędzi innych firm firmy firmy dostępnych do pomocy w różnych scenariuszach migracji bazy danych i danych i zadań specjalnych.

W poniższych tabelach identyfikowano usługi i narzędzia, których można użyć do pomyślnego planowania migracji danych i ukończenia różnych faz.

> [!NOTE]
> W poniższych tabelach elementy oznaczone gwiazdką (*) reprezentują narzędzia innych firm.

## <a name="business-justification-phase"></a>Faza uzasadnienia biznesowego

| Element źródłowy | Środowisko docelowe | Odkryj /<br/>Spis | Cel i jednostka SKU<br/>Zalecenie | TCO/ROI i<br/>Sprawa biznesowa |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Chmura*](https://www.cloudamize.com/) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Obiekt DB usługi Azure SQL | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Chmura*](https://www.cloudamize.com/) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Maszyna wirtualna Azure SQL | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Chmura*](https://www.cloudamize.com/) | [Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Chmura*](https://www.cloudamize.com/) |  | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Azure SQL DB, MI, VM |  | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Wizjer MigVisor*](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer | [Zestaw narzędzi MAP](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Chmura*](https://www.cloudamize.com/) | [Chmura*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/) | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB dla MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB dla MySQL |  |  | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |  | [Kalkulator YKO-procentowych](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Dostęp | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Faza przed migracją

| Element źródłowy | Środowisko docelowe | Dostęp do danych aplikacji<br/>Ocena warstwy | baza danych<br/>Ocena | Wydajność<br/>Ocena |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | Obiekt DB usługi Azure SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | Maszyna wirtualna Azure SQL | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | SQL DW | [DAMT ( DAMT )](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Dea](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | SQL DW | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Chmura*](https://www.cloudamize.com/) | [Chmura*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB dla MySQL |  |  |  |
| RDS MySQL | Azure DB dla MySQL |  |  |  |
| PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |  |  |
| RDS PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Dostęp | Azure SQL DB, MI, VM |  | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Faza migracji

| Element źródłowy | Środowisko docelowe | Schemat | Dane<br/>(W trybie offline) | Dane<br/>(Online) |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Chmura*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Obiekt DB usługi Azure SQL | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Maszyna wirtualna Azure SQL | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Chmura*](https://www.cloudamize.com/) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Chmura*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer | [Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Chmura*](https://www.cloudamize.com/)<br/>[Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Dane Imanis*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Azure SQL DB, MI, VM | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB dla MySQL | [Zrzut MySQL*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB dla MySQL | [Zrzut MySQL*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer | [Zrzut PG*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer | [Zrzut PG*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Dostęp | Azure SQL DB, MI, VM | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase - SAP ASE | Azure SQL DB, MI, VM | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [SSMA (SSMA)](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase - SAP IQ | Azure SQL DB, MI, VM | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Ispirer*](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | |
| | | | | |

## <a name="post-migration-phase"></a>Faza po migracji

| Element źródłowy | Środowisko docelowe | Optymalizacja |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | Obiekt DB usługi Azure SQL | [Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | Maszyna wirtualna Azure SQL | [Atlas chmury*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Chmura*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| RDS SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | SQL DW |  |
| Oracle | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |
| MongoDB | Cosmos DB | [Chmura*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | Azure DB dla MySQL |  |
| RDS MySQL | Azure DB dla MySQL |  |
| PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |
| RDS PostgreSQL | Usługa Azure DB for PostgreSQL -<br/>Pojedynczy serwer |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Dostęp | Azure SQL DB, MI, VM |  |
| Sybase - SAP ASE | Azure SQL DB, MI, VM |  |
| Sybase - SAP IQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem usługi migracji bazy danych platformy Azure, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure](dms-overview.md).
