---
title: Zrozumienie blokowanie zasobów
description: Informacje o opcjach blokowania, aby chronić zasoby podczas przypisywania planu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2e281896d45ada8010f24a1f18265a8cdd523d31
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696997"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania w plany usługi Azure resource

Tworzenie spójnego środowiska na dużą skalę jest tylko naprawdę przydatne, jeśli istnieje mechanizm w celu zachowania spójności. W tym artykule opisano, jak blokowania zasobów działa w plany usługi Azure.

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania odnosi się do przypisania planu i ma trzy opcje: **Nie blokuj**, **tylko do odczytu**, lub **nie usuwaj**. Tryb blokowania jest skonfigurowane podczas wdrażania artefaktu podczas przypisywania planu. Można ustawić inny tryb blokowania, aktualizując przypisanie planu.
Blokowanie trybach, jednak nie można zmienić poza schematy.

Zasoby utworzone w ramach artefaktów na przypisanie planu ma cztery stany: **Nie jest zablokowany**, **tylko do odczytu**, **nie może edytować / usuwać**, lub **nie można usunąć**. Każdy typ artefaktu może być w **nie zablokowany** stanu. Poniższa tabela może służyć do określenia stanu zasobu:

|Tryb|Typ zasobu artefaktu|Stan|Opis|
|-|-|-|-|
|Nie blokuj|*|Nie jest zablokowany|Zasoby nie są chronione przez schematy. Ten stan jest również używany do zasoby dodane do **tylko do odczytu** lub **nie usuwaj** artefaktu grupy zasobów z poza przypisanie planu.|
|Tylko do odczytu|Grupa zasobów|Nie można edytować / Delete|Grupa zasobów jest tylko do odczytu i nie można zmodyfikować tagów w grupie zasobów. **Nie jest zablokowany** zasoby mogą być dodane, przeniesiony, zmieniony lub usunięty z tej grupy zasobów.|
|Tylko do odczytu|Grupa zasobów spoza|Tylko do odczytu|Zasób nie można zmienić w dowolny sposób — bez zmian i nie można usunąć.|
|Nie usuwaj|*|Nie można usunąć|Zasoby mogą ulec zmianie, ale nie można jej usunąć. **Nie jest zablokowany** zasoby mogą być dodane, przeniesiony, zmieniony lub usunięty z tej grupy zasobów.|

## <a name="overriding-locking-states"></a>Zastępowanie stany blokowania

Zazwyczaj można niepowołanym odpowiedni [kontroli dostępu opartej na rolach](../../../role-based-access-control/overview.md) (RBAC) w ramach subskrypcji, takie jak rola "Właściciel", być może zmienić lub usunąć dowolnego zasobu. Dostęp nie schematy stosuje blokowania przypisania wdrożonej w ramach. Jeśli ustawiono przypisanie z **tylko do odczytu** lub **nie usuwaj** opcji, a nie nawet subskrypcji właściciela można wykonać akcji zablokowanych chronionego zasobu.

Ta miara zabezpieczeń chroni spójność zdefiniowanych planu i środowiska, który został zaprojektowany do utworzenia z przypadkowego lub programowy usuwania lub zmiany.

## <a name="removing-locking-states"></a>Stany blokady usuwania

Jeśli okaże się to konieczne, można zmodyfikować lub usunąć zasób chronione przez przypisanie, istnieją dwa sposoby, aby to zrobić.

- Aktualizowanie przypisania planu w celu blokowania tryb **nie blokady**
- Usuwanie przypisania planu

Usunięcie przypisania blokad utworzone przez schematy są usuwane. Jednakże zasób jest pozostawione i czy muszą zostać usunięte w normalny sposób.

## <a name="how-blueprint-locks-work"></a>Jak planu blokuje pracy

RBAC [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) Odmów, akcja jest stosowane do artefaktu zasobów podczas przypisywania planu, jeśli wybrane przypisania **tylko do odczytu** lub **nie usuwaj** Opcja. Akcja odmowy jest dodawany przez zarządzaną tożsamością przypisanie planu i może zostać usunięty tylko ze źródeł artefaktów w tej samej tożsamości zarządzanej. To zabezpieczenie wymusza mechanizm blokowania i uniemożliwia usunięcie blokady plan poza schematy.

> [!IMPORTANT]
> Usługa Azure Resource Manager buforuje Szczegóły przypisania roli dla maksymalnie 30 minut. Odmów co w efekcie przypisania odmowy Akcja zasobów planu natychmiast może nie być w pełnego wpływu. W tym okresie czasu może istnieć możliwość usuwania zasobu mają być chronione przez planu blokad.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md)