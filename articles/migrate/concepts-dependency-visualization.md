---
title: Wizualizacja zależności w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczenia dotyczące oceny usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: raynew
ms.openlocfilehash: 5880c98b0f77b75dfb10969311408307b0c4afbd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603366"
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

[Usługi Azure Migrate](migrate-overview.md) ocenia grupy maszyn lokalnych do migracji na platformę Azure. Do grupowania maszyn, możesz użyć wizualizacji zależności. Ten artykuł zawiera informacje o tej funkcji.


## <a name="overview"></a>Przegląd

Wizualizacja zależności w usłudze Azure Migrate służy do tworzenia grup dla oceny migracji, z większą pewnością. Za pomocą wizualizacji zależności można wyświetlić zależności sieci określonych maszyn, albo w grupie maszyn. Jest to przydatne do upewnij się, że żadne funkcje lub zostały utracone (lub zapomnienia maszyn) w ramach procesu migracji, gdy aplikacje i obciążenia są uruchamiane na wielu komputerach.  

## <a name="how-does-it-work"></a>Jak to działa?

Usługa Azure Migrate korzysta [rozwiązania Service Map](../operations-management-suite/operations-management-suite-service-map.md) rozwiązania [usługi Log Analytics](../log-analytics/log-analytics-overview.md) dla wizualizacji zależności.
- Podczas tworzenia projektu migracji platformy Azure, obszar roboczy usługi Log Analytics jest tworzony w ramach subskrypcji.
- Nazwa obszaru roboczego jest nazwą, należy określić dla projektu migracji, z prefiksem **migracji-** i opcjonalnie sufiksami z liczbą.
- Przejdź do obszaru roboczego usługi Log Analytics z **Essentials** części projektu **Przegląd** strony.
- Utworzonego obszaru roboczego jest oznaczony przy użyciu klucza **projektu migracji**, a wartość **Nazwa projektu**. Służy to do wyszukiwania w witrynie Azure portal.  

    ![Obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Aby użyć wizualizacji zależności, musisz pobrać i zainstalować agentów na każdej maszynie w środowisku lokalnym, w której mają być analizowane.  

## <a name="do-i-need-to-pay-for-it"></a>Czy muszę płacić za jej?

Usługa Azure Migrate jest dostępna bez dodatkowych opłat. Korzystanie z funkcji wizualizacji zależności w usłudze Azure Migrate wymaga mapy usługi. Podczas tworzenia projektu usługi Azure Migrate usługa Azure Migrate automatycznie utworzy nowy obszar roboczy usługi Log Analytics w Twoim imieniu.

> [!NOTE]
> Funkcji wizualizacji zależności rozwiązania Service map za pomocą obszaru roboczego usługi Log Analytics. Od 28 lutego 2018 r. za pomocą anons dotyczący aplikacji usługi Azure Migrate ogólnej dostępności ta funkcja jest teraz dostępne bez dodatkowych opłat. Musisz utworzyć nowy projekt, aby użyć bezpłatnego użycia obszaru roboczego. Istniejących obszarów roboczych przed availaibility ogólne są nadal chargable, dlatego zaleca się migrację do nowego projektu.

1. Użyj dowolnego rozwiązania niż Service Map w tym obszarze roboczym usługi Log Analytics będą naliczane są standardowe opłaty za usługi Log Analytics.
2. Aby zapewnić obsługę scenariuszy migracji, bez ponoszenia dodatkowych kosztów, rozwiązanie mapy usługi nie spowoduje naliczenia żadnych opłat przez pierwsze 180 dni od utworzenia projektu usługi Azure Migrate, po upływie którego będą naliczane standardowe opłaty.
3. Tylko obszaru roboczego, które zostały utworzone w ramach tworzenia projektu będą bezpłatne do użytku.

Po zarejestrowaniu agentów do obszaru roboczego, użyj Identyfikatora jak i klucz podany przez projekt, na stronie kroki instalacji agenta. Nie można użyć istniejącego obszaru roboczego i skojarzyć go z projektu usługi Azure Migrate.

Po usunięciu projektu Azure Migrate nie zostanie usunięty wraz z jej obszaru roboczego. Po usunięciu projektu i użycia rozwiązania Service Map, nie będą bezpłatne każdego węzła spowoduje obciążenie opłatami zgodnie z warstwy płatnej obszaru roboczego usługi Log Analytics.

Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Jak zarządzać obszaru roboczego?

Można użyć obszaru roboczego usługi Log Analytics spoza usługi Azure Migrate. Nie są usuwane po usunięciu projektu migracji, w której został utworzony. Jeśli nie potrzebujesz już obszar roboczy, [ją usunąć](../log-analytics/log-analytics-manage-access.md) ręcznie.

Nie usuwaj tego obszar roboczy utworzony przez usługę Azure Migrate, o ile nie usuniesz projekt migracji. Jeśli to zrobisz, zależności nie działają zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki

[Grupowanie maszyn za pomocą zależności maszyn](how-to-create-group-machine-dependencies.md)
