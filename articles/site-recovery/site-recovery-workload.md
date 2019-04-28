---
title: Jakie obciążenia można chronić za pomocą usługi Azure Site Recovery? | Microsoft Docs
description: W tym artykule opisano obciążenia, które można chronić za pomocą funkcji odzyskiwania po awarii w usłudze Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 3ad3438f02ead9063a683a39d4ac5823274d55f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276141"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Jakie obciążenia można chronić za pomocą usługi Azure Site Recovery?

W tym artykule opisano obciążenia i aplikacje, które może chronić do odzyskiwania po awarii przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.



## <a name="overview"></a>Przegląd

Organizacje wymagają strategii zapewniającej ciągłość biznesową i odzyskiwanie po awarii (BCDR, Business Continuity and Disaster Recovery), w ramach której obciążenia i dane pozostają bezpieczne i dostępne podczas planowanych lub nieplanowanych przestojów oraz możliwie jak najszybciej są przywracane do normalnych warunków roboczych.

Usługa Site Recovery na platformie Azure ułatwia realizację strategii BCDR. Za pomocą usługi Site Recovery można wdrożyć replikację obsługującą aplikacje w chmurze lub lokacji dodatkowej. Niezależnie od tego, czy aplikacje są oparte na systemie Windows, czy Linux, działają na serwerach fizycznych, platformie VMware lub w ramach funkcji Hyper-V, usługi Site Recovery można używać do organizowania replikacji, przeprowadzania testów odzyskiwania po awarii oraz uruchamiania trybu failover i powrotu po awarii.

Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, w tym SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Firma Microsoft współpracuje również blisko z czołowymi producentami, takimi jak Oracle, SAP i Red Hat. Rozwiązania replikacji można dostosować do poszczególnych aplikacji.

## <a name="why-use-site-recovery-for-application-replication"></a>Dlaczego warto używać usługi Site Recovery do replikacji aplikacji?

Usługa Site Recovery przyczynia się do ochrony i odzyskiwania na poziomie aplikacji w następujący sposób:

* Zapewnia replikację dla dowolnych obciążeń działających na obsługiwanej maszynie, niezależnie od aplikacji.
* Niemal synchroniczna replikacja z celami punktu odzyskiwania wynoszącymi nawet 30 sekund, która spełnia wymagania większości aplikacji o krytycznym znaczeniu biznesowym.
* Spójne migawki jednowarstwowych lub wielowarstwowych aplikacji.
* Integracja z funkcją AlwaysOn programu SQL Server i współpraca z innymi technologiami replikacji na poziomie aplikacji, w tym replikacją AD, SQL AlwaysOn, grupami dostępności bazy danych (DAG, Database Availability Group) programu Exchange i Oracle Data Guard.
* Elastyczne plany odzyskiwania, które umożliwiają odzyskanie całego stosu aplikacji jednym kliknięciem oraz uwzględniają zewnętrzne skrypty i działania ręczne w planie.
* Zaawansowane zarządzanie siecią w usłudze Site Recovery i na platformie Azure, które upraszcza wymagania sieciowe aplikacji. Obejmuje to rezerwowanie adresów IP, konfigurowanie równoważenia obciążeń oraz integrację z programem Azure Traffic Manager dla zapewnienia przełączania sieci o niskim limicie czasu odzyskiwania.
* Bogata biblioteka automatyzacji, która zapewnia gotowe do zastosowania w środowisku produkcyjnym skrypty dopasowane do danych aplikacji, które można pobrać i zintegrować z planami odzyskiwania.

## <a name="workload-summary"></a>Podsumowanie obciążenia
Usługa Site Recovery może replikować dowolną aplikację uruchomioną na obsługiwanej maszynie. Ponadto współpracujemy z zespołami odpowiedzialnymi za produkty, aby przeprowadzać dodatkowe testy konkretnych aplikacji.

| **Obciążenie** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** |**Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikacja maszyn wirtualnych VMware do platformy Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Tak |Tak |Tak |Tak |Tak|
| Aplikacje internetowe (IIS, SQL) |Tak |Tak |Tak |Tak |Tak|
| System Center Operations Manager |Tak |Tak |Tak |Tak |Tak|
| Sharepoint |Tak |Tak |Tak |Tak |Tak|
| SAP<br/><br/>Replikacja lokacji SAP do platformy Azure, bez klastra |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft)|
| Exchange (bez grupy DAG) |Tak |Tak |Tak |Tak |Tak|
| Pulpit zdalny/VDI |Tak |Tak |Tak |Tak |Tak|
| Linux (system operacyjny i aplikacje) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft)|
| Dynamics AX |Tak |Tak |Tak |Tak |Tak|
| Serwer plików systemu Windows |Tak |Tak |Tak |Tak |Tak|
| Citrix XenApp i XenDesktop |Tak|ND |Tak |ND |Tak |

## <a name="replicate-active-directory-and-dns"></a>Replikacja usługi Active Directory i DNS
Usługa Active Directory i infrastruktura DNS są istotne dla większości aplikacji firmowych. Podczas odzyskiwania po awarii należy zabezpieczyć i odzyskać te składniki infrastruktury przed odzyskaniem obciążeń i aplikacji.

Usługa Site Recovery umożliwia utworzenie całkowicie zautomatyzowanego planu odzyskiwania po awarii dla usługi Active Directory i infrastruktury DNS. Jeśli na przykład chcesz przełączyć programy SharePoint i SAP do trybu failover z lokacji głównej do lokacji dodatkowej, możesz skonfigurować plan odzyskiwania, który najpierw przełączy usługę Active Directory do trybu failover, a następnie utworzyć dodatkowy plan dla aplikacji, który będzie przełączać do trybu failover inne aplikacje zależne od usługi Active Directory.

[Dowiedz się więcej](site-recovery-active-directory.md) o ochronie usługi Active Directory i infrastruktury DNS.

## <a name="protect-sql-server"></a>Ochrona programu SQL Server
Program SQL Server stanowi podstawę dla usług danych wielu aplikacji biznesowych w lokalnym centrum danych.  Usługi Site Recovery można używać wraz z technologiami wysokiej dostępności i odzyskiwania po awarii programu SQL Server, aby chronić wielowarstwowe aplikacje firmowe, które korzystają z programu SQL Server. Usługa Site Recovery zapewnia:

* Proste i ekonomiczne rozwiązanie do odzyskiwania po awarii dla programu SQL Server. Replikacja wielu wersji i edycji autonomicznych serwerów i klastrów programu SQL Server do platformy Azure lub lokacji dodatkowej.  
* Integracja z zawsze włączonymi grupami dostępności programu SQL w celu zarządzania pracą w trybie failover i powrotem po awarii przy użyciu planów odzyskiwania usługi Azure Site Recovery.
* Kompleksowe plany odzyskiwania dla wszystkich warstw w aplikacji, w tym dla baz danych programu SQL Server.
* Skalowanie programu SQL Server dla obciążeń szczytowych z usługą Site Recovery poprzez „szybkie przekierowanie” do większych rozmiarów maszyn wirtualnych IaaS na platformie Azure.
* Łatwe testowanie odzyskiwania po awarii programu SQL Server. Możliwość uruchomienia testowego trybu failover w celu przeanalizowania danych i przeprowadzenia kontroli zgodności bez wpływu na środowisko produkcyjne.

[Dowiedz się więcej](site-recovery-sql.md) o ochronie programu SQL Server.

## <a name="protect-sharepoint"></a>Ochrona programu SharePoint
Usługa Azure Site Recovery pomaga w ochronie wdrożeń programu SharePoint w następujący sposób:

* Eliminuje potrzebę stosowania farmy rezerwowej (oraz koszty związane z infrastrukturą) na potrzeby odzyskiwania po awarii. Użyj usługi Site Recovery, aby replikować całą farmę (warstwy sieci Web, aplikacji i baz danych) do platformy Azure lub lokacji dodatkowej.
* Upraszcza wdrażanie aplikacji i zarządzanie nimi. Aktualizacje wdrożone w lokacji głównej są automatycznie replikowane, dlatego są dostępne po przejściu do trybu failover i rozpoczęciu odzyskiwania farmy w lokacji dodatkowej. Ponadto upraszcza zarządzanie i obniża koszty związane z utrzymywaniem aktualnej farmy rezerwowej.
* Upraszcza projektowanie i testowanie aplikacji programu SharePoint poprzez tworzenie na żądanie repliki środowiska z kopią przypominającą środowisko produkcyjne, co umożliwia testowanie i debugowanie.
* Upraszcza przejście do chmury przez użycie usługi Site Recovery do migrowania wdrożeń programu SharePoint do platformy Azure.

[Dowiedz się więcej](site-recovery-sharepoint.md) o ochronie programu SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrona programu Dynamics AX
Usługa Azure Site Recovery pomaga chronić rozwiązanie Dynamics AX ERP w następujący sposób:

* Organizowanie replikacji całego środowiska Dynamics AX (warstw sieci Web i serwera AOS, warstw baz danych, programu SharePoint) do platformy Azure lub lokacji dodatkowej.
* Upraszczanie migracji wdrożeń Dynamics AX do chmury (Azure).
* Upraszczanie projektowania i testowania aplikacji programu Dynamics AX poprzez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.

[Dowiedz się więcej](site-recovery-dynamicsax.md) o ochronie programu Dynamic AX.

## <a name="protect-rds"></a>Ochrona usług pulpitu zdalnego
Usługi pulpitu zdalnego zapewniają infrastrukturę pulpitu wirtualnego (VDI), pulpity oparte na sesjach oraz aplikacje, dzięki którym użytkownicy mogą pracować z dowolnego miejsca. Za pomocą usługi Azure Site Recovery można wykonywać następujące czynności:

* Replikowanie zarządzanych lub niezarządzanych pulpitów wirtualnych w puli do lokacji dodatkowej oraz zdalnych aplikacji i sesji do lokacji dodatkowej lub platformy Azure.

* Oto co można replikować:

| **RDS** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** | **Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikacja maszyn wirtualnych VMware do platformy Azure** | **Replikacja serwerów fizycznych do lokacji dodatkowej** | **Replikacja serwerów fizycznych do platformy Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Pulpit wirtualny w puli (niezarządzany)** |Nie|Yes |Nie |Yes |Nie |Yes |Nie |
| **Pulpit wirtualny w puli (zarządzany i bez dysku UPD)** |Nie|Yes |Nie |Yes |Nie |Yes |Nie |
| **Zdalne aplikacje i sesje pulpitu (bez dysku UPD)** |Yes|Yes |Yes |Yes |Yes |Yes |Yes |

[Konfigurowanie funkcji odzyskiwania po awarii ona potrzeby usługi RDS za pomocą usługi Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Dowiedz się więcej](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) o ochronie usług pulpitu zdalnego.

## <a name="protect-exchange"></a>Ochrona programu Exchange
Usługa Site Recovery pomaga chronić program Exchange w następujący sposób:

* W przypadku małych wdrożeń programu Exchange, np. serwerów pojedynczych lub autonomicznych, usługa Site Recovery może wykonywać replikację i stosować tryb failover na platformie Azure lub w lokacji dodatkowej.
* W przypadku większych wdrożeń usługa Site Recovery integruje się z grupami DAG programu Exchange.
* Grupy DAG programu Exchange są zalecanym rozwiązaniem dla odzyskiwania po awarii programu Exchange w przedsiębiorstwie.  Plany odzyskiwania usługi Site Recovery mogą uwzględniać grupy DAG, aby organizować tryb failover grupy DAG między lokacjami.

[Dowiedz się więcej](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) o ochronie programu Exchange.

## <a name="protect-sap"></a>Ochrona systemu SAP
Z usługi Site Recovery można korzystać, aby zabezpieczyć wdrożenie SAP w następujący sposób:

* Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych lokalnie, replikując składniki do platformy Azure.
* Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych na platformie Azure, replikując składniki do innego centrum danych platformy Azure.
* Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* Uprość uaktualnienia, testowanie i tworzenie prototypów projektu SAP, tworząc na żądanie klony środowiska produkcyjnego na potrzeby testowania aplikacji SAP.

[Dowiedz się więcej](site-recovery-sap.md) o ochronie systemu SAP.

## <a name="protect-iis"></a>Ochrona usług IIS
Z usługi Site Recovery można korzystać, aby zabezpieczyć wdrożenie usług IIS w następujący sposób:

Usługa Azure Site Recovery zapewnia odzyskiwanie po awarii przez replikowanie krytycznych składników środowiska do zimnej lokacji zdalnej lub chmury publicznej, takiej jak Microsoft Azure. Ponieważ maszyny wirtualne z serwerem internetowym i bazą danych są replikowane do lokacji odzyskiwania, nie jest wymagane oddzielne tworzenie kopii zapasowych plików konfiguracji lub certyfikatów. Mapowania i powiązania aplikacji zależne od zmiennych środowiskowych, które są zmieniane po przełączeniu do trybu failover, mogą być aktualizowane za pomocą skryptów zintegrowanych z planami odzyskiwania po awarii. Maszyny wirtualne są przenoszone do lokalizacji odzyskiwania tylko w przypadku przełączania do trybu failover. Oprócz tego usługa Azure Site Recovery pomaga kompleksowo organizować przełączanie do trybu failover, udostępniając następujące możliwości:

-   Obsługa sekwencji wyłączania i włączania maszyn wirtualnych w różnych warstwach.
-   Dodawanie skryptów umożliwiających aktualizację zależności i powiązań aplikacji na maszynach wirtualnych po ich uruchomieniu. Skrypty mogą też służyć do aktualizacji serwera DNS w taki sposób, aby wskazywał lokację odzyskiwania.
-   Przydzielanie adresów IP do maszyn wirtualnych przed przełączeniem do trybu failover przez zamapowanie podstawowej oraz dodatkowej sieci odzyskiwania i użycie skryptów, których nie trzeba aktualizować po przełączeniu do trybu failover.
-   Możliwość przełączania do trybu failover jednym kliknięciem dla wielu aplikacji internetowych na serwerach internetowych i w ten sposób wyeliminowanie pomyłek w przypadku awarii.
-   Możliwość testowania planów odzyskiwania w izolowanym środowisku na potrzeby próbnego odzyskiwania po awarii.

[Dowiedz się więcej](https://aka.ms/asr-iis) o ochronie farmy sieci Web usług IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrona programów Citrix XenApp i XenDesktop
Aby chronić wdrożenia programów Citrix XenApp i XenDesktop, korzystaj z usługi Site Recovery w następujący sposób:

* Włącz ochronę wdrożenia programów Citrix XenApp i XenDesktop przez replikowanie różnych warstw wdrożenia (serwer AD DNS, serwer usługi SQL Database, kontroler dostarczania Citrix, serwer StoreFront, XenApp Master (VDA), serwer licencji Citrix XenApp) do platformy Azure.
* Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia programów Citrix XenApp i XenDesktop do platformy Azure.
* Ułatw projektowanie i testowanie wdrożenia Citrix XenApp/XenDesktop przez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.
* To rozwiązanie ma zastosowanie tylko w przypadku pulpitów wirtualnych systemu operacyjnego Windows Server, a nie pulpitów wirtualnych klientów, ponieważ pulpity wirtualne klienta nie są jeszcze obsługiwane w przypadku licencjonowania na platformie Azure.
[Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

[Dowiedz się więcej](site-recovery-citrix-xenapp-and-xendesktop.md) na temat chronienia wdrożeń programów Citrix XenApp i XenDesktop. Możesz też zapoznać się z [oficjalnym dokumentem firmy Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr) poświęconym tym samym zagadnieniom.

## <a name="next-steps"></a>Kolejne kroki

[Zapoznaj się z wprowadzeniem](azure-to-azure-quickstart.md) do replikacji maszyn wirtualnych platformy Azure.
