---
title: Wybieranie między poleceniami SQL na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wybrać jedną z opcji wdrażania w ramach usługi Azure SQL Database i od usługi Azure SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: chmura usługi SQL Server, SQL Server w chmurze, baza danych PaaS, chmura SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: d9cd5ba0b697cbf67f943eb49d66010745d8561e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584868"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Wybieranie opcji programu SQL Server po prawej stronie na platformie Azure

Na platformie Azure może mieć obciążeń programu SQL Server, działające w infrastrukturze hostowanej (IaaS) lub są uruchamiane jako usługa hostowana ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). W ramach PaaS masz kilka opcji wdrażania i warstwy usług w ramach każdej opcji wdrażania. Najistotniejsze pytanie, które musisz poprosić przed podjęciem decyzji o PaaS lub IaaS jest chcesz Zarządzanie bazą danych, stosowanie poprawek, twórz kopie zapasowe lub chcesz przekazać te operacje na platformie Azure?
W zależności od odpowiedzi dostępne są następujące opcje:

- [Azure SQL Database](sql-database-technical-overview.md): W pełni zarządzanego aparatu bazy danych SQL, oparte na najnowszej stabilnej Enterprise programu SQL Server. Jest to relacyjnej bazy danych jako — usługa (DBaaS) hostowanych w chmurze platformy Azure, ją zaliczyć do kategorii branżowych *Platform-as-a-Service (PaaS)* . Usługa SQL database ma wiele dostępnych opcji wdrażania, z których każdy jest oparta na ustandaryzowanym sprzęcie i oprogramowaniu, będące własnością, hostowane i konserwowane przez firmę Microsoft. Usługa SQL Database można użyć wbudowanych funkcji i funkcjonalności, które wymagają konfiguracji rozbudowane, gdy jest używana w programie SQL Server (lokalnie lub w maszynie wirtualnej platformy Azure). Płatność za korzystanie z usługi SQL Database odbywa się zgodnie z rzeczywistym użyciem, a opcje pozwalają na skalowanie w górę lub w poziomie, dzięki czemu klienci mają do dyspozycji większe możliwości bez najmniejszych zakłóceń. Usługa SQL Database ma dodatkowe funkcje, które nie są dostępne w programie SQL Server, takie jak wbudowana wysoka dostępność, analizy i zarządzania. Usługa Azure SQL Database oferuje następujące opcje wdrażania:
  
  - Jako [pojedynczej bazy danych](sql-database-single-database.md) swój własny zestaw zasobów zarządzanych za pomocą serwera usługi SQL Database. Pojedyncza baza danych jest podobny do [zawartych baz danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) w programie SQL Server. Ta opcja jest zoptymalizowany pod kątem tworzenia nowoczesnych aplikacji nowych aplikacji zaprojektowanych w chmurze.
  - [Puli elastycznej](sql-database-elastic-pool.md), który stanowi kolekcję baz danych z współdzielonym zestawem zasobów zarządzanych za pomocą serwera usługi SQL Database. Pojedyncze bazy danych mogą być przenoszone do i z puli elastycznej. Ta opcja jest zoptymalizowany pod kątem tworzenia nowoczesnych aplikacji nowych aplikacji zaprojektowanych w chmurze przy użyciu wielodostępnych aplikacji SaaS.
  - [Wystąpienie zarządzane](sql-database-managed-instance.md), który stanowi kolekcję baz danych z systemu i użytkownika z współdzielonym zestawem zasobów. Wystąpienie zarządzane jest podobny do wystąpienia zasobów oferty [aparatu bazy danych programu Microsoft SQL Server] udostępnione dla baz danych i dodatkowych funkcji należących do zakresu wystąpienia. Wystąpienie zarządzane obsługuje migrację bazy danych ze środowiska lokalnego z minimalnym bez zmian w bazie danych. Ta opcja udostępnia wszystkie korzyści PaaS usługi Azure SQL Database, ale dodaje możliwości, które były wcześniej dostępne tylko na maszynach wirtualnych programu SQL. Obejmuje natywne sieci wirtualnej (VNet) i prawie 100% zgodności z lokalnym serwerem SQL.
- [Program SQL Server na maszynach wirtualnych Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) należy do kategorii branżowych *Infrastructure-as-a-Service (IaaS)* i pozwala na uruchamianie programu SQL Server wewnątrz maszyny wirtualnej z w pełni zarządzane w chmurze platformy Azure. [Maszyny wirtualne programu SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) również uruchomić na ustandaryzowanym sprzęcie, będące własnością, hostowane i konserwowane przez firmę Microsoft. Korzystając z programu SQL Server na maszynie Wirtualnej, można płacić za — tak jak w rzeczywistym licencji programu SQL Server dołączoną do obrazu programu SQL Server lub łatwe użycie istniejącej licencji. Możesz również zatrzymać lub wznowienia maszyny Wirtualnej, zgodnie z potrzebami. Program SQL Server zainstalowany i hostowany w chmurze w systemie Windows Server lub Linux maszyn wirtualnych (VM) na platformie Azure, znany również jako infrastruktura jako usługa (IaaS). Program SQL Server na maszynach wirtualnych platformy Azure to dobra opcja w przypadku migracji lokalnych baz danych programu SQL Server i aplikacje bez zmiany bazy danych. Wszystkie najnowsze wersje i wydania programu SQL Server są dostępne do zainstalowania na maszynie wirtualnej IaaS. Najbardziej znaczący różnica z bazy danych SQL polega na tym, że maszyny wirtualne SQL Server umożliwia pełną kontrolę nad aparatem bazy danych. Można wybrać podczas konserwacji/poprawianie rozpocznie się, aby zmienić model odzyskiwania prostego lub niepełnym dziennikiem umożliwia szybsze ładowanie mniej dziennika, aby wstrzymać lub uruchomić aparatu w razie potrzeby, i można w pełni dostosować aparatu bazy danych programu SQL Server. Dołączono dodano odpowiedzialność za zarządzanie maszynami wirtualnymi za pomocą tej kontrolki dodatkowe.

Główne różnice między te opcje są wymienione w poniższej tabeli:

| Program SQL Server na maszynie wirtualnej | Zarządzane wystąpienia w bazie danych SQL | Pojedynczą bazę danych / elastycznej puli w bazie danych SQL |
| --- | --- | --- |
|Masz pełną kontrolę nad aparatem programu SQL Server.<br/>Nawet gwarantująca dostępność przez 99,95%.<br/>Pełnej zgodności z zgodną wersję programu SQL Server w środowisku lokalnym.<br/>Wersja aparatu bazy danych stałych, dobrze znane.<br/>Łatwa migracja z programu SQL Server w środowisku lokalnym.<br/>Prywatny adres IP w sieci wirtualnej platformy Azure.<br/>Masz możliwość wdrożenia aplikacji lub usługi na hoście, na którym została umieszczona programu SQL Server.| Wysoką zgodność z programu SQL Server w środowisku lokalnym.<br/>gwarantowana dostępność przez 99,99%.<br/>Wbudowaną funkcję kopii zapasowych, poprawek i odzyskiwania.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Łatwa migracja z programu SQL Server.<br/>Prywatny adres IP w sieci wirtualnej platformy Azure.<br/>Wbudowane zaawansowane analizy i zabezpieczeń.<br/>Zmian zasobów (procesora CPU/storage) w trybie online.|Dostępne są najczęściej używane funkcje programu SQL Server.<br/>gwarantowana dostępność przez 99,99%.<br/>Wbudowaną funkcję kopii zapasowych, poprawek i odzyskiwania.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Możliwość przypisania niezbędne zasoby (procesora CPU/storage) dla pojedynczych baz danych.<br/>Wbudowane zaawansowane analizy i zabezpieczeń.<br/>Zmian zasobów (procesora CPU/storage) w trybie online.|
|Musisz zarządzać kopiami zapasowymi i poprawki.<br>Musisz zaimplementować rozwiązania o wysokiej dostępności.<br/>Brak przestojów podczas zmieniania resources(CPU/storage)|Nadal jest niektóre minimalne liczbę funkcji programu SQL Server, które nie są dostępne.<br/>Nie jest gwarantowana konserwacji dokładnego czasu (ale prawie przezroczysty).<br/>Zgodność wersji programu SQL Server może odbyć się tylko za pomocą poziomów zgodności bazy danych.|Migracja z programu SQL Server może być trudne.<br/>Niektóre funkcje programu SQL Server nie są dostępne.<br/>Nie jest gwarantowana konserwacji dokładnego czasu (ale prawie przezroczysty).<br/>Zgodność wersji programu SQL Server może odbyć się tylko za pomocą poziomów zgodności bazy danych.<br/>Nie można przypisać prywatny adres IP (możesz ograniczyć dostęp, przy użyciu reguł zapory).|

Dowiedz się, jak każdej opcji wdrażania dopasowuje się do platformy danych firmy Microsoft i Uzyskaj pomoc przy dopasowaniu odpowiedniej opcji do wymagań biznesowych. Niezależnie od tego, czy priorytetem jest oszczędność, czy ograniczenie administracji do minimum, artykuł ten może pomóc zdecydować, które rozwiązanie pozwoli spełnić wymagania związane z działalnością biznesową, na których zależy Ci najbardziej.

## <a name="microsofts-sql-data-platform"></a>Platforma danych SQL firmy Microsoft

Podczas każdej dyskusji dotyczącej baz danych Azure w zestawieniu z lokalnymi bazami danych programu SQL Server należy przede wszystkim pamiętać o tym, że można używać ich wszystkich. Platforma danych firmy Microsoft korzysta z technologii programu SQL Server i udostępnia ją na fizycznych komputerach lokalnych, w prywatnych środowiskach chmury, prywatnych środowiskach chmury udostępnianych przez inne firmy i w chmurze publicznej. Program SQL Server w usłudze Azure Virtual Machines umożliwia spełnienie unikatowych i zróżnicowanych potrzeb związanych z prowadzeniem działalności biznesowej poprzez kombinację wdrożeń lokalnych i hostowanych w chmurze, z równoczesnym użyciem w tych środowiskach tego samego zestawu produktów serwerowych, narzędzi projektowych i zakresu wiedzy.

   ![Opcje programu SQL Server w chmurze: Program SQL server w usłudze IaaS lub SQL w usłudze SaaS bazy danych w chmurze.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak widać to na diagramie, każde rozwiązanie można scharakteryzować według posiadanego poziomu administracji względem infrastruktury (na osi X) oraz według stopnia opłacalności wynikającej z konsolidacji poziomu bazy danych i automatyzacji (na osi Y).

Podczas projektowania aplikacji są dostępne cztery podstawowe opcje obsługi części aplikacji programu SQL Server:

- program SQL Server na niezwirtualizowanych komputerach fizycznych,
- program SQL Server na lokalnych komputerach zwirtualizowanych (w chmurze prywatnej),
- program SQL Server na maszynie wirtualnej platformy Azure (w chmurze publicznej firmy Microsoft),
- usługa Azure SQL Database (w chmurze publicznej firmy Microsoft).

W poniższych sekcjach dowiesz się o programie SQL Server w chmurze publicznej firmy Microsoft: Usługa Azure SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure. Ponadto przeanalizujemy typowe czynniki motywujące związane z działalnością biznesową, aby ustalić, która opcja jest najlepsza dla aplikacji.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bliższe spojrzenie na usługę Azure SQL Database i program SQL Server na maszynach wirtualnych platformy Azure

Ogólnie rzecz biorąc, te dwie opcje są zoptymalizowane do różnych celów:

- **Azure SQL Database**

Zoptymalizowana pod kątem ograniczenia ogólne koszty zarządzania do minimum dla inicjowania obsługi administracyjnej i zarządzanie wieloma bazami danych. Obniża bieżące koszty administracyjne, ponieważ nie jest konieczne zarządzanie maszynami wirtualnymi, systemem operacyjnym ani oprogramowaniem bazy danych. Nie wymaga zarządzania uaktualnieniami, wysoką dostępnością ani [kopiami zapasowymi](sql-database-automated-backups.md). Ogólnie rzecz biorąc, usługa Azure SQL Database może znacząco zwiększyć liczbę baz danych zarządzanych przez pojedynczy zasób informatyczny lub projektowy. [Pule elastyczne](sql-database-elastic-pool.md) za pomocą funkcji izolacji dzierżawcy i możliwość skalowania, aby ograniczyć koszty dzięki udostępnieniu zasobów w bazach danych obsługują także architektury wielodostępnej aplikacji SaaS. [Wystąpienie zarządzane](sql-database-managed-instance.md) zapewnia obsługę funkcji należących do zakresu wystąpień, umożliwiając łatwą migrację istniejących aplikacji, jak również udostępnianie zasobów między bazami danych.

- **SQL Server uruchomiony na maszynach wirtualnych platformy Azure**

Zoptymalizowane pod kątem migrowania istniejących aplikacji na platformie Azure lub rozszerzania istniejących aplikacji lokalnych do chmury we wdrożeniach hybrydowych. Ponadto przy użyciu programu SQL Server na maszynie wirtualnej można tworzyć i testować tradycyjne aplikacje programu SQL Server. W przypadku programu SQL Server na maszynach wirtualnych platformy Azure masz pełne uprawnienia administracyjne do dedykowanego wystąpienia programu SQL Server oraz maszyny wirtualnej w chmurze. To doskonałe rozwiązanie, gdy firma ma już dostępne zasoby informatyczne do obsługi maszyn wirtualnych. Te funkcje umożliwiają stworzenie doskonale dopasowanego systemu, który spełni określone wymagania aplikacji związane z wydajnością i dostępnością.

Poniższa tabela zawiera podsumowanie głównych cech usługi SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure:

| | Pojedyncze bazy danych SQL Database i pul elastycznych | Wystąpienia zarządzane SQL Database |Maszyny wirtualne platformy Azure z programem SQL Server |
| --- | --- | --- |---|
| **Najlepsze dla:** |Nowe aplikacje zaprojektowane dla chmury, które chcesz korzystać z najnowszych funkcji programu SQL Server stabilne i mają ograniczenia czasowe w zakresie projektowania i marketingu. | Nowe aplikacje lub istniejących aplikacji lokalnych, które ma być używana najnowsza stabilna funkcje programu SQL Server i które są migrowane do chmury przy minimalnych zmianach.  | Istniejące aplikacje, które wymagają szybkiej migracji do chmury przy minimalnych zmianach lub jedynie minimalnych zmianach. Scenariusze szybkiego tworzenia i testowania, gdy nie chcesz kupować lokalnego nieprodukcyjnego sprzętu dla programu SQL Server. |
|  | Zespoły, które wymagają wbudowanej wysokiej dostępności, odzyskiwania po awarii i uaktualniania bazy danych. | Taka sama jak bazy danych SQL pojedynczych i puli baz danych. | Zespoły, które można skonfigurować prawidłowo dostrajania, dostosowywanie i zarządzanie wysokiej dostępności, odzyskiwania po awarii i stosowanie poprawek dla programu SQL Server. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. |
|  | Zespoły, które nie chcą zarządzać podstawowymi ustawieniami systemu operacyjnego i konfiguracji. | Taka sama jak bazy danych SQL pojedynczych i puli baz danych. | Potrzebne jest dostosowane środowisko z pełnymi prawami administracyjnymi. |
|  | Bazy danych o rozmiarze do 100 TB. | Do 8 TB. | Wystąpienia programu SQL Server z maksymalnie 64 TB przestrzeni dyskowej. Wystąpienie może obsługiwać dowolną liczbę baz danych zależnie od potrzeb. |
| **Zgodność** | Obsługuje większość funkcji poziomu bazy danych w środowisku lokalnym. | Obsługuje prawie wszystkie lokalne funkcje na poziomie wystąpienia i poziomu bazy danych. | Obsługuje wszystkie funkcje w środowisku lokalnym. |
| **Zasoby:** | Czy chcesz używać zasobów IT do konfiguracji i zarządzania podstawową infrastrukturą, ale chcesz skupić się na warstwie aplikacji. | Taka sama jak bazy danych SQL pojedynczych i puli baz danych. | Masz niektóre zasoby informatyczne do konfiguracji i zarządzania. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. |
| **Całkowity koszt posiadania:** | Eliminuje koszty sprzętu i ogranicza koszty administracyjne. | Taka sama jak bazy danych SQL pojedynczych i puli baz danych. | Eliminuje koszty sprzętu. |
| **Ciągłość działalności biznesowej:** |Oprócz [wbudowaną odporność na uszkodzenia infrastruktury możliwości](sql-database-high-availability.md), Azure SQL Database oferuje funkcje, takie jak [automatyczne kopie zapasowe](sql-database-automated-backups.md), [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore), [geoprzywracanie](sql-database-recovery-using-backups.md#geo-restore), [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md), i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) Aby zwiększyć ciągłość prowadzenia działalności biznesowej. Więcej informacji znajduje się w temacie [Omówienie ciągłości działalności biznesowej usługi SQL Database](sql-database-business-continuity.md). | Dostępne są takie same jak bazy danych SQL pojedynczych i puli baz danych, a także inicjowane przez użytkownika, tylko do kopiowania kopii zapasowych. | Program SQL Server na maszynach wirtualnych platformy Azure umożliwia skonfigurowanie rozwiązania wysokiej dostępności i odzyskiwania po awarii w zależności od określonych potrzeb bazy danych. Można mieć zatem system w znacznym stopniu zoptymalizowany dla aplikacji. Jeśli wystąpi taka potrzeba, tryb failover można przetestować i uruchomić samodzielnie. Więcej informacji znajduje się w temacie [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w usłudze Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Chmura hybrydowa:** |Aplikacja lokalna może uzyskiwać dostęp do danych w usłudze Azure SQL Database. | [Implementacja natywnych sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md) i łączności do środowiska lokalnego przy użyciu usługi Azure Expressroute lub bram sieci VPN. | Program SQL Server na maszynach wirtualnych platformy Azure może obejmować aplikacje, które działają częściowo w chmurze i częściowo lokalnie. Można na przykład rozszerzyć sieć lokalną i domenę usługi Active Directory do chmury za pośrednictwem usługi [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Aby uzyskać więcej informacji na temat hybrydowych rozwiązań w chmurze, zobacz [rozszerzanie lokalnych rozwiązań dotyczących danych do chmury](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Obsługuje [replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako subskrybent replikowanych danych. | Replikacja jest obsługiwana dla wystąpienia zarządzanego jako funkcja w wersji zapoznawczej. | W pełni obsługuje [replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [zawsze włączonych grup dostępności](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), usługi integracji i wysyłanie dzienników do replikacji danych. Ponadto w pełni obsługiwane są tradycyjne kopie zapasowe programu SQL Server. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Względy biznesowe przemawiające za wyborem usługi Azure SQL Database lub programu SQL Server na maszynach wirtualnych Azure

Istnieje kilka czynników, które mogą mieć wpływ na Twoją decyzję, aby wybrać PaaS lub IaaS do hostowania bazy danych SQL:

- [Koszt](#cost) — opcja zarówno PaaS i IaaS obejmować podstawowej ceny określające podstawową infrastrukturę i Licencjonowanie. Jednak przy użyciu opcji IaaS należy zainwestować dodatkowy czas i zasoby umożliwiające zarządzanie bazą danych w przypadku, gdy w modelu PaaS otrzymujesz tych funkcji administrowania uwzględnione w cenie. Opcja IaaS umożliwia zamknięcie Twoich zasobów, gdy nie używasz ich do zmniejszenia kosztów, gdy zawsze działa PaaS w wersji, chyba że jeśli porzucać i odtwarzać swoje zasoby, gdy są potrzebne.
- [Administracja](#administration) — opcje PaaS Zmniejsz ilość czasu, który należy zainwestować zarządzać bazą danych. Jednak ogranicza różnych zadań administracyjnych niestandardowy i skrypty, które można wykonać lub uruchomić. Na przykład środowisko CLR jest nieobsługiwane w przypadku pojedynczego lub pulami baz danych, ale jest obsługiwany w przypadku wystąpienia zarządzanego. Brak opcji wdrażania w modelu PaaS obsługują także, użycie flagi śledzenia.
- [Umowa dotycząca poziomu usług](#service-level-agreement-sla) — zarówno IaaS i PaaS zapewnienia wysokiego, branżowy standard umowy SLA. Opcja PaaS gwarantuje SLA na poziomie 99,99%, gdy IaaS gwarancji SLA 99,95% dla infrastruktury, co oznacza, należy wdrożyć dodatkowe mechanizmy, aby zapewnić dostępność bazy danych. W skrajnych przypadkach, jeśli chcesz zaimplementować rozwiązania o wysokiej dostępności, który jest zgodny PaaS, konieczne może być tworzenie dodatkowych programu SQL Server na maszynie Wirtualnej i konfigurowanie grup dostępności AlwaysOn, które może być double koszt bazy danych.
- [Czas na przeniesienie na platformę Azure](#market) -programu SQL Server na maszynie Wirtualnej platformy Azure jest dokładne dopasowanie środowiska, dlatego migracji ze środowiska lokalnego do maszyny Wirtualnej SQL Azure nie jest inny niż przeniesienie baz danych z jednego na serwerze lokalnym do innego. Wystąpienie zarządzane umożliwia również bardzo łatwa migracja; jednak mogą istnieć pewne zmiany, które należy zastosować przed przeprowadzeniem migracji do wystąpienia zarządzanego.

Czynniki te zostaną omówione bardziej szczegółowo w poniższych sekcjach.

### <a name="cost"></a>Koszty

Ograniczone fundusze są często podstawowym czynnikiem decydującym o wyborze sposobu hostowania baz danych — niezależnie od tego, czy Twoja firma to start-up o ograniczonych środkach finansowych, czy należysz do zespołu firmowego, który musi zmieścić się w zaplanowanym budżecie. W tej sekcji poznasz informacje dotyczące rozliczeń i licencjonowania platformy Azure w odniesieniu do tych dwóch opcji relacyjnej bazy danych: SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure. Poznasz również sposób obliczania całkowitego kosztu aplikacji.

#### <a name="billing-and-licensing-basics"></a>Podstawowe informacje dotyczące rozliczeń i licencjonowania

Obecnie **bazy danych SQL** jest oferowana jako usługa i jest dostępnych kilka opcji wdrażania i w kilku warstwach usług z cenami różnych zasobów, z których wszystkie są naliczane godzinowo według stałej stawki ustalanej na podstawie warstwy usługi i obliczenia rozmiaru. Aby uzyskać najnowsze informacje dotyczące bieżących obsługiwanych warstw usług można obliczyć rozmiary i ilości pamięci masowej, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

- Za pomocą pojedynczej bazy danych SQL Database możesz wybrać warstwę usług, który spełnia Twoje potrzeby z szerokiej gamy ceny, zaczynając od 5$ / miesiąc dla warstwy podstawowa.
- Możesz utworzyć [pul elastycznych](sql-database-elastic-pool.md) współużytkowanie zasobów między wystąpieniami bazy danych do zmniejszenia kosztów i obsłużyć obciążenie nagłego zapotrzebowania.
- Wystąpienie zarządzane usługi SQL Database możesz także dostarczyć własną licencję. Aby uzyskać więcej informacji o licencjonowaniu bring your own, zobacz [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/) lub użyj [Kalkulator korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) aby zobaczyć, jak **Zaoszczędź do 40%** .

Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/). Możesz dynamicznie dostosować warstwy usług i obliczenia rozmiarów odpowiednio do potrzeb przepływności zależeć od Twojej aplikacji.

W przypadku usługi **SQL Database** oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez firmę Microsoft, co obniża koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](sql-database-automated-backups.md) pozwala osiągnąć znaczne oszczędności kosztów, zwłaszcza w przypadku dużej liczby baz danych.

Dzięki **programowi SQL Server na maszynach wirtualnych platformy Azure** można używać udostępnianego na platformie obrazu programu SQL Server (który obejmuje licencję) lub przenieść swoją licencję programu SQL Server. Dostępne są wszystkie obsługiwane wersje programu SQL Server (2008R2, 2012, 2014, 2016) i edycje (Developer, Express, Web, Standard, Enterprise). Ponadto są dostępne wersje bring-your-own-license (BYOL) obrazów. W przypadku użycia obrazów dostarczanych przez platformę Azure koszty operacyjne zależą od rozmiaru maszyny wirtualnej, a także od wybranej wersji programu SQL Server. Niezależnie od rozmiaru maszyny Wirtualnej lub wersji programu SQL Server naliczana jest opłata za minutę licencjonowania programu SQL Server i Windows lub Linux Server wraz z kosztami usługi Azure Storage dla dysków maszyny Wirtualnej. Opcja rozliczania co minutę pozwala używać programu SQL Server tak długo, jak jest to konieczne bez wykupywania dodatkowych licencji programu SQL Server. W przypadku przeniesienia licencji programu SQL Server na platformie Azure, opłaty są naliczane dla serwera i tylko koszty magazynowania. Więcej informacji na temat przenoszenia własnej licencji można znaleźć w temacie [Przenośność licencji za pośrednictwem programu Software Assurance w systemie Azure](https://azure.microsoft.com/pricing/license-mobility/). Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Obliczanie całkowitego kosztu aplikacji

Po uruchomieniu przy użyciu platformy w chmurze, koszt działania aplikacji obejmuje koszty w przypadku nowych wdrożeń i bieżące koszty administracyjne, a także koszty usługi chmury publicznej platformy.

**W przypadku korzystania z usługi Azure SQL Database:**

- Znacznie ograniczone koszty administracyjne
- Koszty rozwoju ograniczone dla migrowanych aplikacji (wystąpienia zarządzanego)
- Koszty usługi SQL Database
- Żadnych kosztów zakupu sprzętu

**W przypadku korzystania z programu SQL Server na maszynach wirtualnych platformy Azure:**

- Wyższe koszty administracyjne
- Ograniczone do żadnych kosztów programowania dla migrowanych aplikacji
- Koszty usługi maszyny wirtualnej
- Żadnych kosztów zakupu sprzętu

Więcej informacji na temat cen zawierają następujące zasoby:

- [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/) dla [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

Dla wielu firm decyzja o przeniesieniu usług do usługi w chmurze wiąże się zarówno z ograniczeniem złożoności w zakresie administracji, jak i obniżeniem kosztów. Przy użyciu rozwiązań IaaS i PaaS Microsoft administruje podstawowej infrastruktury i automatycznie replikuje wszystkie dane w celu zapewniania odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i obsługuje tryb failover, jeśli istnieje Błąd serwera w centrum danych.

- Za pomocą **usługi Azure SQL Database**, możesz administrować swoją bazą danych, ale nie trzeba zarządzać aparatem bazy danych, systemu operacyjnego lub sprzętu. Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia. Ponadto skonfigurowanie wysokiej dostępności do innego centrum danych wymaga minimalnej konfiguracji i administrowania.
- **Program SQL Server na maszynach wirtualnych platformy Azure** daje pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia programu SQL Server. W przypadku korzystania z maszyny wirtualnej decydujesz o tym, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych i kiedy zainstalować dodatkowe oprogramowanie, np. oprogramowanie antywirusowe. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Platforma Azure umożliwia zmianę rozmiaru maszyny wirtualnej zgodnie z zapotrzebowaniem. Więcej informacji można znaleźć w temacie [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Rozmiary maszyn wirtualnych i usług w chmurze na platformie Azure).

### <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)

Dla wielu działów IT wypełnienie zobowiązań wynikających z umowy dotyczącej poziomu usług (SLA) ma najwyższy priorytet. W tej sekcji opisano, jakie warunki umowy SLA stosuje się do poszczególnych opcji obsługi bazy danych.

Aby uzyskać **bazy danych SQL**, firma Microsoft oferuje umowę SLA ZAPEWNIAJĄCĄ dostępność przez 99,99% czasu. Najnowsze informacje można znaleźć w artykule [SQL Database — umowa SLA](https://azure.microsoft.com/support/legal/sla/sql-database/).

W przypadku **programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure** firma Microsoft zapewnia dostępność na poziomie 99,95%, która obejmuje tylko maszyny wirtualne. Umowa SLA nie obejmuje procesów (np. programu SQL Server) uruchomionych na maszynie wirtualnej i wymaga obsługi przynajmniej dwóch wystąpień maszyny wirtualnej w zbiorze dostępności. Najnowsze informacje znajdują się w artykule [Maszyny wirtualne — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla bazy danych o wysokiej dostępności (HA) w ramach maszyn wirtualnych, należy skonfigurować jedną z obsługiwanych opcji wysokiej dostępności w programie SQL Server, takich jak [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Użycie obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia osiągnięcie dostępności bazy danych na poziomie > 99,99%.

### <a name="market"></a>Czas na przeniesienie na platformę Azure

**Pojedyncze bazy danych SQL Database lub pul elastycznych** są odpowiednim rozwiązaniem dla aplikacji projektowanych w chmurze, gdy wydajność deweloperów i krótki czas — na rynek nowe rozwiązania mają kluczowe znaczenie. Dzięki funkcjonalności przypominającej model DBA jest doskonała dla architektów i deweloperów chmury, ponieważ zmniejsza potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

**Wystąpienie zarządzane bazy danych SQL** znacznie upraszcza migrację istniejących aplikacji do usługi Azure SQL Database, co umożliwia przenoszenie aplikacji zmigrowana baza danych, aby szybko wprowadzać na rynek na platformie Azure.

**Program SQL Server działający na maszynach wirtualnych Azure** jest doskonałym rozwiązaniem, jeśli istniejące lub nowe aplikacje wymagają dużych baz danych lub uzyskać dostęp do wszystkich funkcji programu SQL Server lub Windows/Linux i chcesz uniknąć czasu i pieniędzy uzyskiwanie nowego sprzętu lokalnego. Jest również odpowiedni, jeśli chcesz przeprowadzić migrację istniejących lokalnych aplikacji i baz danych na platformie Azure jako-to - w przypadkach, gdy wystąpienie zarządzane usługi Azure SQL Database jest dobrym rozwiązaniem. Ponieważ nie trzeba zmieniać prezentacji, aplikacji i warstwy danych, oszczędność czasu i budżetu na ponownego projektowania istniejącego rozwiązania. Zamiast tego możesz skoncentrować się na migracji wszystkich rozwiązań do platformy Azure i przeprowadzeniu optymalizacji wydajności, których może wymagać platforma Azure. Więcej informacje zawiera artykuł [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Najlepsze praktyki dotyczące wydajności dla programu SQL Server w usłudze Azure Virtual Machines).

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę z usługą SQL Database, zobacz [Your first Azure SQL Database](sql-database-single-database-get-started.md) (Twoja pierwsza baza danych Azure SQL Database).
- Zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Aby rozpocząć pracę z programem SQL Server na maszynach wirtualnych platformy Azure, zobacz temat [Aprowizowanie maszyny wirtualnej programu SQL Server w Portalu Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
