---
title: Obsługa oceny VMware w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny VMware w Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 8ed20ecd37eacdcb771db7c166ff8fc22b96cb89
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846181"
---
# <a name="support-matrix-for-vmware-assessment"></a>Macierz obsługi dla oceny oprogramowania VMware 

Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące oceny maszyn wirtualnych VMware za pomocą [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-migration-tool). Jeśli szukasz informacji na temat migrowania maszyn wirtualnych VMware na platformę Azure, zapoznaj się z [macierzą obsługi migracji](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Przegląd

Aby ocenić maszyny lokalne na potrzeby migracji na platformę Azure z tego artykułu, należy dodać Azure Migrate: Narzędzie do oceny serwera do projektu Azure Migrate. Należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła dane dotyczące konfiguracji i wydajności na platformę Azure. Po odnajdywaniu maszyn można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny**| Odkrywaj i oceniaj do 35 000 maszyn wirtualnych VMware w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Projekt może obejmować maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne oraz limity oceny.
**Discovery** (Odnajdywanie) | Urządzenie Azure Migrate może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.


## <a name="application-discovery"></a>Odnajdywanie aplikacji

Oprócz odnajdywania maszyn Azure Migrate: Ocena serwera może wykrywać aplikacje, role i funkcje działające na maszynach. Odnajdywanie spisu aplikacji umożliwia zidentyfikowanie i zaplanowanie ścieżki migracji dopasowanej do obciążeń lokalnych. 

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Discovery** (Odnajdywanie) | Odnajdywanie jest bezagentem, przy użyciu poświadczeń gościa komputera i zdalnie uzyskuje dostęp do maszyn przy użyciu usług WMI i wywołań SSH.
**Obsługiwane maszyny** | Lokalne maszyny wirtualne programu VMware.
**System operacyjny maszyny** | Wszystkie wersje systemów Windows i Linux.
**poświadczenia vCenter** | Konto vCenter Server z dostępem tylko do odczytu i uprawnienia do Virtual Machines > operacji gościa.
**Poświadczenia maszyny wirtualnej** | Obecnie obsługuje użycie jednego poświadczenia dla wszystkich serwerów z systemem Windows i jedno poświadczenie dla wszystkich serwerów z systemem Linux.<br/><br/> Tworzysz konto użytkownika-gościa dla maszyn wirtualnych z systemem Windows oraz normalne/normalne konto użytkownika (dostęp sudo) dla wszystkich maszyn wirtualnych z systemem Linux.
**Narzędzia VMware** | Narzędzia VMware muszą być zainstalowane i uruchomione na maszynach wirtualnych, które mają zostać odnajdywane.
**Dostęp do portu** | Na hostach ESXi z uruchomionymi maszynami wirtualnymi urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443.
**Limity** | W przypadku odnajdywania aplikacji można odkryć do 10000 na urządzenie. 

## <a name="vmware-requirements"></a>Wymagania dotyczące oprogramowania VMware

**VMware** | **Szczegóły**
--- | ---
**vCenter Server** | Komputery, które mają być odnajdywane i oceniane, muszą być zarządzane przez vCenter Server w wersji 5,5, 6,0, 6,5 lub 6,7.
**Uprawnienia (ocena)** | vCenter Server konto tylko do odczytu.
**Uprawnienia (Odnajdywanie aplikacji)** | konto vCenter Server z dostępem tylko do odczytu i przywileje włączone dla **maszyn wirtualnych > operacji gościa**.
**Uprawnienia (Wizualizacja zależności)** | Konto serwera centrum z dostępem tylko do odczytu oraz uprawnienia włączone dla **maszyn wirtualnych** > **operacji gościa**.


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla oprogramowania VMware jest wdrażane przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server. 

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) w oprogramowaniu VMware.
- Informacje o [adresach URL](migrate-appliance.md#url-access) , do których urządzenie musi uzyskać dostęp.

## <a name="port-access"></a>Dostęp do portu

**urządzenia** | **Połączenie**
--- | ---
Wprowadzony | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443 (HTTPS), 5671 i 5672 (AMQP) do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
Serwer vCenter | Połączenia przychodzące na porcie TCP 443 umożliwiające urządzeniu zbieranie metadanych dotyczących konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.
Hosty ESXi | **Wymagane tylko w przypadku [odnajdywania aplikacji](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) i [wizualizacji zależności bez wykorzystania agentów](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> Urządzenie łączy się z hostami ESXi na porcie TCP 443 w celu odnajdywania aplikacji i uruchamiania wizualizacji zależności bez agenta na maszynach wirtualnych działających na hostach.

## <a name="agent-based-dependency-visualization"></a>Wizualizacja zależności oparta na agencie

[Wizualizacja zależności](concepts-dependency-visualization.md) ułatwia wizualizację zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji. W przypadku wizualizacji opartej na agencie, wymagania i ograniczenia są podsumowane w poniższej tabeli.


**Wymaganie** | **Szczegóły**
--- | ---
**Wdrożenie** | Przed wdrożeniem wizualizacji zależności należy mieć projekt Azure Migrate na miejscu, z Azure Migrate: Narzędzie do oceny serwera dodane do projektu. Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych.<br/><br/> Wizualizacja zależności nie jest dostępna w Azure Government.
**Mapa usługi** | Wizualizacja zależności oparta na agentach używa rozwiązania [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Aby wdrożyć program, należy skojarzyć nowy lub istniejący obszar roboczy Log Analytics z projektem Azure Migrate.
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
**Odsetk** | Service Map rozwiązanie nie wiąże się z żadnymi opłatami za pierwsze 180 dni (od dnia skojarzonego Log Analytics obszaru roboczego z projektem Azure Migrate).<br/><br/> Po 180 dniach opłaty zostaną naliczone według standardowych Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie standardowych Log Analytics opłat.<br/><br/> Jeśli usuniesz projekt Azure Migrate, obszar roboczy nie zostanie usunięty z nim. Po usunięciu projektu Service Map nie jest bezpłatny, a każdy węzeł będzie obciążany zgodnie z warstwą płatną Log Analytics obszarze roboczym.
**Pracownicy** | Wizualizacja zależności oparta na agentach wymaga zainstalowania dwóch agentów na każdej maszynie, która ma zostać przeanalizowana.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [Agent zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)- . 
**Łączność z Internetem** | Jeśli maszyny nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.


## <a name="agentless-dependency-visualization"></a>Bezagentowa wizualizacja zależności

Ta opcja jest obecnie dostępna w wersji zapoznawczej. [Dowiedz się więcej](how-to-create-group-machine-dependencies-agentless.md). Wymagania zostały podsumowane w poniższej tabeli.

**Wymaganie** | **Szczegóły**
--- | ---
**Wdrożenie** | Przed wdrożeniem wizualizacji zależności należy mieć projekt Azure Migrate na miejscu, z Azure Migrate: Narzędzie do oceny serwera dodane do projektu. Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych.
**Obsługa maszyn wirtualnych** | Obecnie obsługiwane tylko w przypadku maszyn wirtualnych VMware.
**Maszyny wirtualne z systemem Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitowe)
**Maszyny wirtualne z systemem Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Konto systemu Windows** |  Wizualizacja wymaga konta użytkownika z dostępem gościa.
**Konto systemu Linux** | Wizualizacja wymaga konta użytkownika z uprawnieniami głównymi.<br/><br/> Alternatywnie konto użytkownika wymaga tych uprawnień w przypadku plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.
**Agenci maszyn wirtualnych** | Na maszynach wirtualnych nie jest wymagany żaden Agent.
**Narzędzia VMware** | Narzędzia VMware muszą być zainstalowane i uruchomione na maszynach wirtualnych, które mają być analizowane.
**poświadczenia vCenter** | Konto vCenter Server z dostępem tylko do odczytu i uprawnienia do Virtual Machines > operacji gościa.
**Dostęp do portu** | Na hostach ESXi z maszynami wirtualnymi, które chcesz analizować, urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443.



## <a name="next-steps"></a>Następne kroki

- [Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen.
- [Przygotowanie do](tutorial-prepare-vmware.md) oceny oprogramowania VMware.
