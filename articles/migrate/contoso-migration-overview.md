---
title: Omówienie migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie strategii migracji i scenariusze używany przez firmę Contoso do migracji ich lokalnego centrum danych Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 659627eb6241bf31350b5a51c2e6c449c7f731c2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300807"
---
# <a name="contoso-migration-overview"></a>Migracja Contoso: omówienie


Jest to pierwszy artykułu w serii, która ilustruje sposób migrowania infrastruktury lokalnej do fikcyjnej organizacji Contoso [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) chmury. 

Seria artykuł zawiera przykładowych wdrożeń, które pomagają zrozumieć i wypróbować różne opcje do migracji do usługi Azure i pracy w środowisku hybrydowym. Artykuły pokazać, jak firma Contoso zakończeniu jej misji migracji, ale wskaźników do odczytu ogólne i szczegółowe instrukcje znajdują się w ciągu.

## <a name="introduction"></a>Wprowadzenie

Azure udostępnia rozbudowany zestaw usługi w chmurze. Jako deweloperów i informatyków te usługi służy do tworzenia, wdrażania i zarządzania aplikacjami w zakresie narzędzi i struktur, za pośrednictwem globalnej sieci centrów danych. Jeśli Twoja firma ma problem z przejściem do środowiska cyfrowego, chmura platformy Azure może ułatwić określenie sposobu optymalizacji zasobów i operacji, komunikację z klientami i pracownikami oraz przekształcanie produktów.

Jednak w przypadku platformy Azure uwzględniono fakt, że mimo wszystkich zalet chmury w zakresie szybkości, elastyczności, wydajności, niezawodności i minimalizowania kosztów wiele organizacji będzie musiało jeszcze przez jakiś czas korzystać z lokalnych centrów danych. W odpowiedzi na problemy z dostosowaniem chmury platforma Azure udostępnia strategię chmury hybrydowej, która stanowi pomost między lokalnymi centrami danych i chmurą publiczną platformy Azure. Dotyczy to na przykład używania zasobów w chmurze platformy Azure, takich jak kopia zapasowa, do ochrony zasobów lokalnych lub używania usługi analiz platformy Azure w celu uzyskiwania wglądu w obciążenia lokalne. 

W ramach strategii chmury hybrydowej platforma Azure udostępnia rosnącym rozwiązań migrowanie aplikacji lokalnych i obciążeń do chmury. Wystarczy wykonać proste czynności, aby kompleksowo ocenić zasoby lokalne i określić, jak będą działać w chmurze platformy Azure. Następnie po przeprowadzeniu szczegółowej oceny można bez obaw zmigrować zasoby na platformę Azure. Gdy zasoby są uruchomione na platformie Azure, można je zoptymalizować w celu zachowania i poprawy dostępu, elastyczności, bezpieczeństwa i niezawodności.

## <a name="migration-strategies"></a>Strategie migracji

Strategii migracji do chmury można podzielić na cztery ogólne kategorie: rehost, Refaktoryzuj, rearchitect lub Odbuduj. Strategii, które należy przyjąć, zależy od czynniki biznesowe i celów migracji. Strategie może przyjąć. Na przykład można wybrać opcję rehost aplikacje proste (przyrostu i shift) lub aplikacji, które nie są istotne dla Twojej firmy, ale rearchitect te, które są bardziej złożoną i biznesowych o znaczeniu krytycznym. Przyjrzyjmy się strategii.


**Strategia** | **Definicja** | **Kiedy używać** 
--- | --- | --- 
**Rehost** | Często określany jako "przyrostu i shift" migracji. Ta opcja nie wymaga zmian kodu i umożliwia migracji istniejącej aplikacji na platformę Azure szybko. Każdej aplikacji są migrowane, ponieważ jest do czerpania korzyści z chmury, bez ryzyka, kosztów związanych z zmian w kodzie. | Jeśli musisz szybkie przenoszenie aplikacji w chmurze.<br/><br/> Jeśli chcesz przenieść aplikacji bez modyfikowania jej.<br/><br/> Gdy aplikacji są zaprojektowana, dzięki czemu mogą oni korzystać z [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) skalowalność po migracji.<br/><br/> Gdy aplikacje są istotnym elementem do firmy, ale nie jest konieczne natychmiastowe zmiany do funkcji aplikacji.
**Zrefaktoryzuj** | Często określany jako "ponowne utworzenie pakietu", refaktoryzacji wymaga minimalne zmiany dotyczące aplikacji, tak aby umożliwić im połączenie z [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/)i użyj ofert chmury.<br/><br/> Na przykład można przeprowadzić migracji istniejących aplikacji do usługi Azure App Service lub usługi Kubernetes Azure (AKS). Lub może Refaktoryzuj relacyjnych i nierelacyjnych baz danych w opcji, takich jak wystąpienia zarządzane bazy danych SQL Azure, Azure bazy danych MySQL, baza danych Azure PostgreSQL i bazy danych Azure rozwiązania Cosmos. | Jeśli aplikację można z łatwością można udostępniane do pracy na platformie Azure.<br/><br/> Jeśli chcesz zastosować innowacyjnych rozwiązań DevOps dostarczany przez platformę Azure lub myślisz o DevOps przy użyciu strategii kontener dla obciążeń.<br/><br/> Do refaktoryzacji, należy zastanowić przenośność istniejące ścieżki bazowej kodu i dostępne programistami.
**Rearchitect** | Oszczędzasz migracji koncentruje się na modyfikowanie i rozszerzanie funkcjonalności aplikacji i kodu w celu zoptymalizowania Architektura aplikacji skalowalności chmury.<br/><br/> Na przykład można podzielić wbudowanymi aplikacji w grupie mikrousług, które współpracują ze sobą i łatwe skalowanie. Lub, można rearchitect relacyjnych i nierelacyjnych baz danych do pełnego zarządzania DBaaS rozwiązań, takich jak wystąpienia zarządzane bazy danych SQL Azure, Azure bazy danych MySQL, baza danych Azure, PostgreSQL i bazy danych Azure rozwiązania Cosmos. | Gdy aplikacji muszą główne wersje uwzględnienie nowych funkcji lub działał efektywnie na platformy w chmurze.<br/><br/> Jeśli chcesz wykorzystać istniejące inwestycje w technologie aplikacji wymagań skalowalności w sposób ekonomiczny Zastosuj innowacyjne rozwiązania Azure DevOps, a zminimalizować korzystanie z maszyn wirtualnych.
**Skompiluj ponownie** | Odbuduj ma dalszych czynności w kroku po ponownym utworzeniu aplikacji od podstaw przy użyciu technologii chmury systemu Azure. Na przykład można utworzyć pola zielony aplikacji za pomocą natywnego chmury technologii, takich jak pliki, Azure AI wystąpienia zarządzane bazy danych SQL Azure i bazy danych Azure rozwiązania Cosmos. | Gdy ma szybkie opracowywanie i istniejące aplikacje mają ograniczoną funkcjonalność i cykl życia.<br/><br/> Jeśli wszystko jest gotowe do przyspieszenia innowacyjnych firm (w tym rozwiązania w zakresie opracowywania oprogramowania dostarczany przez platformę Azure), tworzenie nowej aplikacji przy użyciu technologii chmury natywne i wykorzystać innowacyjny AI, blockchain i IoT.

## <a name="migration-articles"></a>Artykuły migracji

Artykuły, które tworzą serii migracji Contoso podsumowano w poniższej tabeli.  

- Scenariusze są zwiększenia złożoności i Trwa dodawanie do nich wraz z upływem czasu.
- Każdy scenariusz migracji jest wymuszany przez określenie strategii migracji cele biznesowe nieco inne.
- Dla każdego scenariusza wdrażania udostępniamy informacji na temat czynniki biznesowe i cele, architekturę proponowanych, kroki do przeprowadzenia migracji oraz zalecenia dla oczyszczania i następne kroki po zakończeniu migracji.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
Artykuł 1: Omówienie (w tym artykule) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje używane. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-overview.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md) | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji sieci web maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), bazy danych aplikacji przy użyciu zarządzanego wystąpienia serwera SQL i [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Usługa Site Recovery ich użyć do migracji aplikacji maszyny Wirtualnej, a usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności AlwaysOn programu SQL Server. | Dostępne
Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure | Pokazuje, jak Contoso migruje aplikacji maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. | Planowany
Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure | Pokazuje, jak Contoso przeprowadzanie migracji aplikacji maszyny Wirtualnej przy użyciu usługi Site Recovery, a używa MySQL Workbench migrację do wystąpienia serwera MySQL Azure. | Planowany



### <a name="demo-apps"></a>Wersja demonstracyjna dla aplikacji

Artykuły korzystanie z dwóch aplikacji demonstracyjnej - SmartHotel i osTicket.

- SmartHotel360: Ta aplikacja został opracowany przez firmę Microsoft jako przetestuj aplikację, pomocne podczas pracy z platformą Azure. Jest podana jako typu open source i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360). Jest połączona z bazą danych programu SQL Server aplikacji ASP.NET. Aplikacja jest obecnie na dwóch maszyn wirtualnych VMware systemem Windows Server 2008 R2 i SQL Server 2008 R2. Maszyny wirtualne są obsługiwana lokalnie i aplikacji zarządzanych przez serwer vCenter.
- osTicket jest biletów aplikację, która działa w systemie Linux działu open source. Możesz pobrać go z [GitHub](https://github.com/osTicket/osTicket). Bieżąca aplikacja jest na dwóch maszyn wirtualnych VMware systemem Ubuntu 16.04LTS, przy użyciu Apache 2, PHP 7.0 i MySQL 5.7
    

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso konfiguruje ich lokalnych i infrastruktury platformy Azure w przygotowaniu do migracji.



