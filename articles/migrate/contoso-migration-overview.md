---
title: Seria migracji contoso | Dokumentacja firmy Microsoft
description: Zawiera omówienie strategii migracji i scenariuszy używane przez firmę Contoso migrować swoje lokalne centrum danych na platformę Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: bf2a8dec919980db616cd927639cdd2f695720c5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238025"
---
# <a name="contoso-migration-series"></a>Seria dotycząca migracji w firmie Contoso


Mamy seria artykułów, które przedstawiają sposób fikcyjnej organizacji Contoso jest migrowana w obrębie lokalnej infrastruktury do [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) chmury. 

Seria zawiera informacje i scenariusze, które pokazują, jak konfigurować migrację, infrastruktury, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania w miarę jej postępu. Artykuły pokazują, jak firma Contoso kończy swoją misję migracji, ale wskaźników do ogólnego odczytu i uzyskać szczegółowe instrukcje znajdują się w całym.

## <a name="migration-articles"></a>Artykuły migracji

Artykuły w tej serii przedstawiono w poniższej tabeli.  

- Każdy scenariusz migracji jest wymuszany przez cele nieco biznesowe ustalić strategię migracji.
- Dla każdego scenariusza wdrażania, firma Microsoft zapewnia informacji na temat czynników biznesowych i cele, proponowane architektury, kroki do przeprowadzenia migracji i zalecenia oczyszczania i następne kroki po zakończeniu migracji.

**Artykuł** | **Szczegóły** 
--- | --- 
[Artykuł 1: Przegląd](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. 
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. 
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant.
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. 
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. 
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso zostanie ukończona lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery.
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. 
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych.     
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. 
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure.
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](c https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. 
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB.  
[Artykuł 14: Skalowanie migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. 


    

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migracji do chmury. 

