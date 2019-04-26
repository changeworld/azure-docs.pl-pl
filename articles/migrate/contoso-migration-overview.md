---
title: Omówienie migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie strategii migracji i scenariuszy używane przez firmę Contoso migrować swoje lokalne centrum danych na platformę Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 8c1b5cc8a9f1c1246bd1973539e3bd00b340a657
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679038"
---
# <a name="contoso-migration-overview"></a>Migracja Contoso: Omówienie


W tym artykule przedstawiono sposób fikcyjnej organizacji Contoso jest migrowana w obrębie lokalnej infrastruktury do [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) chmury. 

Ten dokument jest pierwszy w serii artykułów, które pokazują, jak fikcyjnej firmy Contoso przeprowadza migrację do platformy Azure. Seria zawiera informacje i scenariusze, które pokazują, jak konfigurować migrację, infrastruktury, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu. Artykuły pokazują, jak firma Contoso kończy swoją misję migracji, ale wskaźników do ogólnego odczytu i uzyskać szczegółowe instrukcje znajdują się w całym.

## <a name="introduction"></a>Wprowadzenie

Azure zapewnia dostęp do kompleksowych usług w chmurze. Jako deweloperów i specjalistów IT możesz korzystać z tych usług tworzenie, wdrażanie i zarządzanie aplikacjami na szereg narzędzi i platform, za pośrednictwem globalnej sieci centrów danych. Jeśli Twoja firma ma problem z przejściem do środowiska cyfrowego, chmura platformy Azure może ułatwić określenie sposobu optymalizacji zasobów i operacji, komunikację z klientami i pracownikami oraz przekształcanie produktów.

Jednak w przypadku platformy Azure uwzględniono fakt, że mimo wszystkich zalet chmury w zakresie szybkości, elastyczności, wydajności, niezawodności i minimalizowania kosztów wiele organizacji będzie musiało jeszcze przez jakiś czas korzystać z lokalnych centrów danych. W odpowiedzi na problemy z dostosowaniem chmury platforma Azure udostępnia strategię chmury hybrydowej, która stanowi pomost między lokalnymi centrami danych i chmurą publiczną platformy Azure. Na przykład przy użyciu zasobów w chmurze platformy Azure, takich jak usługi Azure Backup do ochrony zasobów lokalnych lub za pomocą analiz platformy Azure, aby uzyskać wgląd w obciążenia lokalne. 

W ramach strategii chmury hybrydowej platforma Azure udostępnia rozwijające się rozwiązania do migrowania aplikacji lokalnych i obciążeń do chmury. Wystarczy wykonać proste czynności, aby kompleksowo ocenić zasoby lokalne i określić, jak będą działać w chmurze platformy Azure. Następnie po przeprowadzeniu szczegółowej oceny można bez obaw zmigrować zasoby na platformę Azure. Gdy zasoby są uruchomione na platformie Azure, można je zoptymalizować w celu zachowania i poprawy dostępu, elastyczności, bezpieczeństwa i niezawodności.

## <a name="migration-strategies"></a>Strategie migracji

Strategie migracji do chmury można podzielić na cztery ogólne kategorie: rehost, refaktoryzacji, przekształcanie i Odbuduj. Strategii, które należy przyjąć, zależy od czynników biznesowych i celów migracji. Mogą przyjąć różne strategie. Na przykład można wybrać rehost Wizard (lift-and-shift) proste aplikacje lub aplikacje, które nie są istotne dla Twojej firmy, ale Przekształcanie te, które są bardziej złożone i krytyczne dla prowadzonej działalności. Przyjrzyjmy się strategie.


**Strategia** | **Definicja** | **Kiedy należy używać** 
--- | --- | --- 
**Rehost** | Często określane jako migracji "lift-and-shift". Ta opcja nie wymaga zmian w kodzie, a teraz migrujesz istniejące aplikacje na platformę Azure szybko. Każda aplikacja jest migrowana jest czerpać korzyści z pracy w chmurze, bez ryzyko i koszty związane ze zmianami kodu. | Jeśli musisz szybko przenieść aplikacje do chmury.<br/><br/> Jeśli chcesz przenieść aplikację bez modyfikowania go.<br/><br/> Gdy Twoje aplikacje mające architekturę, dzięki czemu mogą one używać [IaaS platformy Azure](https://azure.microsoft.com/overview/what-is-iaas/) skalowalność po migracji.<br/><br/> Gdy aplikacje są istotnym elementem dla Twojej firmy, ale nie ma potrzeby natychmiastowego zmiany możliwości aplikacji.
**Refaktoryzacja** | Często określane jako "ponowne pakowanie", Refaktoryzacja wymaga minimalne zmiany dotyczące aplikacji, tak, aby umożliwić im połączenie z [modelu PaaS platformy Azure](https://azure.microsoft.com/overview/what-is-paas/)i korzystać z oferty w chmurze.<br/><br/> Na przykład można przeprowadzić migracji istniejących aplikacji do usługi Azure App Service lub Azure Kubernetes Service (AKS).<br/><br/> Alternatywnie można refaktoryzacji relacyjnych i nierelacyjnych baz danych na opcje, takie jak wystąpienia zarządzanego Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure Cosmos DB. | W przypadku aplikacji z łatwością mogą być udostępniane do pracy na platformie Azure.<br/><br/> Jeśli chcesz zastosować innowacyjne praktyki metodyki DevOps udostępnianych przez platformę Azure lub myślisz o DevOps za pomocą strategii kontenerów dla obciążeń.<br/><br/> Do refaktoryzacji, należy wziąć pod uwagę przenośność istniejącej bazy kodu i umiejętności tworzenia dostępne.
**Przekształcanie** | Transformować migracji koncentruje się na modyfikowanie, a także rozszerzanie funkcjonalności aplikacji i bazy kodu, aby zoptymalizować architektury aplikacji skalowalności chmury.<br/><br/> Na przykład może spowodować przerwanie monolitycznych w grupie mikrousług współpracują ze sobą i łatwe skalowanie w dół.<br/><br/> Alternatywnie można Przekształcanie relacyjnych i nierelacyjnych baz danych do w pełni zarządzanych rozwiązań DBaaS, takich jak wystąpienia zarządzanego Azure SQL Database, Azure Database for MySQL — Azure Database for PostgreSQL i Azure Cosmos DB. | Gdy Twoje aplikacje muszą główne wersje zawierają nowe funkcje lub wydajnie pracować nad platforma usług w chmurze.<br/><br/> Jeśli chcesz korzystać z istniejących inwestycji w aplikacji, spełnianie wymagań skalowalności, zastosować innowacyjne praktyki DevOps platformy Azure i zminimalizować wykorzystanie maszyn wirtualnych.
**Ponowna kompilacja** | Ponownie skompiluj ma rzeczy w kroku dalsze poprzez ponowną kompilację aplikacji od podstaw przy użyciu technologii chmury platformy Azure.<br/><br/> Na przykład można utworzyć aplikacji zielone pole za pomocą [natywnych dla chmury](https://azure.com/cloudnative) technologii, takich jak Azure Functions, Azure AI, wystąpienia zarządzanego Azure SQL Database i Azure Cosmos DB. | Gdy ma szybki rozwój i istniejących aplikacji o ograniczonej funkcjonalności lub okresu eksploatacji.<br/><br/> Gdy wszystko będzie gotowe przyspieszyć wprowadzanie innowacji firmy (w tym metodyki DevOps, udostępnianych przez platformę Azure), tworzyć nowe aplikacje przy użyciu technologii natywnych dla chmury i korzystać z zalet postępy w sztucznej Inteligencji, łańcuch bloków i IoT.

## <a name="migration-articles"></a>Artykuły migracji

Artykuły w tej serii przedstawiono w poniższej tabeli.  

- Każdy scenariusz migracji jest wymuszany przez cele nieco biznesowe ustalić strategię migracji.
- Dla każdego scenariusza wdrażania, firma Microsoft zapewnia informacji na temat czynników biznesowych i cele, proponowane architektury, kroki do przeprowadzenia migracji i zalecenia oczyszczania i następne kroki po zakończeniu migracji.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
Artykuł 1: Omówienie | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | W tym artykule
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne [artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) 
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych. | Dostępne    
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne    
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne 
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne

W tym artykule, który Contoso skonfiguruje wszystkie elementy infrastruktury potrzebny do ukończenia wszystkich scenariuszy migracji. 







### <a name="demo-apps"></a>Aplikacje z pokazem

W artykułach używany dwie aplikacje z pokazem — rozwiązanie SmartHotel360 i osTicket.

- **SmartHotel360**: Ta aplikacja został opracowany przez firmę Microsoft jako aplikacja testowa, którego można użyć podczas pracy z platformą Azure. Jest ona udostępniana jako oprogramowanie open source i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360). Jest połączona z bazą danych programu SQL Server aplikacji platformy ASP.NET. Obecnie aplikacja znajduje się na dwie maszyny wirtualne VMware, systemem Windows Server 2008 R2 i SQL Server 2008 R2. Aplikacja maszyny wirtualne są hostowane lokalnie i zarządzane przez program vCenter Server.
- **osTicket**: Zarządzanie biletami aplikacji działu typu open source, która działa w systemie Linux. Możesz ją pobrać z [GitHub](https://github.com/osTicket/osTicket). Obecnie aplikacja znajduje się na dwie maszyny wirtualne VMware systemem Ubuntu 16.04 LTS, przy użyciu Apache 2, środowisko PHP 7.0 i MySQL 5.7
    

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso konfiguruje lokalną i infrastrukturę platformy Azure w celu przygotowania do migracji.



