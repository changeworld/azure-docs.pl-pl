---
title: Zrozumienie blokowania w plany usługi Azure resource
description: Informacje o opcjach blokowania, aby chronić zasoby podczas przypisywania planu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139446"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania w plany usługi Azure resource

Tworzenie spójnego środowiska na dużą skalę jest tylko naprawdę przydatne, jeśli istnieje mechanizm w celu zachowania spójności. W tym artykule opisano, jak blokowania zasobów działa w plany usługi Azure.

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania odnosi się do przypisania planu i ma tylko dwie opcje: **Brak** lub **wszystkie zasoby**. Tryb blokowania jest konfigurowana podczas przypisywania planu i nie można zmienić po pomyślnie zastosowano przypisania do subskrypcji.

Zasoby utworzone w ramach artefaktów na przypisanie planu mają trzy stany: **nie zablokowany**, **tylko do odczytu**, lub **nie może edytować / usuwać**. Każdego artefaktu może znajdować się w **nie zablokowany** stanu. Jednak grupy niezwiązana z zasobem artefakty mają **tylko do odczytu** i grup zasobów **nie może edytować / usuwać** stanów. Różnica ta jest to ważna różnica, w jaki sposób te zasoby są zarządzane.

**Tylko do odczytu** stan jest dokładnie zgodnie z definicją: zasobu nie można zmienić w dowolny sposób — bez zmian i nie można usunąć. **Nie może edytować / usuwać** jest bardziej złożonych ze względu na charakter "container" grupy zasobów. Obiekt grupy zasobów jest tylko do odczytu, ale można dokonać zmian — zablokowane zasoby w grupie zasobów.

## <a name="overriding-locking-states"></a>Zastępowanie stany blokowania

Zazwyczaj można niepowołanym odpowiedni [kontroli dostępu opartej na rolach](../../../role-based-access-control/overview.md) (RBAC) w ramach subskrypcji, takie jak rola "Właściciel", być może zmienić lub usunąć dowolnego zasobu. Dostęp nie schematy stosuje blokowania przypisania wdrożonej w ramach. Jeśli przypisanie została ustawiona za pomocą **blokady** opcji, a nie nawet subskrypcji zmienić właściciela dołączone zasoby.

Ta miara zabezpieczeń chroni spójność zdefiniowanych planu i środowiska, który został zaprojektowany do utworzenia z przypadkowego lub programowy usuwania lub zmiany.

## <a name="removing-locking-states"></a>Stany blokady usuwania

Jeśli okaże się to konieczne usunąć zasoby utworzone przez przypisanie, sposób, aby je usunąć jest pierwszym Usuń przypisanie. Usunięcie przypisania blokad utworzone przez schematy są usuwane. Jednakże zasób jest pozostawione i czy muszą zostać usunięte w normalny sposób.

## <a name="how-blueprint-locks-work"></a>Jak planu blokuje pracy

Rola RBAC `denyAssignments` jest stosowane do artefaktu zasobów podczas przypisywania planu, jeśli wybrane przypisania **blokady** opcji. Ta rola jest dodawany przez zarządzaną tożsamością przypisanie planu i może zostać usunięty tylko ze źródeł artefaktów w tej samej tożsamości zarządzanej. To zabezpieczenie wymusza mechanizm blokowania i uniemożliwia usunięcie blokady plan poza schematy. Usunięcie roli, a blokada jest możliwe tylko przez usunięcie przypisania planu, który może zostać wykonana tylko przez osoby, które mają odpowiednie uprawnienia.

> [!IMPORTANT]
> Usługa Azure Resource Manager buforuje Szczegóły przypisania roli dla maksymalnie 30 minut. W rezultacie `denyAssignments` na plan zasobów natychmiast prawdopodobnie pełnego wpływu. W tym okresie czasu może istnieć możliwość usuwania zasobu mają być chronione przez planu blokad.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md)