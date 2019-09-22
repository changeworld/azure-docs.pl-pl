---
title: Seria migracji firmy Contoso | Microsoft Docs
description: Zawiera omówienie strategii migracji i scenariuszy używanych przez firmę Contoso do migrowania lokalnych centrów danych do platformy Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: raynew
ms.openlocfilehash: d20c0be47f44cdce8ea895007494565d37f5923f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179140"
---
# <a name="contoso-migration-series"></a>Seria dotycząca migracji w firmie Contoso


Mamy szereg artykułów demonstrujących sposób, w jaki fikcyjna organizacja firmy Contoso migruje infrastrukturę lokalną do chmury [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

Seria zawiera informacje i scenariusze, które ilustrują sposób konfigurowania migracji infrastruktury i uruchamiania różnych typów migracji. Scenariusze rosną w miarę postępu. W tych artykułach pokazano, jak firma firmy Contoso wykonuje swoją migrację, ale wskazówki dotyczące ogólnego odczytywania i konkretnych instrukcji są udostępniane w całym systemie.

## <a name="migration-articles"></a>Artykuły dotyczące migracji

Artykuły z tej serii są podsumowane w poniższej tabeli.  

- Każdy scenariusz uwzględnia nieco inne cele biznesowe, które determinują strategię migracji.
- W przypadku każdego scenariusza wdrożenia zapewniamy informacje na temat celów biznesowych, proponowanej architektury, czynności do wykonania podczas migracji, zaleceń dotyczących czyszczenia zasobów oraz kolejnych czynności, które należy wykonać po zakończeniu migracji.

**Artykuł** | **Szczegóły** 
--- | --- 
[Artykuł 1: Podsumowanie](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Omówienie serii artykułów, strategii migracji firmy Contoso i przykładowych aplikacji, które są używane w tej serii. 
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Firma Contoso przygotowuje infrastrukturę lokalną i infrastrukturę platformy Azure do migracji. Ta sama infrastruktura jest używana w przypadku wszystkich artykułów migracji z serii. 
[Artykuł 3: Ocenianie zasobów lokalnych na potrzeby migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Firma Contoso wykonuje ocenę swojej lokalnej aplikacji SmartHotel360 działającej w oprogramowaniu VMware. Firma Contoso ocenia maszyny wirtualne aplikacji przy użyciu usługi Azure Migrate, a aplikacja SQL Server bazy danych przy użyciu Data Migration Assistant.
[Artykuł 4: Przehostaj aplikację na maszynę wirtualną platformy Azure i SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Firma Contoso wykonuje migrację do platformy Azure w celu przeprowadzenia przejścia i przesunięcia do swojej lokalnej aplikacji SmartHotel360. Firma Contoso migruje maszynę wirtualną aplikacji frontonu przy użyciu [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Firma Contoso migruje bazę danych aplikacji do Azure SQL Database wystąpienia zarządzanego przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artykuł 5: Hostowanie aplikacji na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Firma Contoso migruje maszyny wirtualne aplikacji SmartHotel360 do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. 
[Artykuł 6: Przehostaj aplikację na maszynach wirtualnych platformy Azure i w SQL Server zawsze włączonych grup dostępności](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Firma Contoso migruje aplikację SmartHotel360. Firma Contoso używa Site Recovery do migrowania maszyn wirtualnych aplikacji. Używa Database Migration Service do migrowania bazy danych aplikacji do klastra SQL Server, który jest chroniony przez grupę dostępności AlwaysOn. 
[Artykuł 7: Przehostaj aplikację systemu Linux na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Firma Contoso wykonuje migrację podnoszenia i przesunięcia swojej aplikacji osTicket systemu Linux na maszyny wirtualne platformy Azure przy użyciu usługi Site Recovery.
[Artykuł 8: Przehostaj aplikację systemu Linux na maszynach wirtualnych platformy Azure i Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Firma Contoso migruje swoją aplikację osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu Site Recovery. Baza danych aplikacji jest migrowana do usługi Azure Database for MySQL za pomocą programu MySQL Workbench. 
[Artykuł 9: Refaktoryzacja aplikacji w aplikacji sieci Web platformy Azure i Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Firma Contoso migruje swoją aplikację SmartHotel360 do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do wystąpienia usługi Azure SQL Server przy użyciu Asystent migracji bazy danych.     
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w aplikacji internetowej platformy Azure i Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Firma Contoso migruje swoją aplikację z systemem Linux osTicket do aplikacji internetowej platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager zintegrowanej z usługą GitHub w celu ciągłego dostarczania. Firma Contoso migruje bazę danych aplikacji do wystąpienia Azure Database for MySQL. 
[Artykuł 11: Refaktoryzacja Team Foundation Server na Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Firma Contoso migruje swoje lokalne wdrożenie Team Foundation Server do Azure DevOps Services na platformie Azure.
[Artykuł 12: Ponowne tworzenie architektury aplikacji w kontenerach platformy Azure i Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Firma Contoso migruje swoją aplikację SmartHotel na platformę Azure. Następnie rearchitektura warstwy sieci Web aplikacji jako kontenera systemu Windows działającego w usłudze Azure Service Fabric i bazy danych z Azure SQL Database.
[Artykuł 13: Ponowne kompilowanie aplikacji na platformie Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Firma Contoso odbudowuje swoją aplikację SmartHotel przy użyciu szeregu możliwości i usług platformy Azure, w tym Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services i Azure Cosmos DB.
[Artykuł 14: Skalowanie migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po zakończeniu kombinacji migracji firma Contoso przygotowuje skalowanie do pełnej migracji do platformy Azure.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migracji do chmury.
- Więcej informacji na temat strategii migracji dla innych scenariuszy (par Source/Target) znajduje się w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).
