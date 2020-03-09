---
title: Wizualizacja zależności w usłudze Azure Migrate
description: Zawiera omówienie obliczeń oceny w usłudze oceny serwera w Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362205"
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

W tym artykule opisano wizualizację zależności w Azure Migrate: Ocena serwera.

## <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Wizualizacja zależności pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i przeprowadzenie migracji na platformę Azure. 

- W Azure Migrate: Ocena serwera, zbierasz maszyny w grupie, a następnie oceniasz grupę. Wizualizacja zależności ułatwia precyzyjne grupowanie maszyn z wysokim poziomem pewności dla oceny.
- Wizualizacja zależności pozwala identyfikować maszyny, które muszą być migrowane ze sobą. Możesz określić, czy maszyny są używane, czy mogą zostać zlikwidowane zamiast migrować.
- Wizualizacja zależności pomaga upewnić się, że nic nie zostało pozostawione, i uniknąć awarii podczas migracji.
- Wizualizacja jest szczególnie przydatna, jeśli nie masz pewności, czy maszyny są częścią wdrożenia aplikacji, które chcesz zmigrować na platformę Azure.


> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

## <a name="agent-basedagentless-visualization"></a>Wizualizacja oparta na agentach/bez agentów

Dostępne są dwie opcje wdrażania wizualizacji zależności:

- **Oparta na agentach**: Wizualizacja zależności oparta na agentach wymaga zainstalowania agentów na poszczególnych maszynach lokalnych, które mają zostać poddane analizie.
- Bez **agenta**: w przypadku tej opcji nie trzeba instalować agentów na maszynach, które mają być sprawdzane krzyżowo. Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware.


## <a name="agent-based-visualization"></a>Wizualizacja oparta na agencie

**Wymaganie** | **Szczegóły** | **Dowiedz się więcej**
--- | --- | ---
**Przed wdrożeniem** | Należy mieć projekt Azure Migrate na miejscu z Azure Migrate: Narzędzie do oceny serwera dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych. | [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/><br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/><br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby oceny serwerów fizycznych ( [Hyper-V](how-to-set-up-appliance-hyper-v.md)) lub [VMware](how-to-set-up-appliance-vmware.md).
**Wymagani agenci** | Na każdej maszynie, którą chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli maszyny lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics. | Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach Azure monitor](../log-analytics/log-analytics-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Istnieje skojarzenie nowego lub istniejącego obszaru roboczego Log Analytics z projektem Azure Migrate. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.
**Koszty** | W rozwiązaniu Service Map nie są naliczane żadne opłaty za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem Azure Migrate)/<br/><br/> Po 180 dniach opłaty zostaną naliczone według standardowych Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym należy używać identyfikatora i klucza dostarczonego przez projekt Azure Migrate.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt Azure Migrate, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.

## <a name="agentless-visualization"></a>Wizualizacja bez agentów


**Wymaganie** | **Szczegóły** | **Dowiedz się więcej**
--- | --- | ---
**Przed wdrożeniem** | Należy mieć projekt Azure Migrate na miejscu z Azure Migrate: Narzędzie do oceny serwera dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia lokalnych maszyn VMWare. | [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/><br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/><br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceny maszyn wirtualnych [VMware](how-to-set-up-appliance-vmware.md) .
**Wymagani agenci** | Na maszynach, które mają być analizowane, nie jest wymagany żaden Agent.
**Obsługiwane systemy operacyjne** | Przejrzyj [systemy operacyjne](migrate-support-matrix-vmware.md#agentless-dependency-visualization) obsługiwane w przypadku wizualizacji bez wykorzystania agentów.
**Maszyny wirtualne** | **Narzędzia VMware**: narzędzia VMware muszą być zainstalowane i uruchomione na maszynach wirtualnych, które mają być analizowane.<br/><br/> **Konto**: na urządzeniu Azure Migrate należy dodać konto użytkownika, za pomocą którego można uzyskiwać dostęp do maszyn wirtualnych w celu analizy.<br/><br/> **Maszyny wirtualne z systemem Windows**: konto użytkownika musi być kontem lokalnym lub administratorem domeny na komputerze.<br/><br/> **Maszyny wirtualne z systemem Linux**: uprawnienie główne jest wymagane na koncie. Alternatywnie konto użytkownika wymaga tych dwóch możliwości w plikach/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE. | [Dowiedz się więcej o](migrate-appliance.md) urządzeniu Azure Migrate.
**VMware** | **vCenter**: urządzenie wymaga konta vCenter Server z dostępem tylko do odczytu i ma uprawnienia do Virtual Machines > operacji gościa.<br/><br/> **Hosty ESXi**: na hostach ESXi z maszynami wirtualnymi, które chcesz analizować, urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443.
**Zebrane dane** |  Wizualizacja zależności bez agenta działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona. Po rozpoczęciu odnajdywania zależności urządzenie zbiera te dane z maszyn, sondowanie co pięć minut:<br/> — Połączenia protokołu TCP.<br/> — Nazwy procesów z aktywnymi połączeniami.<br/> — Nazwy zainstalowanych aplikacji uruchamiających proces z aktywnymi połączeniami.<br/> -Liczba wykrytych połączeń podczas każdego interwału sondowania.


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie wizualizacji zależności](how-to-create-group-machine-dependencies.md)
- [Wypróbuj wizualizację zależności bez agenta](how-to-create-group-machine-dependencies-agentless.md) dla maszyn wirtualnych VMware.
- Przejrzyj [typowe pytania](common-questions-discovery-assessment.md#what-is-dependency-visualization) dotyczące wizualizacji zależności.


