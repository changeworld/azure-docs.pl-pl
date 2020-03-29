---
title: Migracja SSIS z wystąpieniem zarządzanym usługi Azure SQL Database jako miejscem docelowym obciążenia bazy danych
description: Migracja SSIS z wystąpieniem zarządzanym usługi Azure SQL Database jako miejscem docelowym obciążenia bazy danych.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929779"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migracja SSIS z wystąpieniem zarządzanym usługi Azure SQL Database jako miejscem docelowym obciążenia bazy danych

Podczas migracji obciążeń bazy danych z programu SQL Server lokalnie do wystąpienia zarządzanego usługi Azure SQL Database należy zapoznać się z [usługą Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) i [topologią sieci dla migracji zarządzanych wystąpień usługi Azure SQL Database przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

W tym artykule koncentruje się na migracji pakietów sql server integration service (SSIS) przechowywanych w katalogu SSIS (SSISDB) i SQL Server Agent zadań, które harmonogramują wykonywanie pakietów SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrowanie katalogu SSIS (SSISDB)

Migrację SSISDB można przeprowadzić przy użyciu dms, jak opisano w [artykule: Migrowanie pakietów SSIS do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Zadania SSIS dla agenta wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzanej usługi Azure SQL Database ma natywny harmonogram pierwszej klasy, podobnie jak sql server agent w środowisku lokalnym.  Ponieważ narzędzie do migracji dla zadań SSIS nie jest jeszcze dostępne, muszą one zostać zmigrowane z programu SQL Server Agent lokalnie do agenta wystąpienia zarządzanego usługi Azure SQL Database za pomocą skryptów/kopii ręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Fabryka danych platformy Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Środowisko uruchomieniowe integracji platformy Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Usługa migracji bazy danych platformy Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie sieci dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu dms](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrowanie pakietów usług SSIS do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Następne kroki

- [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
