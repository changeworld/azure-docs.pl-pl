---
title: Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych
description: Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929779"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych

Podczas migrowania obciążeń bazy danych z SQL Server lokalnie do Azure SQL Database wystąpienia zarządzanego, należy zapoznać się z [usługą Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) i [topologiami sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ten artykuł koncentruje się na migracji pakietów SQL Server Integration Service (SSIS) przechowywanych w katalogu SSIS (SSISDB) i zadaniach agenta SQL Server, które zaplanują wykonywanie pakietów SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrowanie wykazu usług SSIS (SSISDB)

Migrację SSISDB można wykonać za pomocą usługi DMS, zgodnie z opisem w artykule: [Migrowanie pakietów SSIS do wystąpienia zarządzanego Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Zadania SSIS do Azure SQL Database agenta wystąpienia zarządzanego

Azure SQL Database wystąpienie zarządzane ma natywny, pierwszy-klasowy harmonogram, podobnie jak Agent SQL Server lokalnie.  Ponieważ narzędzie migracji dla zadań SSIS nie jest jeszcze dostępne, muszą zostać zmigrowane z SQL Server agenta lokalnie do Azure SQL Database agenta wystąpienia zarządzanego za pośrednictwem skryptów/kopii ręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrowanie pakietów SSIS do Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Następne kroki

- [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
