---
title: Wybór spośród opcji SQL na platformie Azure | Microsoft Docs
description: Dowiedz się, jak wybierać spośród opcji wdrażania w Azure SQL Database i między Azure SQL Database i SQL Server na maszynach wirtualnych platformy Azure
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
ms.date: 03/11/2019
ms.openlocfilehash: fc0204e774e66e9304887f47377a102df1b828cb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884327"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Wybierz odpowiednią opcję wdrożenia w usłudze Azure SQL

Na platformie Azure możesz mieć SQL Server obciążenia działające w hostowanej infrastrukturze (IaaS) lub działającej jako usługa hostowana ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). W ramach programu PaaS masz wiele opcji wdrażania i warstw usług w ramach każdej opcji wdrażania. Kluczowym pytaniem, które należy zadać w przypadku podejmowania decyzji między PaaS lub IaaS, jest zarządzanie bazą danych, stosowanie poprawek, tworzenie kopii zapasowych lub delegowanie tych operacji na platformę Azure?
W zależności od odpowiedzi dostępne są następujące opcje:

- [Azure SQL Database](sql-database-technical-overview.md): W pełni zarządzany aparat bazy danych SQL w oparciu o najnowszą stabilną wersję Enterprise SQL Server. Jest to relacyjna baza danych jako usługa (DBaaS) hostowana w chmurze platformy Azure, która znajduje się w kategorii branżowej *platformy jako usługi (PaaS)* . Baza danych SQL ma wiele opcji wdrażania, z których każdy jest oparty na ustandaryzowanym sprzęcie i oprogramowaniu, które jest własnością, hostowana i obsługiwana przez firmę Microsoft. Za pomocą SQL Database można używać wbudowanych funkcji i funkcji, które wymagają obszernej konfiguracji, gdy są używane w SQL Server (lokalnie lub na maszynie wirtualnej platformy Azure). Płatność za korzystanie z usługi SQL Database odbywa się zgodnie z rzeczywistym użyciem, a opcje pozwalają na skalowanie w górę lub w poziomie, dzięki czemu klienci mają do dyspozycji większe możliwości bez najmniejszych zakłóceń. SQL Database oferuje dodatkowe funkcje, które nie są dostępne w SQL Server, takie jak wbudowana wysoka dostępność, analiza i zarządzanie. Azure SQL Database oferuje następujące opcje wdrażania:
  
  - Jako [pojedyncza baza danych](sql-database-single-database.md) z własnym zestawem zasobów zarządzanych za pośrednictwem serwera SQL Database. Pojedyncza baza danych jest podobna do [zawartych baz danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) w SQL Server. Ta opcja jest zoptymalizowana pod kątem nowoczesnego opracowywania nowych aplikacji w chmurze.
  - [Pula elastyczna](sql-database-elastic-pool.md), która jest kolekcją baz danych ze współdzielonym zestawem zasobów zarządzanych za pośrednictwem serwera SQL Database. Pojedyncze bazy danych można przenieść do i z puli elastycznej. Ta opcja jest zoptymalizowana pod kątem nowoczesnego opracowywania nowych aplikacji w chmurze przy użyciu wielodostępnej aplikacji SaaS.
  - [Wystąpienie zarządzane](sql-database-managed-instance.md), które jest kolekcją baz danych systemu i użytkowników z współdzielonym zestawem zasobów. Wystąpienie zarządzane jest podobne do wystąpienia programu [Microsoft SQL Server Database Engine] oferującego udostępnione zasoby dla baz danych i dodatkowych funkcji o zakresie wystąpienia. Wystąpienie zarządzane obsługuje migrację bazy danych z lokalizacji lokalnej z minimalnym brakiem zmian w bazie danych. Ta opcja zapewnia wszystkie zalety PaaS Azure SQL Database ale dodaje funkcje, które były wcześniej dostępne tylko na maszynach wirtualnych SQL. Obejmuje to natywną sieć wirtualną (VNet) i niemal 100% zgodności z lokalnymi SQL Server.
- [SQL Server na platformie azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) znajduje się w kategorii branżowej *infrastruktura jako usługa (IaaS)* i umożliwia uruchamianie SQL Server wewnątrz w pełni zarządzanej maszyny wirtualnej w chmurze platformy Azure. [SQL Server maszyny wirtualne](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) działają również na znormalizowanym sprzęcie, który jest własnością, hostowana i obsługiwana przez firmę Microsoft. W przypadku korzystania z SQL Server na maszynie wirtualnej możesz wybrać opcję płatność zgodnie z rzeczywistym użyciem, aby uzyskać SQL Server licencję już dołączoną do obrazu SQL Server lub łatwo korzystać z istniejącej licencji. Możesz również zatrzymać lub wznowić maszynę wirtualną zgodnie z wymaganiami. SQL Server zainstalowane i hostowane w chmurze na maszynach wirtualnych z systemem Windows Server lub Linux działających na platformie Azure, znanych także jako infrastruktura jako usługa (IaaS). SQL Server w usłudze Azure Virtual Machines jest dobrym rozwiązaniem w przypadku migrowania lokalnych baz danych SQL Server i aplikacji bez żadnej zmiany w bazie danych. Wszystkie najnowsze wersje SQL Server są dostępne do zainstalowania na maszynie wirtualnej IaaS. Najbardziej znaczącą różnicą od SQL Database jest to, że SQL Server maszyny wirtualne umożliwiają pełną kontrolę nad aparatem bazy danych. Możesz wybrać, kiedy należy rozpocząć konserwację/stosowanie poprawek, zmienić model odzyskiwania na prosty lub zbiorczo-zarejestrowany, wstrzymać lub uruchomić usługę w razie potrzeby i można w pełni dostosować aparat bazy danych SQL Server. Dodatkowa kontrola obejmuje dodanie odpowiedzialności za zarządzanie maszyną wirtualną.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

| Program SQL Server na maszynie wirtualnej | Wystąpienie zarządzane w SQL Database | Pojedyncza baza danych/Pula elastyczna w SQL Database |
| --- | --- | --- |
|Masz pełną kontrolę nad aparatem SQL Server.<br/>Do 99,99% czasu dostępności.<br/>Pełna zgodność ze zgodną wersją SQL Server lokalnego.<br/>Stała, dobrze znana wersja aparatu bazy danych.<br/>Łatwa migracja z SQL Server lokalnych.<br/>Prywatny adres IP w sieci wirtualnej platformy Azure.<br/>Można wdrożyć aplikacje lub usługi na hoście, na którym znajduje się SQL Server.| Wysoka zgodność z SQL Server lokalnie.<br/>dostępność na 99,99%.<br/>Wbudowane kopie zapasowe, poprawki, odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Łatwa migracja z SQL Server.<br/>Prywatny adres IP w sieci wirtualnej platformy Azure.<br/>Wbudowana Zaawansowana analiza i zabezpieczenia.<br/>Online zmiana zasobów (procesor CPU/magazyn).|Dostępne są najczęściej używane funkcje SQL Server.<br/>dostępność na 99,99%.<br/>Wbudowane kopie zapasowe, poprawki, odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Możliwość przypisywania niezbędnych zasobów (procesor CPU/magazyn) do poszczególnych baz danych.<br/>Wbudowana Zaawansowana analiza i zabezpieczenia.<br/>Online zmiana zasobów (procesor CPU/magazyn).|
|Musisz zarządzać kopiami zapasowymi i poprawkami.<br>Musisz zaimplementować własne rozwiązanie wysokiej dostępności.<br/>Podczas zmiany zasobów (procesor CPU/magazyn) występuje przestój|Nadal istnieje pewna minimalna liczba funkcji SQL Server, które nie są dostępne.<br/>Bez gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczyste).<br/>Zgodność z wersją SQL Server można uzyskać tylko przy użyciu poziomów zgodności bazy danych.|Migracja z SQL Server może być trudna.<br/>Niektóre funkcje SQL Server są niedostępne.<br/>Bez gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczyste).<br/>Zgodność z wersją SQL Server można uzyskać tylko przy użyciu poziomów zgodności bazy danych.<br/>Nie można przypisać prywatnego adresu IP (można ograniczyć dostęp za pomocą reguł zapory).|

Dowiedz się, w jaki sposób każda opcja wdrażania mieści się w platformie danych firmy Microsoft i uzyskaj pomoc dotyczącą odpowiedniej opcji w wymaganiach firmy. Niezależnie od tego, czy priorytetem jest oszczędność, czy ograniczenie administracji do minimum, artykuł ten może pomóc zdecydować, które rozwiązanie pozwoli spełnić wymagania związane z działalnością biznesową, na których zależy Ci najbardziej.

## <a name="microsofts-sql-data-platform"></a>Platforma danych SQL firmy Microsoft

Podczas każdej dyskusji dotyczącej baz danych Azure w zestawieniu z lokalnymi bazami danych programu SQL Server należy przede wszystkim pamiętać o tym, że można używać ich wszystkich. Platforma danych firmy Microsoft korzysta z technologii programu SQL Server i udostępnia ją na fizycznych komputerach lokalnych, w prywatnych środowiskach chmury, prywatnych środowiskach chmury udostępnianych przez inne firmy i w chmurze publicznej. Program SQL Server w usłudze Azure Virtual Machines umożliwia spełnienie unikatowych i zróżnicowanych potrzeb związanych z prowadzeniem działalności biznesowej poprzez kombinację wdrożeń lokalnych i hostowanych w chmurze, z równoczesnym użyciem w tych środowiskach tego samego zestawu produktów serwerowych, narzędzi projektowych i zakresu wiedzy.

   ![Opcje SQL Server w chmurze: Program SQL Server w systemie IaaS lub SaaS usługi SQL Database w chmurze.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak widać to na diagramie, każde rozwiązanie można scharakteryzować według posiadanego poziomu administracji względem infrastruktury (na osi X) oraz według stopnia opłacalności wynikającej z konsolidacji poziomu bazy danych i automatyzacji (na osi Y).

Podczas projektowania aplikacji są dostępne cztery podstawowe opcje obsługi części aplikacji programu SQL Server:

- program SQL Server na niezwirtualizowanych komputerach fizycznych,
- program SQL Server na lokalnych komputerach zwirtualizowanych (w chmurze prywatnej),
- program SQL Server na maszynie wirtualnej platformy Azure (w chmurze publicznej firmy Microsoft),
- usługa Azure SQL Database (w chmurze publicznej firmy Microsoft).

W poniższych sekcjach znajdziesz informacje na temat SQL Server w chmurze publicznej firmy Microsoft: Azure SQL Database i SQL Server na maszynach wirtualnych platformy Azure. Ponadto przeanalizujemy typowe czynniki motywujące związane z działalnością biznesową, aby ustalić, która opcja jest najlepsza dla aplikacji.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bliższe spojrzenie na usługę Azure SQL Database i program SQL Server na maszynach wirtualnych platformy Azure

Ogólnie rzecz biorąc, te dwie opcje są zoptymalizowane do różnych celów:

- **Azure SQL Database**

Zoptymalizowany pod kątem zmniejszenia ogólnych kosztów zarządzania do minimum dla aprowizacji i zarządzania wieloma bazami danych. Obniża bieżące koszty administracyjne, ponieważ nie jest konieczne zarządzanie maszynami wirtualnymi, systemem operacyjnym ani oprogramowaniem bazy danych. Nie wymaga zarządzania uaktualnieniami, wysoką dostępnością ani [kopiami zapasowymi](sql-database-automated-backups.md). Ogólnie rzecz biorąc, usługa Azure SQL Database może znacząco zwiększyć liczbę baz danych zarządzanych przez pojedynczy zasób informatyczny lub projektowy. [Pule elastyczne](sql-database-elastic-pool.md) obsługują również architekturę aplikacji z wieloma dzierżawcami z funkcjami obejmującymi izolację dzierżawy i możliwość skalowania w celu obniżenia kosztów dzięki udostępnieniu zasobów między bazami danych. [Wystąpienie zarządzane](sql-database-managed-instance.md) zapewnia obsługę funkcji z zakresem wystąpienia, co umożliwia łatwą migrację istniejących aplikacji, a także udostępnianie zasobów między bazami danych.

- **SQL Server uruchomione na maszynach wirtualnych platformy Azure**

Optymalizacja pod kątem migrowania istniejących aplikacji na platformę Azure lub rozszerzania istniejących aplikacji lokalnych do chmury w ramach wdrożeń hybrydowych. Ponadto przy użyciu programu SQL Server na maszynie wirtualnej można tworzyć i testować tradycyjne aplikacje programu SQL Server. W przypadku programu SQL Server na maszynach wirtualnych platformy Azure masz pełne uprawnienia administracyjne do dedykowanego wystąpienia programu SQL Server oraz maszyny wirtualnej w chmurze. To doskonałe rozwiązanie, gdy firma ma już dostępne zasoby informatyczne do obsługi maszyn wirtualnych. Te funkcje umożliwiają stworzenie doskonale dopasowanego systemu, który spełni określone wymagania aplikacji związane z wydajnością i dostępnością.

Poniższa tabela zawiera podsumowanie głównych cech usługi SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure:

| | SQL Database pojedynczych baz danych i pul elastycznych | SQL Database wystąpienia zarządzane |Usługa Azure Virtual Machines z SQL Server |
| --- | --- | --- |---|
| **Najlepsze dla:** |Nowe aplikacje zaprojektowane w chmurze, które chcą używać najnowszych stabilnych funkcji SQL Server oraz ograniczeń czasowych w programowaniu i marketingu. | Nowe aplikacje lub istniejące aplikacje lokalne, które chcą używać najnowszych stabilnych funkcji SQL Server i są migrowane do chmury przy minimalnych zmianach.  | Istniejące aplikacje, które wymagają szybkiej migracji do chmury z minimalnymi zmianami lub bez zmian. Scenariusze szybkiego tworzenia i testowania, gdy nie chcesz kupować lokalnego nieprodukcyjnego sprzętu dla programu SQL Server. |
|  | Zespoły, które wymagają wbudowanej wysokiej dostępności, odzyskiwania po awarii i uaktualniania bazy danych. | Taka sama jak SQL Database baz danych z pojedynczą i pulą. | Zespoły, które mogą konfigurować, dostrajać, dostosowywać i zarządzać wysoką dostępnością, odzyskiwaniem po awarii oraz poprawek dla SQL Server. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. |
|  | Zespoły, które nie chcą zarządzać podstawowymi ustawieniami systemu operacyjnego i konfiguracji. | Taka sama jak SQL Database baz danych z pojedynczą i pulą. | Potrzebujesz niestandardowego środowiska z pełnymi prawami administracyjnymi. |
|  | Bazy danych o pojemności do 100 TB. | Do 8 TB. | SQL Server wystąpienia o pojemności do 256 TB magazynu. Wystąpienie może obsługiwać dowolną liczbę baz danych zależnie od potrzeb. |
| **Zgodna** | Obsługa większości lokalnych możliwości na poziomie bazy danych. | Obsługuje niemal wszystkie funkcje na poziomie wystąpienia lokalnego i na poziomie bazy danych. | Obsługuje wszystkie możliwości lokalne. |
| **Zasoby:** | Nie chcesz używać zasobów IT do konfiguracji i zarządzania podstawową infrastrukturą, ale chcesz skupić się na warstwie aplikacji. | Taka sama jak SQL Database baz danych z pojedynczą i pulą. | Masz niektóre zasoby informatyczne do konfiguracji i zarządzania. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. |
| **Całkowity koszt posiadania:** | Eliminuje koszty sprzętu i ogranicza koszty administracyjne. | Taka sama jak SQL Database baz danych z pojedynczą i pulą. | Eliminuje koszty sprzętu. |
| **Ciągłość działalności biznesowej:** |Oprócz [wbudowanych funkcji infrastruktury](sql-database-high-availability.md)odporności na uszkodzenia Azure SQL Database udostępnia funkcje, takie jak [zautomatyzowane kopie zapasowe](sql-database-automated-backups.md), [przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore), [przywracanie geograficzne](sql-database-recovery-using-backups.md#geo-restore), [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md)i [ Grupy autopraca awaryjna](sql-database-auto-failover-group.md) , aby zwiększyć ciągłość działania firmy. Więcej informacji znajduje się w temacie [Omówienie ciągłości działalności biznesowej usługi SQL Database](sql-database-business-continuity.md). | Taka sama jak SQL Database baz danych z pojedynczą i pulą, a także inicjowane przez użytkownika kopie zapasowe, które są dostępne tylko do kopiowania. | Program SQL Server na maszynach wirtualnych platformy Azure umożliwia skonfigurowanie rozwiązania wysokiej dostępności i odzyskiwania po awarii w zależności od określonych potrzeb bazy danych. Można mieć zatem system w znacznym stopniu zoptymalizowany dla aplikacji. Jeśli wystąpi taka potrzeba, tryb failover można przetestować i uruchomić samodzielnie. Więcej informacji znajduje się w temacie [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w usłudze Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Chmura hybrydowa:** |Aplikacja lokalna może uzyskiwać dostęp do danych w usłudze Azure SQL Database. | [Implementacja natywnej sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md) i łączność ze środowiskiem lokalnym za pomocą usługi Azure Express Route lub VPN Gateway. | Program SQL Server na maszynach wirtualnych platformy Azure może obejmować aplikacje, które działają częściowo w chmurze i częściowo lokalnie. Można na przykład rozszerzyć sieć lokalną i domenę usługi Active Directory do chmury za pośrednictwem usługi [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Aby uzyskać więcej informacji na temat rozwiązań w chmurze hybrydowej, zobacz [rozszerzanie lokalnych rozwiązań danych do chmury](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Obsługuje [replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako subskrybent replikowanych danych. | Replikacja jest obsługiwana w przypadku wystąpienia zarządzanego jako funkcji w wersji zapoznawczej. | W pełni obsługuje [SQL Server replikację transakcyjną](https://msdn.microsoft.com/library/mt589530.aspx), [zawsze włączone grupy dostępności](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), usługi integracji i wysyłanie dziennika w celu replikowania danych. Ponadto w pełni obsługiwane są tradycyjne kopie zapasowe programu SQL Server. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Względy biznesowe przemawiające za wyborem usługi Azure SQL Database lub programu SQL Server na maszynach wirtualnych Azure

Istnieje kilka czynników, które mogą mieć wpływ na decyzję o wyborze PaaS lub IaaS do hostowania baz danych SQL:

- [Koszt](#cost) — zarówno opcja PaaS, jak i IaaS obejmują podstawową cenę obejmującą podstawową infrastrukturę i Licencjonowanie. Jednak w przypadku opcji IaaS należy zainwestować dodatkowy czas i zasoby w celu zarządzania bazą danych, a w PaaS są dostępne te funkcje administracyjne. Opcja IaaS umożliwia zamknięcie zasobów, gdy nie są używane w celu obniżenia kosztów, podczas gdy wersja PaaS jest zawsze uruchomiona, chyba że zostaną usunięte i ponownie utworzone zasoby, gdy są potrzebne.
- Opcja [Administration](#administration) -PaaS zmniejsza ilość czasu potrzebną do zainwestowania w celu administrowania bazą danych. Jednak ogranicza także zakres niestandardowych zadań administracyjnych i skryptów, które można wykonać lub uruchomić. Na przykład środowisko CLR nie jest obsługiwane w przypadku baz danych z pojedynczym lub pulą, ale jest obsługiwane dla wystąpienia zarządzanego. Ponadto żadne opcje wdrażania w programie PaaS nie obsługują używania flag śledzenia.
- [Umowa dotycząca poziomu usług](#service-level-agreement-sla) — zarówno IaaS, jak i PaaS zapewniają wysoką, branżową umowę SLA. Opcja PaaS gwarantuje dostępność na 99,99% umowy SLA, podczas gdy IaaS gwarantuje gwarancję zgodności z 99,95% dla infrastruktury, co oznacza, że konieczne jest zaimplementowanie dodatkowych mechanizmów w celu zapewnienia dostępności baz danych. Rozwiązanie o wysokiej dostępności można zaimplementować w 99,99%, tworząc dodatkowe SQL Server w maszynie wirtualnej i konfigurując zawsze włączone grupy dostępności.
- [Czas przejścia na platformę Azure](#market) — SQL Server na maszynie wirtualnej platformy Azure to dokładne dopasowanie środowiska, dlatego migracja z lokalizacji lokalnej do maszyny wirtualnej Azure SQL nie różni się od przenoszenia baz danych z jednego serwera lokalnego na inny. Wystąpienie zarządzane również umożliwia bardzo łatwą migrację. mogą jednak wystąpić pewne zmiany, które należy zastosować przed migracją do wystąpienia zarządzanego.

Te czynniki zostaną omówione bardziej szczegółowo w poniższych sekcjach.

### <a name="cost"></a>Koszt

Ograniczone fundusze są często podstawowym czynnikiem decydującym o wyborze sposobu hostowania baz danych — niezależnie od tego, czy Twoja firma to start-up o ograniczonych środkach finansowych, czy należysz do zespołu firmowego, który musi zmieścić się w zaplanowanym budżecie. W tej sekcji przedstawiono podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure w odniesieniu do tych dwóch opcji relacyjnych baz danych: SQL Database i SQL Server na maszynach wirtualnych platformy Azure. Poznasz również sposób obliczania całkowitego kosztu aplikacji.

#### <a name="billing-and-licensing-basics"></a>Podstawowe informacje dotyczące rozliczeń i licencjonowania

Obecnie **SQL Database** jest sprzedawany jako usługa i jest dostępna z kilkoma opcjami wdrażania i w kilku warstwach usług z różnymi cenami dla zasobów, które są rozliczane godzinowo według stałej stawki na podstawie wybranej warstwy usług i wybranego rozmiaru. . Najnowsze informacje dotyczące bieżących obsługiwanych warstw usług, rozmiarów obliczeń i ilości miejsca do magazynowania można znaleźć w temacie [model zakupów oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

- Korzystając z SQL Database pojedynczej bazy danych, możesz wybrać warstwę usług odpowiadającą potrzebom z szerokiego zakresu cen, rozpoczynając od 5 USD/miesiąc dla warstwy Podstawowa.
- Można utworzyć [Pule elastyczne](sql-database-elastic-pool.md) , aby współużytkować zasoby między wystąpieniami bazy danych w celu ograniczenia kosztów i przekroczenia użycia.
- Korzystając z SQL Database wystąpienia zarządzanego, możesz również wprowadzić własną licencję. Aby uzyskać więcej informacji na temat przygotowywania licencji, zobacz [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/) lub Użyj [kalkulatora korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) , aby dowiedzieć się, jak **zaoszczędzić do 40%** .

Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji.

W przypadku usługi **SQL Database** oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez firmę Microsoft, co obniża koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](sql-database-automated-backups.md) pozwala osiągnąć znaczne oszczędności kosztów, zwłaszcza w przypadku dużej liczby baz danych.

Dzięki **programowi SQL Server na maszynach wirtualnych platformy Azure** można używać udostępnianego na platformie obrazu programu SQL Server (który obejmuje licencję) lub przenieść swoją licencję programu SQL Server. Dostępne są wszystkie obsługiwane wersje programu SQL Server (2008R2, 2012, 2014, 2016) i edycje (Developer, Express, Web, Standard, Enterprise). Ponadto są dostępne wersje bring-your-own-license (BYOL) obrazów. W przypadku użycia obrazów dostarczanych przez platformę Azure koszty operacyjne zależą od rozmiaru maszyny wirtualnej, a także od wybranej wersji programu SQL Server. Niezależnie od rozmiaru maszyny wirtualnej lub wersji SQL Server, użytkownik uiszcza koszt licencjonowania za minutę SQL Server i serwera z systemem Windows lub Linux wraz z kosztem usługi Azure Storage dla dysków maszyny wirtualnej. Opcja rozliczania co minutę pozwala używać programu SQL Server tak długo, jak jest to konieczne bez wykupywania dodatkowych licencji programu SQL Server. Jeśli wprowadzisz własną licencję SQL Server na platformę Azure, opłaty są naliczone wyłącznie za koszty związane z serwerem i magazynem. Więcej informacji na temat przenoszenia własnej licencji można znaleźć w temacie [Przenośność licencji za pośrednictwem programu Software Assurance w systemie Azure](https://azure.microsoft.com/pricing/license-mobility/). Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Obliczanie całkowitego kosztu aplikacji

Po rozpoczęciu korzystania z platformy w chmurze koszt korzystania z aplikacji obejmuje koszt nowych i bieżących kosztów administracyjnych oraz koszty usług w chmurze publicznej.

**W przypadku korzystania z usługi Azure SQL Database:**

- Wysoce zminimalizowane koszty administracyjne
- Ograniczone koszty opracowywania migrowanych aplikacji (wystąpienia zarządzane)
- Koszty usługi SQL Database
- Brak kosztów zakupu sprzętu

**W przypadku korzystania z programu SQL Server na maszynach wirtualnych platformy Azure:**

- Wyższe koszty administracyjne
- Ograniczone do braku kosztów programistycznych dla zmigrowanych aplikacji
- Koszty usługi maszyny wirtualnej
- Brak kosztów zakupu sprzętu

Więcej informacji na temat cen zawierają następujące zasoby:

- [Cennik SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Cennik usługi Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) Machines dla [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) i dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

Dla wielu firm decyzja o przeniesieniu usług do usługi w chmurze wiąże się zarówno z ograniczeniem złożoności w zakresie administracji, jak i obniżeniem kosztów. Dzięki IaaS i PaaS, firma Microsoft administruje podstawową infrastrukturą i automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i w przypadku, gdy istnieje awaria serwera w centrum danych.

- Za pomocą **Azure SQL Database**można nadal administrować swoją bazą danych, ale nie musisz już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia. Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji i administrowania.
- **Program SQL Server na maszynach wirtualnych platformy Azure** daje pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia programu SQL Server. W przypadku korzystania z maszyny wirtualnej decydujesz o tym, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych i kiedy zainstalować dodatkowe oprogramowanie, np. oprogramowanie antywirusowe. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Platforma Azure umożliwia zmianę rozmiaru maszyny wirtualnej zgodnie z zapotrzebowaniem. Więcej informacji można znaleźć w temacie [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Rozmiary maszyn wirtualnych i usług w chmurze na platformie Azure).

### <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)

Dla wielu działów IT wypełnienie zobowiązań wynikających z umowy dotyczącej poziomu usług (SLA) ma najwyższy priorytet. W tej sekcji opisano, jakie warunki umowy SLA stosuje się do poszczególnych opcji obsługi bazy danych.

W przypadku **SQL Database**firma Microsoft oferuje umowę SLA dotyczącą dostępności 99,99%. Najnowsze informacje można znaleźć w artykule [SQL Database — umowa SLA](https://azure.microsoft.com/support/legal/sla/sql-database/).

W przypadku **programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure** firma Microsoft zapewnia dostępność na poziomie 99,95%, która obejmuje tylko maszyny wirtualne. Umowa SLA nie obejmuje procesów (np. programu SQL Server) uruchomionych na maszynie wirtualnej i wymaga obsługi przynajmniej dwóch wystąpień maszyny wirtualnej w zbiorze dostępności. Najnowsze informacje znajdują się w artykule [Maszyny wirtualne — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Aby zapewnić wysoką dostępność bazy danych (HA) na maszynach wirtualnych, należy skonfigurować jedną z obsługiwanych opcji wysokiej dostępności w SQL Server, na przykład [zawsze włączone grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Użycie obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia osiągnięcie dostępności bazy danych na poziomie > 99,99%.

### <a name="market"></a>Czas przejścia na platformę Azure

**SQL Database pojedyncze bazy danych lub pule elastyczne** to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas na rynek dla nowych rozwiązań jest krytyczny. Dzięki funkcjonalności przypominającej model DBA jest doskonała dla architektów i deweloperów chmury, ponieważ zmniejsza potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

**SQL Database wystąpienie zarządzane** znacznie upraszcza migrację istniejących aplikacji do Azure SQL Database, umożliwiając szybkie przenoszenie aplikacji baz danych na rynek na platformie Azure.

**SQL Server uruchomiony na maszynach wirtualnych platformy Azure** jest idealnym rozwiązaniem, jeśli istniejące lub nowe aplikacje wymagają dużych baz danych lub dostępu do wszystkich funkcji w SQL Server lub Windows/Linux, i chcesz uniknąć czasu i kosztów nabycia nowego sprzętu lokalnego. Jest to również dobre dopasowanie, gdy chcesz migrować istniejące lokalne aplikacje i bazy danych na platformę Azure, tak jak w przypadku, gdy Azure SQL Database wystąpienia zarządzanego nie jest dobrym dopasowaniem. Ponieważ nie musisz zmieniać warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet na potrzeby ponownej architektury istniejącego rozwiązania. Zamiast tego możesz skoncentrować się na migracji wszystkich rozwiązań do platformy Azure i przeprowadzeniu optymalizacji wydajności, których może wymagać platforma Azure. Więcej informacje zawiera artykuł [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Najlepsze praktyki dotyczące wydajności dla programu SQL Server w usłudze Azure Virtual Machines).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą SQL Database, zobacz [Your first Azure SQL Database](sql-database-single-database-get-started.md) (Twoja pierwsza baza danych Azure SQL Database).
- Zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Aby rozpocząć pracę z programem SQL Server na maszynach wirtualnych platformy Azure, zobacz temat [Aprowizowanie maszyny wirtualnej programu SQL Server w Portalu Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
- [Zidentyfikuj odpowiednią Azure SQL Database/jednostkę SKU wystąpienia zarządzanego dla lokalnej bazy danych](/sql/dma/dma-sku-recommend-sql-db/).
