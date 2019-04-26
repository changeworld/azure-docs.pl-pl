---
title: Wizualizacja zależności w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczenia dotyczące oceny usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201565"
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

[Usługi Azure Migrate](migrate-overview.md) ocenia grupy maszyn lokalnych do migracji na platformę Azure. Skorzystać z funkcji wizualizacji zależności w usłudze Azure Migrate, aby utworzyć grupy. Ten artykuł zawiera informacje o tej funkcji.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="overview"></a>Omówienie

Wizualizacja zależności w usłudze Azure Migrate służy do tworzenia grup o wysokim poziomie pewności dla oceny migracji. Za pomocą wizualizacji zależności, można wyświetlać sieci zależności maszyn i zidentyfikować powiązane maszyny, które muszą być migrowane razem na platformie Azure. Ta funkcja jest przydatne w scenariuszach, w której nie jesteś świadomość maszyn, które stanowią aplikacji i muszą być migrowane razem na platformie Azure.

## <a name="how-does-it-work"></a>Jak to działa?

Usługa Azure Migrate korzysta [rozwiązania Service Map](../operations-management-suite/operations-management-suite-service-map.md) rozwiązania [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) dla wizualizacji zależności.
- Aby korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy usługi Log Analytics, nowej lub istniejącej z projektem migracji platformy Azure.
- Możesz tworzyć lub dołączyć obszaru roboczego w tej samej subskrypcji, w której jest tworzony projekt migracji.
- Aby dołączyć obszar roboczy usługi Log Analytics z projektem, przejdź do **Essentials** części projektu **Przegląd** strony, a następnie kliknij przycisk **wymaga konfiguracji**

    ![Skojarzyć obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Podczas tworzenia skojarzenia obszar roboczy, uzyskasz opcję, aby utworzyć nowy obszar roboczy lub dołączenie istniejącego:
  - Gdy tworzysz nowy obszar roboczy, musisz określić nazwę obszaru roboczego. Obszar roboczy zostanie utworzony w regionie, w tym samym [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migracji.
  - Po dołączeniu istniejącego obszaru roboczego, możesz wybrać wszystkie dostępne obszary robocze w tej samej subskrypcji co projekt migracji. Należy pamiętać, że tylko te obszary robocze są wymienione, które zostały utworzone w regionie, w którym [Mapa usługi jest obsługiwany](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby można było połączyć obszar roboczy, upewnij się, że masz "Reader" dostęp do obszaru roboczego.

  > [!NOTE]
  > Po dołączeniu obszaru roboczego do projektu nie można go zmienić później.

- Skojarzone obszaru roboczego jest oznaczony przy użyciu klucza **projektu migracji**i wartość **Nazwa projektu**, które służy do wyszukiwania w witrynie Azure portal.
- Aby przejść do obszaru roboczego skojarzonego z projektem, możesz przejść do **Essentials** części projektu **Przegląd** strony, a dostęp do obszaru roboczego

    ![Przejdź do obszaru roboczego usługi Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Aby użyć wizualizacji zależności, musisz pobrać i zainstalować agentów na każdej maszynie w środowisku lokalnym, w której mają być analizowane.  

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi być zainstalowany na każdym komputerze.
- [Agenta zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musi być zainstalowany na każdym komputerze.
- Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować bramę usługi Log Analytics na nich.

Nie potrzebujesz tych agentów na komputerach, które chcesz ocenić, jeśli nie używasz wizualizacji zależności.

## <a name="do-i-need-to-pay-for-it"></a>Czy muszę płacić za jej?

Usługa Azure Migrate jest dostępna bez dodatkowych opłat. Korzystanie z funkcji wizualizacji zależności w usłudze Azure Migrate wymaga rozwiązania Service Map, a możesz skojarzyć obszar roboczy usługi Log Analytics, nowej lub istniejącej, za pomocą usługi Azure Migrate projektu. Funkcji wizualizacji zależności w usłudze Azure Migrate jest bezpłatne przez pierwsze 180 dni w usłudze Azure Migrate.

1. Użyj dowolnego rozwiązania niż Service Map w tym obszarze roboczym usługi Log Analytics spowoduje naliczenie opłaty za [standardowa usługa Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) opłaty.
2. Aby zapewnić obsługę scenariuszy migracji, bez ponoszenia dodatkowych kosztów, rozwiązania mapy usługi nie spowoduje naliczenia żadnych opłat dla przez pierwsze 180 dni od dnia skojarzyć obszar roboczy usługi Log Analytics za pomocą usługi Azure Migrate projektu. Po upływie 180 dni będą naliczane standardowe opłaty usługi Log Analytics.

Po zarejestrowaniu agentów do obszaru roboczego, użyj Identyfikatora jak i klucz podany przez projekt, na stronie kroki instalacji agenta.

Po usunięciu projektu Azure Migrate nie zostanie usunięty wraz z jej obszaru roboczego. Po usunięciu projektu, użycia rozwiązania Service Map, nie będą bezpłatne i każdego węzła spowoduje obciążenie opłatami zgodnie z warstwy płatnej obszaru roboczego usługi Log Analytics.

> [!NOTE]
> Funkcji wizualizacji zależności rozwiązania Service map za pomocą obszaru roboczego usługi Log Analytics. Od 28 lutego 2018 r. za pomocą anons dotyczący aplikacji usługi Azure Migrate ogólnej dostępności ta funkcja jest teraz dostępne bez dodatkowych opłat. Musisz utworzyć nowy projekt, aby użyć bezpłatnego użycia obszaru roboczego. Istniejących obszarów roboczych przed ogólnej dostępności są nadal mogą być obciążane i, dlatego zaleca się migrację do nowego projektu.

Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Jak zarządzać obszaru roboczego?

Można użyć obszaru roboczego usługi Log Analytics spoza usługi Azure Migrate. Nie są usuwane po usunięciu projektu migracji, w której został utworzony. Jeśli nie potrzebujesz już obszar roboczy, [ją usunąć](../azure-monitor/platform/manage-access.md) ręcznie.

Nie usuwaj tego obszar roboczy utworzony przez usługę Azure Migrate, o ile nie usuniesz projekt migracji. Jeśli to zrobisz, funkcji wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki
- [Grupowanie maszyn za pomocą zależności maszyn](how-to-create-group-machine-dependencies.md)
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) temat — często zadawane pytania na wizualizacji zależności.
