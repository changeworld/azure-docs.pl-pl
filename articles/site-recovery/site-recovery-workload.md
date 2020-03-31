---
title: Odzyskiwanie po awarii dla aplikacji lokalnych w przypadku usługi Azure Site Recovery — informacje
description: W tym artykule opisano obciążenia, które można chronić za pomocą funkcji odzyskiwania po awarii w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062835"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informacje o odzyskiwaniu po awarii dla aplikacji lokalnych

W tym artykule opisano lokalne obciążenia i aplikacje, które można chronić w celu odzyskiwania po awarii za pomocą usługi [Azure Site Recovery.](site-recovery-overview.md)

## <a name="overview"></a>Omówienie

Organizacje potrzebują strategii ciągłości działania i odzyskiwania po awarii (BCDR), aby zapewnić bezpieczeństwo obciążeń i danych podczas planowanych i nieplanowanych przestojów. I odzyskać do regularnych warunków pracy.

Usługa Site Recovery na platformie Azure ułatwia realizację strategii BCDR. Za pomocą usługi Site Recovery można wdrożyć replikację obsługującą aplikacje w chmurze lub lokacji dodatkowej. Za pomocą usługi Site Recovery można zarządzać replikacją, przeprowadzać testy odzyskiwania po awarii oraz uruchamiać trybu failover i po awarii. Aplikacje mogą być uruchamiane na komputerach z systemem Windows lub Linux, serwerach fizycznych, vmware lub funkcji Hyper-V.

Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Firma Microsoft ściśle współpracuje z wiodącymi dostawcami, w tym Oracle, SAP i Red Hat. Rozwiązania replikacji można dostosować do poszczególnych aplikacji.

## <a name="why-use-site-recovery-for-application-replication"></a>Dlaczego warto używać usługi Site Recovery do replikacji aplikacji?

Usługa Site Recovery przyczynia się do ochrony i odzyskiwania na poziomie aplikacji w następujący sposób:

- Niezależna od aplikacji i zapewnia replikację dla wszystkich obciążeń uruchomionych na obsługiwanym komputerze.
- Replikacja niemal synchroniczowa, z celami punktu odzyskiwania (RPO) w zaledwie 30 sekund, aby zaspokoić potrzeby najbardziej krytycznych aplikacji biznesowych.
- Spójne migawki jednowarstwowych lub wielowarstwowych aplikacji.
- Integracja z programem SQL Server AlwaysOn oraz współpraca z innymi technologiami replikacji na poziomie aplikacji. Na przykład replikacja usługi Active Directory, zawsze dostępne w programie SQL i grupy dostępności baz danych programu Exchange (DAG).
- Elastyczne plany odzyskiwania, które umożliwiają odzyskanie całego stosu aplikacji za pomocą jednego kliknięcia i uwzględnić skrypty zewnętrzne i akcje ręczne w planie.
- Zaawansowane zarządzanie siecią w usłudze Site Recovery i azure w celu uproszczenia wymagań sieci aplikacji. Zarządzanie siecią, takie jak możliwość rezerwowania adresów IP, konfigurowania równoważenia obciążenia i integracji z usługą Azure Traffic Manager w celu przełączania sieci celów czasu odzyskiwania (RTO).
- Bogata biblioteka automatyzacji, która zapewnia gotowe do zastosowania w środowisku produkcyjnym skrypty dopasowane do danych aplikacji, które można pobrać i zintegrować z planami odzyskiwania.

## <a name="workload-summary"></a>Podsumowanie obciążenia

Usługa Site Recovery może replikować dowolną aplikację uruchomioną na obsługiwanej maszynie. Nawiązaliśmy współpracę z zespołami produktów, aby wykonać dodatkowe testy aplikacji określonych w poniższej tabeli.

| **Obciążenie** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** |**Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikowanie maszyn wirtualnych VMware na platformie Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Tak |Tak |Tak |Tak |Tak|
| Aplikacje internetowe (IIS, SQL) |Tak |Tak |Tak |Tak |Tak|
| System Center Operations Manager |Tak |Tak |Tak |Tak |Tak|
| Program SharePoint |Tak |Tak |Tak |Tak |Tak|
| SAP<br/><br/>Replikacja lokacji SAP do platformy Azure, bez klastra |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft)|
| Exchange (bez grupy DAG) |Tak |Tak |Tak |Tak |Tak|
| Pulpit zdalny/VDI |Tak |Tak |Tak |Tak |Tak|
| Linux (system operacyjny i aplikacje) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft) |Tak (testowany przez firmę Microsoft)|
| Dynamics AX |Tak |Tak |Tak |Tak |Tak|
| Serwer plików systemu Windows |Tak |Tak |Tak |Tak |Tak|
| Citrix XenApp i XenDesktop |Tak|Nie dotyczy |Tak |Nie dotyczy |Tak |

## <a name="replicate-active-directory-and-dns"></a>Replikacja usługi Active Directory i DNS

Usługa Active Directory i infrastruktura DNS są istotne dla większości aplikacji firmowych. Podczas odzyskiwania po awarii należy chronić i odzyskać te składniki infrastruktury, zanim odzyskasz obciążenia i aplikacje.

Usługa Site Recovery umożliwia utworzenie całkowicie zautomatyzowanego planu odzyskiwania po awarii dla usługi Active Directory i infrastruktury DNS. Na przykład, aby w trybie failpoint i SAP z lokacji podstawowej do dodatkowej, można skonfigurować plan odzyskiwania, który najpierw kończy się niepowodzeniem w usłudze Active Directory. Następnie użyj dodatkowego planu odzyskiwania specyficznego dla aplikacji, aby przejść w tryb fail over inne aplikacje, które opierają się na usłudze Active Directory.

[Dowiedz się więcej](site-recovery-active-directory.md) o odzyskiwaniu po awarii w usłudze Active Directory i systemie DNS.

## <a name="protect-sql-server"></a>Ochrona programu SQL Server

SQL Server zapewnia podstawę usług danych dla wielu aplikacji biznesowych w lokalnym centrum danych. Odzysk lokacji może być używany z technologiami SQL Server HA/DR, aby chronić wielowarstwowe aplikacje dla przedsiębiorstw korzystające z programu SQL Server.

Usługa Site Recovery zapewnia:

- Proste i ekonomiczne rozwiązanie do odzyskiwania po awarii dla programu SQL Server. Replikacja wielu wersji i edycji autonomicznych serwerów i klastrów programu SQL Server do platformy Azure lub lokacji dodatkowej.
- Integracja z zawsze włączonymi grupami dostępności programu SQL w celu zarządzania pracą w trybie failover i powrotem po awarii przy użyciu planów odzyskiwania usługi Azure Site Recovery.
- Kompleksowe plany odzyskiwania dla wszystkich warstw w aplikacji, w tym dla baz danych programu SQL Server.
- Skalowanie programu SQL Server dla szczytowych obciążeń za pomocą funkcji Site Recovery przez _rozerwanie_ ich na większe rozmiary maszyn wirtualnych IaaS na platformie Azure.
- Łatwe testowanie odzyskiwania po awarii programu SQL Server. Możliwość uruchomienia testowego trybu failover w celu przeanalizowania danych i przeprowadzenia kontroli zgodności bez wpływu na środowisko produkcyjne.

[Dowiedz się więcej](site-recovery-sql.md) o odzyskiwaniu po awarii dla serwera SQL.

## <a name="protect-sharepoint"></a>Ochrona programu SharePoint

Usługa Azure Site Recovery pomaga w ochronie wdrożeń programu SharePoint w następujący sposób:

- Eliminuje potrzebę stosowania farmy rezerwowej (oraz koszty związane z infrastrukturą) na potrzeby odzyskiwania po awarii. Usługa Site Recovery umożliwia replikowanie całej farmy (warstw sieci web, aplikacji i bazy danych) na platformie Azure lub w lokacji dodatkowej.
- Upraszcza wdrażanie aplikacji i zarządzanie nimi. Aktualizacje wdrożone w lokacji głównej są automatycznie replikowane. Aktualizacje są dostępne po pracy awaryjnej i odzyskiwania farmy w lokacji dodatkowej. Zmniejsza złożoność zarządzania i koszty związane z aktualizowaniem gospodarstwa dyżurnego.
- Upraszcza projektowanie i testowanie aplikacji programu SharePoint poprzez tworzenie na żądanie repliki środowiska z kopią przypominającą środowisko produkcyjne, co umożliwia testowanie i debugowanie.
- Upraszcza przejście do chmury przez użycie usługi Site Recovery do migrowania wdrożeń programu SharePoint do platformy Azure.

[Dowiedz się więcej](site-recovery-sharepoint.md) o odzyskiwaniu po awarii w programie SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrona programu Dynamics AX

Usługa Azure Site Recovery pomaga chronić rozwiązanie Dynamics AX ERP w następujący sposób:

- Zarządzanie replikacją całego środowiska Dynamics AX (warstwy sieci Web i systemu AOS, warstwy bazy danych, program SharePoint) na platformie Azure lub w lokacji dodatkowej.
- Upraszczanie migracji wdrożeń Dynamics AX do chmury (Azure).
- Upraszczanie projektowania i testowania aplikacji programu Dynamics AX poprzez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.

[Dowiedz się więcej](site-recovery-dynamicsax.md) o odzyskiwaniu po awarii w systemie Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Ochrona usług pulpitu zdalnego

Usługi pulpitu zdalnego (RDS) umożliwiają korzystanie z infrastruktury pulpitów wirtualnych (VDI), pulpitów opartych na sesjach i aplikacji, które umożliwiają użytkownikom pracę w dowolnym miejscu.

Za pomocą usługi Azure Site Recovery można replikować następujące usługi:

- Replikowanie zarządzanych lub niezarządzanych komputerów stacjonarnych w puli do lokacji dodatkowej.
- Replikowanie zdalnych aplikacji i sesji do lokacji dodatkowej lub platformy Azure.

W poniższej tabeli przedstawiono opcje replikacji:

| **RDS** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** | **Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikowanie maszyn wirtualnych VMware na platformie Azure** | **Replikacja serwerów fizycznych do lokacji dodatkowej** | **Replikacja serwerów fizycznych do platformy Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Pulpit wirtualny w puli (niezarządzany)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Pulpit wirtualny w puli (zarządzany i bez dysku UPD)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Zdalne aplikacje i sesje pulpitu (bez dysku UPD)** |Tak|Tak |Tak |Tak |Tak |Tak |Tak |

[Dowiedz się więcej](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) o odzyskiwaniu po awarii dla usług RDS.

## <a name="protect-exchange"></a>Ochrona programu Exchange

Usługa Site Recovery pomaga chronić program Exchange w następujący sposób:

- W przypadku małych wdrożeń programu Exchange, np. serwerów pojedynczych lub autonomicznych, usługa Site Recovery może wykonywać replikację i stosować tryb failover na platformie Azure lub w lokacji dodatkowej.
- W przypadku większych wdrożeń usługa Site Recovery integruje się z grupami DAG programu Exchange.
- Grupy DAG programu Exchange są zalecanym rozwiązaniem dla odzyskiwania po awarii programu Exchange w przedsiębiorstwie.  Plany odzyskiwania usługi Site Recovery mogą uwzględniać grupy DAG, aby organizować tryb failover grupy DAG między lokacjami.

Aby dowiedzieć się więcej o odzyskiwaniu po awarii w programie Exchange, zobacz [Usługi daustki exchange](/Exchange/high-availability/database-availability-groups/database-availability-groups) i [odzyskiwanie po awarii programu Exchange](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Ochrona systemu SAP

Z usługi Site Recovery można korzystać, aby zabezpieczyć wdrożenie SAP w następujący sposób:

- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych lokalnie, replikując składniki do platformy Azure.
- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych na platformie Azure, replikując składniki do innego centrum danych platformy Azure.
- Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
- Uprość uaktualnienia, testowanie i tworzenie prototypów projektu SAP, tworząc na żądanie klony środowiska produkcyjnego na potrzeby testowania aplikacji SAP.

[Dowiedz się więcej](site-recovery-sap.md) o odzyskiwaniu po awarii dla systemu SAP.

## <a name="protect-internet-information-services"></a>Ochrona internetowych usług informacyjnych

Funkcja Odzyskiwanie witryny służy do ochrony wdrożenia internetowych usług informacyjnych (IIS) w następujący sposób:

Usługa Azure Site Recovery zapewnia odzyskiwanie po awarii przez replikowanie krytycznych składników środowiska do zimnej lokacji zdalnej lub chmury publicznej, takiej jak Microsoft Azure. Ponieważ maszyny wirtualne z serwerem sieci web i bazą danych są replikowane do lokacji odzyskiwania, nie ma wymogu oddzielnej kopii zapasowej dla plików konfiguracyjnych lub certyfikatów. Mapowania i powiązania aplikacji zależne od zmiennych środowiskowych, które są zmieniane po przełączeniu do trybu failover, mogą być aktualizowane za pomocą skryptów zintegrowanych z planami odzyskiwania po awarii. Maszyny wirtualne są wychowywane w lokacji odzyskiwania tylko podczas pracy awaryjnej. Usługa Azure Site Recovery ułatwia również organizowanie kompleksowej pracy awaryjnej, zapewniając następujące możliwości:

- Obsługa sekwencji wyłączania i włączania maszyn wirtualnych w różnych warstwach.
- Dodawanie skryptów, aby umożliwić aktualizacje zależności aplikacji i powiązań na maszynach wirtualnych po ich uruchomieniu. Skrypty mogą też służyć do aktualizacji serwera DNS w taki sposób, aby wskazywał lokację odzyskiwania.
- Przydzielanie adresów IP do maszyn wirtualnych przed przepracowaniem awaryjnym przez mapowanie sieci podstawowych i odzyskiwania i używanie skryptów, które nie muszą być aktualizowane po zakończeniu pracy awaryjnej.
- Możliwość pracy awaryjnej jednym kliknięciem dla wielu aplikacji sieci web, która eliminuje zakres zamieszania podczas awarii.
- Możliwość testowania planów odzyskiwania w izolowanym środowisku na potrzeby próbnego odzyskiwania po awarii.

[Dowiedz się więcej](site-recovery-iis.md) o odzyskiwania po awarii dla iIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrona programów Citrix XenApp i XenDesktop

Aby chronić wdrożenia programów Citrix XenApp i XenDesktop, korzystaj z usługi Site Recovery w następujący sposób:

- Włącz ochronę wdrożenia Citrix XenApp i XenDesktop. Replikuj różne warstwy wdrażania na platformę Azure: Active Directory, serwer DNS, serwer bazy danych SQL, kontroler dostarczania Citrix, serwer StoreFront, XenApp Master (VDA), Serwer licencji Citrix XenApp.
- Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia programów Citrix XenApp i XenDesktop do platformy Azure.
- Ułatw projektowanie i testowanie wdrożenia Citrix XenApp/XenDesktop przez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.
- To rozwiązanie dotyczy tylko pulpitów wirtualnych systemu Windows Server, a nie pulpitów wirtualnych klienckich. Pulpity wirtualne klienta nie są jeszcze obsługiwane do licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

[Dowiedz się więcej](site-recovery-citrix-xenapp-and-xendesktop.md) o odzyskiwaniu po awarii w przypadku wdrożeń Citrix XenApp i XenDesktop. Możesz też zapoznać się z [białą papierem Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](azure-to-azure-quickstart.md) o odzyskiwaniu po awarii dla maszyny Wirtualnej platformy Azure.
