---
title: Zrozumienie blokowania w plany usługi Azure resource
description: Informacje o opcjach blokowania, aby chronić zasoby podczas przypisywania planu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973256"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania w plany usługi Azure resource

Tworzenie spójnego środowiska na dużą skalę jest tylko naprawdę przydatne, jeśli istnieje mechanizm, aby upewnić się, że spójności jest trwały. W tym artykule opisano, jak blokowania zasobów działa w plany usługi Azure.

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania odnosi się do przypisania planu i ma tylko dwie opcje: **Brak** lub **wszystkie zasoby**. Podczas przypisywania planu jest konfigurowana i nie można zmienić po pomyślnie zastosowano przypisania do subskrypcji.

Zasoby utworzone w ramach definicji artefaktu w ramach planu, przypisany do subskrypcji mają trzy stany: **nie zablokowany**, **tylko do odczytu**, lub **nie może edytować / usuwać**. Dowolny rodzaj artefaktu może mieć **nie zablokowany** stanu. Artefakty grupy zasobów nie są jednak jako **tylko do odczytu** i grupy zasobów są jako **nie może edytować / usuwać**. Jest to ważna różnica, w jaki sposób te zasoby są zarządzane.

**Tylko do odczytu** stan jest dokładnie zgodnie z definicją: zasobu nie można zmienić w dowolny sposób — bez zmian i nie można usunąć. **Nie może edytować / usuwać** jest bardziej złożonych ze względu na charakter "container" grupy zasobów. Obiektu grupy zasobów jest tylko do odczytu, ale istnieje możliwość tworzenia, aktualizacji i usuwania zasobów w ramach zasobu grupy — tak długo, jak nie są one częścią dowolnego przypisanie planu z **tylko do odczytu** stan blokady.

## <a name="overriding-locking-states"></a>Zastępowanie stany blokowania

Gdy istnieje zazwyczaj możliwość osoby z odpowiednią [kontroli dostępu opartej na rolach](../../../role-based-access-control/overview.md) (RBAC) w ramach subskrypcji, takie jak rola "Właściciel", aby obsługiwać zmienianie lub usuwanie dowolnego zasobu nie jest to wymagane podczas plany ma zastosowanie, blokowanie przypisania wdrożonej w ramach. Jeśli przypisanie została ustawiona za pomocą **blokady** opcji, a nie nawet subskrypcji zmienić właściciela dołączone zasoby.

Chroni to spójność zdefiniowanych planu i środowiska, który został zaprojektowany do utworzenia z przypadkowego lub programowy usuwania lub zmiany.

## <a name="removing-locking-states"></a>Stany blokady usuwania

Jeśli okaże się to konieczne usunąć zasoby utworzone przez przypisanie, jedynym sposobem, aby je usunąć, jest do usunięcia pierwszego przypisania. Usunięcie przypisania blokad utworzone przez schematy są usuwane. Zasób, jednak jest pozostawione i będą musiały zostać usunięte w normalny sposób przez inna osoba z odpowiednimi uprawnieniami.

## <a name="how-blueprint-locks-work"></a>Jak planu blokuje pracy

Rola RBAC `denyAssignments` jest stosowane do artefaktu zasobów podczas przypisywania planu, jeśli wybrane przypisania **blokady** opcji. Ta rola jest dodawany przez zarządzaną tożsamością przypisanie planu i może zostać usunięty tylko ze źródeł artefaktów w tej samej tożsamości zarządzanej. Wymusza mechanizm blokowania i zapobiega próby usunięcia blokady plan poza schematy. Usunięcie roli, a blokada jest możliwe tylko przez usunięcie przypisania planu, który może zostać wykonana tylko przez osoby, które mają odpowiednie uprawnienia.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [planu cyklu życia](lifecycle.md)
- Opis sposobu użycia [statycznych i dynamicznych parametrów](parameters.md)
- Dowiedz się, jak dostosować [planu sekwencjonowania](sequencing-order.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania planu z [Ogólne rozwiązywanie problemów](../troubleshoot/general.md)