---
title: Wizualizacja zależności w Azure Migrate | Microsoft Docs
description: Zawiera omówienie obliczeń oceny w usłudze oceny serwera w Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 4b6a140ec428ce3b053c41074f02f65f19b8dc72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102893"
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

Azure Migrate: Ocena serwera ocenia grupy maszyn lokalnych pod kątem migracji na platformę Azure. W celu utworzenia grup można użyć funkcji wizualizacji zależności w obszarze Ocena serwera. Ten artykuł zawiera informacje dotyczące tej funkcji.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="overview"></a>Przegląd

Wizualizacja zależności w ramach oceny serwera umożliwia tworzenie grup o wysokim poziomie pewności na potrzeby oceny migracji. Za pomocą wizualizacji zależności można wyświetlać zależności sieci maszyn i identyfikować powiązane maszyny, które muszą zostać zmigrowane razem z platformą Azure. Ta funkcja jest przydatna w scenariuszach, w których nie są w pełni świadomi maszyn, które stanowią aplikację i muszą zostać zmigrowane razem z platformą Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Upewnij się, że maszyny zostały odnalezione w Azure Migrate; w tym celu można skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Server Assessment. [Dowiedz się więcej](migrate-appliance.md).

## <a name="how-does-it-work"></a>Jak to działa?

Azure Migrate używa rozwiązania [Service map](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach Azure monitor](../log-analytics/log-analytics-overview.md) na potrzeby wizualizacji zależności.
- Aby można było korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy Log Analytics, nowy lub istniejący, z projektem Azure Migrate.
- Obszar roboczy można utworzyć lub dołączyć tylko w ramach tej samej subskrypcji, w której jest tworzony projekt Azure Migrate.
- Aby dołączyć obszar roboczy Log Analytics do projektu:
    1. Na karcie **serwery** w **Azure Migrate: Kafelek Ocena** serwera, kliknij przycisk **Przegląd**.
    2. W obszarze **Przegląd**kliknij strzałkę w dół, aby rozwinąć **podstawowe**elementy.
    3. W **obszarze roboczym pakietu OMS**kliknij pozycję **wymaga konfiguracji**.
    4. W obszarze **Konfiguruj obszar roboczy**Określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego:
    
    ![Dodaj obszar roboczy](./media/how-to-create-group-machine-dependencies/workspace.png)

- Podczas kojarzenia obszaru roboczego uzyskasz możliwość utworzenia nowego obszaru roboczego lub dołączenia istniejącego:
  - Podczas tworzenia nowego obszaru roboczego należy określić nazwę obszaru roboczego. Obszar roboczy jest następnie tworzony w regionie w tej samej lokalizacji [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) co projekt migracji.
  - Po dołączeniu istniejącego obszaru roboczego możesz wybrać wszystkie dostępne obszary robocze w tej samej subskrypcji co projekt migracji. Należy zauważyć, że na liście są wyświetlane tylko te obszary robocze, które zostały utworzone w regionie, w którym [Service map jest obsługiwana](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby możliwe było dołączenie obszaru roboczego, upewnij się, że masz dostęp do obszaru roboczego "czytelnik".

  > [!NOTE]
  > Po dołączeniu obszaru roboczego do projektu nie można go później zmienić.

- Skojarzony obszar roboczy jest otagowany za pomocą **projektu migracji**kluczy i **nazwy projektu**wartości, którego można użyć do wyszukiwania w Azure Portal.
- Aby przejść do obszaru roboczego skojarzonego z projektem, możesz przejść do sekcji **podstawowe** strony **Przegląd** projektu i uzyskać dostęp do obszaru roboczego

    ![Nawigowanie Log Analytics obszarze roboczym](./media/concepts-dependency-visualization/oms-workspace.png)

Aby można było używać wizualizacji zależności, należy pobrać i zainstalować agenty na każdej maszynie lokalnej, która ma zostać przeanalizowana.  

- [Program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi zostać zainstalowany na każdej maszynie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) na temat sposobu instalowania agenta MMA.
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent) musi być zainstalowany na każdej maszynie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) na temat sposobu instalowania agenta zależności.
- Ponadto w przypadku maszyn, które nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.

Te agenci nie są potrzebne na maszynach, które chcesz ocenić, chyba że używasz wizualizacji zależności.

## <a name="do-i-need-to-pay-for-it"></a>Czy muszę uiścić płatność?

Funkcja wizualizacji zależności jest dostępna bez dodatkowych opłat. Korzystanie z funkcji wizualizacji zależności w ramach oceny serwera wymaga Service Map i wymaga skojarzenia obszaru roboczego Log Analytics, nowego lub istniejącego z projektem Azure Migrate. Funkcje wizualizacji zależności w ramach oceny serwera są bezpłatne przez pierwsze 180 dni.

1. Użycie rozwiązań innych niż Service Map w tym Log Analytics obszarze roboczym spowoduje naliczenie [standardowych log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) opłat.
2. Aby obsłużyć scenariusze migracji bez dodatkowych kosztów, rozwiązanie Service Map nie spowoduje naliczania opłat za pierwsze 180 dni od dnia kojarzenia obszaru roboczego Log Analytics z projektem Azure Migrate. Po 180 dniach opłaty zostaną naliczone według standardowych Log Analytics.

Podczas rejestrowania agentów w obszarze roboczym Użyj identyfikatora i klucza podanym przez projekt na stronie Instalowanie kroków agenta.

Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie będzie bezpłatne, a dla każdego węzła zostanie naliczona opłata zgodnie z warstwą płatną Log Analytics obszarze roboczym.

> [!NOTE]
> Funkcja wizualizacji zależności używa Service Map za pośrednictwem Log Analytics obszaru roboczego. Od 28 lutego 2018 z ogłoszeniem Azure Migrate ogólnej dostępności funkcja jest teraz dostępna bez dodatkowych opłat. Należy utworzyć nowy projekt, aby korzystać z obszaru roboczego bezpłatnego użycia. Istniejące obszary robocze przed ogólną dostępnością nadal są płatne, dlatego zalecamy przeniesienie do nowego projektu.

Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Jak mogę zarządzać obszarem roboczym?

Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate. Nie jest on usuwany w przypadku usunięcia projektu Azure Migrate, w którym został utworzony. Jeśli obszar roboczy nie jest już potrzebny, [Usuń go](../azure-monitor/platform/manage-access.md) ręcznie.

Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki
- [Grupowanie maszyn przy użyciu zależności maszyn](how-to-create-group-machine-dependencies.md)
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) na temat często zadawanych pytań dotyczących wizualizacji zależności.
