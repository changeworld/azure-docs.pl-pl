---
title: Wizualizacja zależności w migracji Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczeń oceny w usłudze Azure migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 2b6b3e78436a215e64894b1102c4e109f72c896d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="dependency-visualization"></a>Wizualizacja zależności

[Migracji Azure](migrate-overview.md) usług ocenia grup lokalnych maszyn do migracji do usługi Azure. Aby zgrupować maszyny, można użyć wizualizacji zależności. Ten artykuł zawiera informacje o tej funkcji.


## <a name="overview"></a>Przegląd

Wizualizacja zależności w Azure migracji służy do tworzenia grup dla migracji oceny o większej pewności. Za pomocą wizualizacji zależności można wyświetlić zależności sieci przez określone maszyny lub dla grupy komputerów. Jest to przydatne w celu zapewnienia, że żadne funkcje lub zostały utracone (lub zapomnienia hasła maszyny) w procesie migracji, gdy aplikacje i obciążenia uruchomione na wielu komputerach.  

## <a name="how-does-it-work"></a>Jak to działa?

Azure używa migracji [mapy usługi](../operations-management-suite/operations-management-suite-service-map.md) rozwiązania [analizy dzienników](../log-analytics/log-analytics-overview.md) dla zależności wizualizacji.
- Podczas tworzenia projektu migracji Azure obszaru roboczego analizy dzienników jest tworzony w ramach subskrypcji.
- Nazwa obszaru roboczego jest nazwą, określ dla projektu migracji, i jest poprzedzony prefiksem **migracji-** i opcjonalnie sufiks z numerem. 
- Przejdź do obszaru roboczego analizy dzienników z **Essentials** sekcji projektu **omówienie** strony.
- Obszar roboczy utworzony zostanie oznaczony przy użyciu klucza **MigrateProject**i wartość **Nazwa projektu**. Te umożliwia wyszukiwanie w portalu Azure.  

    ![Obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Aby korzystać z wizualizacji zależności, musisz pobrać i zainstalować agentów na każdej maszynie lokalnej, które mają być analizowane.  

## <a name="do-i-need-to-pay-for-it"></a>Należy dokonać jego?

Usługa Azure Migrate jest dostępna bez dodatkowych opłat. Korzystanie z funkcji wizualizacji zależności w Azure migracji wymagają mapy usługi. Podczas tworzenia projektu migracji Azure Azure migracji automatycznie utworzy nowy obszar roboczy analizy dzienników w Twoim imieniu.

> [!NOTE]
> Funkcja wizualizacji zależności używa mapy usługi przy użyciu obszaru roboczego analizy dzienników. Od 28 lutego 2018 z anonsu migracji Azure ogólnie funkcję jest teraz dostępna bez dodatkowych opłat. Musisz utworzyć nowy projekt, aby użyć użycia wolnego obszaru roboczego. Istniejących obszarów roboczych przed availaibility ogólne są nadal chargable, dlatego zalecamy przejście do nowego projektu.

1. Korzystanie z rozwiązań innych niż Mapy usługi w ramach tego obszaru roboczego analizy dzienników będą naliczane standardowe opłaty analizy dzienników. 
2. Na potrzeby obsługi scenariuszy migracji bez ponoszenia dodatkowych kosztów, rozwiązanie mapy usług nie będą naliczane opłaty na pierwszy okres 180 dni od utworzenia projektu Azure migracji, po upływie którego standardowe opłaty będą stosowane.
3. Tylko obszaru roboczego, które są tworzone w ramach tworzenia projektu będzie bezpłatnie do użytku.

Podczas rejestrowania agentów do obszaru roboczego, użyj identyfikator i klucz podany przez projekt na stronie kroki instalacji agenta. Nie można użyć istniejący obszar roboczy i skojarzyć go z projektu Azure migracji.

Po usunięciu projektu Azure migracji nie zostanie usunięta wraz z jego obszaru roboczego. Po usunięciu projektu, użycia mapy usług nie będzie wolny, i każdy węzeł zostanie naliczona opłata zgodnie z płatnych warstwy obszaru roboczego analizy dzienników.

Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Jak zarządzać obszaru roboczego?

Można użyć obszaru roboczego analizy dzienników poza Azure migracji. Nie zostanie usunięta po usunięciu projektu migracji, w której został utworzony. Jeśli nie są już potrzebne obszaru roboczego [go usunąć](../log-analytics/log-analytics-manage-access.md) ręcznie.

Nie należy usuwać obszar roboczy utworzony przez Azure migracji, o ile nie usuniesz migracji projektu. Jeśli to zrobisz, zależności nie działają zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki

[Grupy maszyn przy użyciu zależności maszyny](how-to-create-group-machine-dependencies.md)