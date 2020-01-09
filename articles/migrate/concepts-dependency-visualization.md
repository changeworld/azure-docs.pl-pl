---
title: Wizualizacja zależności w usłudze Azure Migrate
description: Zawiera omówienie obliczeń oceny w usłudze oceny serwera w Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 828f3078c992bed4fa556a374468e69dd58ebc04
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75494510"
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

W tym artykule opisano funkcję wizualizacji zależności w Azure Migrate: Ocena serwera.

Wizualizacja zależności ułatwia zrozumienie zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji. Mapowanie zależności zazwyczaj jest stosowane, gdy chcesz ocenić komputery z wyższym poziomem zaufania.

- W Azure Migrate: Ocena serwera umożliwia zebranie maszyn w grupach w celu oceny. Grupy zwykle składają się z maszyn, które mają być migrowane ze sobą, a Wizualizacja zależności ułatwia sprawdzanie zależności maszyn, dzięki czemu można dokładnie grupować maszyny.
- Korzystając z wizualizacji, można odnajdywać systemy zależne, które muszą być migrowane razem. Możesz określić, czy uruchomione systemy są nadal używane, czy można zlikwidować systemy zamiast migrować.
- Wizualizacja zależności pomaga upewnić się, że nic nie zostało pozostawione, i uniknąć awarii podczas migracji.
- Ta funkcja jest szczególnie przydatna, jeśli nie znasz całkowicie maszyn, które są częścią aplikacji, a tym samym należy migrować ze sobą na platformę Azure.


> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="agent-based-and-agentless"></a>Oparte na agentach i bez wykorzystania agentów

Dostępne są dwie opcje wdrażania wizualizacji zależności:

- **Wizualizacja zależności bez agenta**: Ta opcja jest obecnie dostępna w wersji zapoznawczej, a jej dostępność dotyczy tylko maszyn wirtualnych VMware. Nie wymaga to instalowania żadnych agentów na komputerach. 
    - Działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona. [Dowiedz się więcej](how-to-create-group-machine-dependencies-agentless.md).
Po rozpoczęciu odnajdywania zależności urządzenie zbiera dane z maszyn z interwałem sondowania równym pięć minut.
    - Zbierane są następujące dane:
        - Połączenia protokołu TCP
        - Nazwy procesów, które mają aktywne połączenia
        - Nazwy zainstalowanych aplikacji, które uruchamiają powyższe procesy
        - Nie. wykrytych połączeń podczas każdego interwału sondowania
- **Wizualizacja zależności oparta na agentach**: Aby użyć wizualizacji zależności opartej na agentach, należy pobrać i zainstalować następujących agentów na każdej maszynie lokalnej, która ma zostać przeanalizowana.  
    - [Program Microsoft Monitoring Agent (MMA) ](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi być zainstalowany na każdej maszynie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) na temat sposobu instalowania agenta MMA.
    - [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent) musi być zainstalowany na każdej maszynie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) na temat sposobu instalowania agenta zależności.
    - Ponadto w przypadku maszyn, które nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.

## <a name="agent-based-requirements"></a>Wymagania oparte na agentach

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Co jest potrzebne do wdrożenia wizualizacji zależności?

Przed wdrożeniem wizualizacji zależności należy mieć projekt Azure Migrate na miejscu, z Azure Migrate: Narzędzie do oceny serwera dodane do projektu. Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych.

[Dowiedz się więcej](how-to-assess.md) o dodawaniu narzędzia i wdrażaniu urządzenia dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md), programu [VMware](how-to-set-up-appliance-vmware.md)lub serwerów fizycznych.


### <a name="how-does-it-work"></a>Jak to działa?

Azure Migrate używa rozwiązania [Service map](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach Azure monitor](../log-analytics/log-analytics-overview.md) na potrzeby wizualizacji zależności.

- Aby można było korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy Log Analytics (nowy lub istniejący) z projektem Azure Migrate.
- Obszar roboczy musi znajdować się w tej samej subskrypcji, co w przypadku tworzenia projektu Azure Migrate.
- Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem. Należy również pamiętać, że obszar roboczy musi znajdować się w regionie, w którym [Service map jest obsługiwana](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
- W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.

    ![Nawigowanie Log Analytics obszarze roboczym](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Czy muszę uiścić płatność?

Wizualizacja zależności wymaga Service Map i skojarzonego obszaru roboczego Log Analytics. 

- Service Map rozwiązanie nie wiąże się z żadnymi opłatami za pierwsze 180 dni. Jest to dzień, w którym skojarzono obszar roboczy Log Analytics z projektem Azure Migrate.
- Po 180 dniach opłaty zostaną naliczone według standardowych Log Analytics.
- Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [standardowych log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) opłat.
- Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z warstwą płatną Log Analytics obszarze roboczym.

Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności w dniu 28 lutego 2018, być może nastąpiły dodatkowe opłaty za Service Map. Aby upewnić się, że płacisz tylko po 180 dniach, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze są nadal dostępne.



### <a name="how-do-i-manage-the-workspace"></a>Jak mogę zarządzać obszarem roboczym?

- Podczas rejestrowania agentów w obszarze roboczym należy używać identyfikatora i klucza dostarczonego przez projekt Azure Migrate.
- Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.
- Jeśli usuniesz skojarzony projekt Azure Migrate, obszar roboczy nie zostanie automatycznie usunięty. Należy [usunąć je ręcznie](../azure-monitor/platform/manage-access.md).
- Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki
- [Grupowanie maszyn przy użyciu zależności maszyn](how-to-create-group-machine-dependencies.md)
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) na temat często zadawanych pytań dotyczących wizualizacji zależności.


