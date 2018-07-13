---
title: Omówienie migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie strategii migracji i scenariuszy używane przez firmę Contoso migrować swoje lokalne centrum danych na platformę Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 4cad84d1cabd140f4846cb823e8c4b7ce11bc1f6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008250"
---
# <a name="contoso-migration-overview"></a>Migracja Contoso: omówienie


W tym artykule przedstawiono sposób fikcyjnej organizacji o nazwie Contoso migruje infrastruktury lokalnej do [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) chmury. 

Ten dokument jest pierwszy w serii artykułów, które pokazują, jak fikcyjnej firmy Contoso przeprowadza migrację do platformy Azure. Seria zawiera informacje i scenariusze, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu. Artykuły pokazują, jak firma Contoso kończy swoją misję migracji, ale wskaźników do ogólnego odczytu i uzyskać szczegółowe instrukcje znajdują się w całym.

## <a name="introduction"></a>Wprowadzenie

Azure zapewnia dostęp do kompleksowych usług w chmurze. Jako deweloperów i specjalistów IT możesz korzystać z tych usług tworzenie, wdrażanie i zarządzanie aplikacjami na szereg narzędzi i platform, za pośrednictwem globalnej sieci centrów danych. Jeśli Twoja firma ma problem z przejściem do środowiska cyfrowego, chmura platformy Azure może ułatwić określenie sposobu optymalizacji zasobów i operacji, komunikację z klientami i pracownikami oraz przekształcanie produktów.

Jednak w przypadku platformy Azure uwzględniono fakt, że mimo wszystkich zalet chmury w zakresie szybkości, elastyczności, wydajności, niezawodności i minimalizowania kosztów wiele organizacji będzie musiało jeszcze przez jakiś czas korzystać z lokalnych centrów danych. W odpowiedzi na problemy z dostosowaniem chmury platforma Azure udostępnia strategię chmury hybrydowej, która stanowi pomost między lokalnymi centrami danych i chmurą publiczną platformy Azure. Dotyczy to na przykład używania zasobów w chmurze platformy Azure, takich jak kopia zapasowa, do ochrony zasobów lokalnych lub używania usługi analiz platformy Azure w celu uzyskiwania wglądu w obciążenia lokalne. 

W ramach strategii chmury hybrydowej platforma Azure udostępnia rozwijające się rozwiązania do migrowania aplikacji lokalnych i obciążeń do chmury. Wystarczy wykonać proste czynności, aby kompleksowo ocenić zasoby lokalne i określić, jak będą działać w chmurze platformy Azure. Następnie po przeprowadzeniu szczegółowej oceny można bez obaw zmigrować zasoby na platformę Azure. Gdy zasoby są uruchomione na platformie Azure, można je zoptymalizować w celu zachowania i poprawy dostępu, elastyczności, bezpieczeństwa i niezawodności.

## <a name="migration-strategies"></a>Strategie migracji

Strategie migracji do chmury można podzielić na cztery ogólne kategorie: rehost, refaktoryzacji, przekształcanie i Odbuduj. Strategii, które należy przyjąć, zależy od czynników biznesowych i celów migracji. Mogą przyjąć różne strategie. Na przykład można wybrać rehost Wizard (lift-and-shift) proste aplikacje lub aplikacje, które nie są istotne dla Twojej firmy, ale Przekształcanie te, które są bardziej złożone i krytyczne dla prowadzonej działalności. Przyjrzyjmy się strategie.


**Strategia** | **Definicja** | **Kiedy należy używać** 
--- | --- | --- 
**Rehost** | Często określane jako migracji "lift-and-shift". Ta opcja nie wymaga zmian w kodzie, a teraz migrujesz istniejące aplikacje na platformę Azure szybko. Każda aplikacja jest migrowana jest czerpać korzyści z pracy w chmurze, bez ryzyko i koszty związane ze zmianami kodu. | Jeśli musisz szybko przenieść aplikacje do chmury.<br/><br/> Jeśli chcesz przenieść aplikację bez modyfikowania go.<br/><br/> Gdy Twoje aplikacje mające architekturę, dzięki czemu mogą one używać [IaaS platformy Azure](https://azure.microsoft.com/overview/what-is-iaas/) skalowalność po migracji.<br/><br/> Gdy aplikacje są istotnym elementem dla Twojej firmy, ale nie ma potrzeby natychmiastowego zmiany możliwości aplikacji.
**Refaktoryzacja** | Często określane jako "ponowne pakowanie", Refaktoryzacja wymaga minimalne zmiany dotyczące aplikacji, tak, aby umożliwić im połączenie z [modelu PaaS platformy Azure](https://azure.microsoft.com/overview/what-is-paas/)i korzystać z oferty w chmurze.<br/><br/> Na przykład można przeprowadzić migracji istniejących aplikacji do usługi Azure App Service lub Azure Kubernetes Service (AKS).<br/><br/> Alternatywnie można refaktoryzacji relacyjnych i nierelacyjnych baz danych na opcje, takie jak wystąpienia zarządzanego Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure Cosmos DB. | W przypadku aplikacji z łatwością mogą być udostępniane do pracy na platformie Azure.<br/><br/> Jeśli chcesz zastosować innowacyjne praktyki metodyki DevOps udostępnianych przez platformę Azure lub myślisz o DevOps za pomocą strategii kontenerów dla obciążeń.<br/><br/> Do refaktoryzacji, należy wziąć pod uwagę przenośność istniejącej bazy kodu i umiejętności tworzenia dostępne.
**Przekształcanie** | Transformować migracji koncentruje się na modyfikowanie, a także rozszerzanie funkcjonalności aplikacji i kodu podstawowego, aby zoptymalizować architektury aplikacji skalowalności chmury.<br/><br/> Na przykład może spowodować przerwanie monolitycznych w grupie mikrousług współpracują ze sobą i łatwe skalowanie w dół.<br/><br/> Lub można Przekształcanie relacyjnych i nierelacyjnych baz danych do w pełni zarządzana rozwiązań DBaaS, takich jak wystąpienia zarządzanego Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure Cosmos DB. | Gdy Twoje aplikacje muszą główne wersje zawierają nowe funkcje lub wydajnie pracować nad platforma usług w chmurze.<br/><br/> Jeśli chcesz korzystać z istniejących inwestycji w aplikacji, spełnianie wymagań skalowalności, zastosować innowacyjne praktyki DevOps platformy Azure i zminimalizować wykorzystanie maszyn wirtualnych.
**Ponowna kompilacja** | Ponownie skompiluj ma rzeczy w kroku dalsze poprzez ponowną kompilację aplikacji od podstaw przy użyciu technologii chmury platformy Azure.<br/><br/> Na przykład można zbudować zielone pole aplikacji przy użyciu technologii natywnych dla chmury, takich jak bez użycia serwera, Azure AI, wystąpienia zarządzanego Azure SQL Database i Azure Cosmos DB. | Gdy ma szybki rozwój i istniejących aplikacji o ograniczonej funkcjonalności lub okresu eksploatacji.<br/><br/> Gdy wszystko będzie gotowe przyspieszyć wprowadzanie innowacji firmy (w tym metodyki DevOps, udostępnianych przez platformę Azure), tworzyć nowe aplikacje przy użyciu technologii natywnych dla chmury i korzystać z zalet postępy w sztucznej Inteligencji, łańcuch bloków i IoT.

## <a name="migration-articles"></a>Artykuły migracji

Artykuły w tej serii przedstawiono w poniższej tabeli.  

- Każdy scenariusz migracji jest wymuszany przez cele nieco biznesowe ustalić strategię migracji.
- Dla każdego scenariusza wdrażania, firma Microsoft zapewnia informacji na temat czynników biznesowych i cele, proponowane architektury, kroki do przeprowadzenia migracji i zalecenia oczyszczania i następne kroki po zakończeniu migracji.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
Artykuł 1: omówienie | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | W tym artykule.
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest wykonywany migracji lift-and-shift na platformie Azure dla lokalnych aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i programu SQL Server zawsze włączona grupa dostępności](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzacja TFS w usłudze VSTS](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje ich lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne






### <a name="demo-apps"></a>Aplikacje z pokazem

W artykułach używany dwie aplikacje z pokazem - SmartHotel i osTicket.

- **Rozwiązanie SmartHotel360**: tej aplikacji został opracowany przez firmę Microsoft jako aplikacja testowa, którego można użyć podczas pracy z platformą Azure. Jest ona udostępniana jako oprogramowanie open source i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360). Jest połączona z bazą danych programu SQL Server aplikacji platformy ASP.NET. Obecnie aplikacja znajduje się na dwie maszyny wirtualne VMware, systemem Windows Server 2008 R2 i SQL Server 2008 R2. Aplikacja maszyny wirtualne są hostowane lokalnie i zarządzane przez program vCenter Server.
- **osTicket**: typu open-source pomocy technicznej tworzenia biletów aplikacja, która działa w systemie Linux. Możesz ją pobrać z [GitHub](https://github.com/osTicket/osTicket). Bieżąca aplikacja znajduje się na dwie maszyny wirtualne VMware systemem Ubuntu 16.04LTS, przy użyciu Apache 2, środowisko PHP 7.0 i MySQL 5.7
    

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso konfiguruje lokalną i infrastrukturę platformy Azure w celu przygotowania do migracji.



