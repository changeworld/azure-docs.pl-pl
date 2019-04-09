---
title: Informacje o cyklu życia planu
description: Informacje o cyklu życia, które przechodzi planu i szczegółowe informacje o każdym etapie.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a57085fa37efd56a46b740d8cbc4278dc53cf39f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266617"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Informacje o cyklu życia planu platformy Azure

Podobnie jak wiele zasobów na platformie Azure planu w plany usługi Azure ma typowe i fizyczne cykl życia. Ich tworzenia, wdrożona i na końcu usuwane po nie są już potrzebne lub istotne.
Schematy obsługuje operacje standardowego cyklu życia. Następnie kompiluje na nich, aby zapewnić dodatkowe poziomy stanu, obsługujące typowe ciągłej integracji i ciągłego wdrażania potoków dla organizacji, które zarządzają infrastruktury jako kodu — kluczowy element w metodyce DevOps.

Aby w pełni zrozumieć planu i etapów, omówimy standardowego cyklu życia:

> [!div class="checklist"]
> - Tworzenie i edytowanie planu
> - Publikowanie planu
> - Tworzenie i edytowanie nową wersję planu
> - Publikowanie nową wersję planu
> - Usuwanie określoną wersję planu
> - Trwa usuwanie planu

## <a name="creating-and-editing-a-blueprint"></a>Tworzenie i edytowanie planu

Podczas tworzenia planu dodawania artefaktów, Zapisz do grupy zarządzania lub subskrypcji, a następnie podać unikatową nazwę i wersję unikatowy. Planu jest teraz w **projekt** tryb i jeszcze nie można przypisać. W **projekt** tryb, nadal można go aktualizować i zmienić.

Element nigdy nie opublikowane planu w **projekt** tryb Wyświetla inną ikonę na **definicji planu** strony niż te, które zostały **opublikowano**. **Najnowszej wersji** jest wyświetlany jako **projekt** podczas nigdy nie są publikowane schematy.

Tworzenie i edytowanie planu z [witryny Azure portal](../create-blueprint-portal.md#create-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikowanie planu

Gdy wszystkie planowane zmiany zostały wprowadzone do planu w **projekt** tryb, może być **opublikowano** i dostępne do przypisania. **Opublikowano** wersję planu nie może być zmieniona.
Raz **opublikowano**, planu wyświetlane z ikoną różnych niż **projekt** plany i wyświetla numer wersji podana w **najnowszej wersji** kolumny.

Publikuj plan z [witryny Azure portal](../create-blueprint-portal.md#publish-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Tworzenie i edytowanie nową wersję planu

A **opublikowano** wersję planu nie może być zmieniona. Jednak nową wersję planu można dodać do istniejącego planu i zmodyfikować odpowiednio do potrzeb. Należy wprowadzić zmiany do istniejącego planu, edytując je. Podczas zapisywania nowych zmian planu ma teraz **nieopublikowane zmiany**. Te zmiany są nowe **projekt** wersję planu.

Edytuj plan z [witryny Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikowanie nową wersję planu

Każdy edytowaną wersją planu musi być **opublikowano** przed może ona zostać przypisana. Gdy **nieopublikowane zmiany** zostały wprowadzone do planu, ale nie **opublikowano**, **publikowania planu** przycisk jest dostępny na stronie edycji planu. Jeśli przycisk jest niewidoczny, planu został już **opublikowano** i nie ma **nieopublikowane zmiany**.

> [!NOTE]
> Pojedynczego planu może mieć wiele **opublikowano** wersje, które każdy może przypisać do subskrypcji.

Aby opublikować planu z **nieopublikowane zmiany**, Zastosuj te same kroki publikowania nowego planu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Usuwanie określoną wersję planu

Każda wersja planu jest obiektem unikatowy i może być indywidualnie **opublikowano**. W efekcie mogą być również usuwane każdą wersję planu. Usuwanie wersję planu nie ma żadnego wpływu na inne wersje tego planu.

> [!NOTE]
> Nie jest możliwe usuwanie planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** ze strony po lewej i użyj opcji filtru, można zlokalizować planu, aby usunąć wersję. Kliknij go, aby otworzyć stronę edycji.

1. Kliknij przycisk **opublikowane wersje** kartę i Znajdź wersję, którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy w wersji do usunięcia i wybierz **Usuń tę wersję**.

## <a name="deleting-the-blueprint"></a>Trwa usuwanie planu

Plan core mogą być również usuwane. Usunięcie planu core spowoduje również usunięcie wszelkich planu wersje tego planu, w tym **projekt** i **opublikowano** schematy. Jako usuwanie wersję planu, usuwanie planu podstawowego nie powoduje usunięcia istniejące przypisania z dowolnej wersji planu.

> [!NOTE]
> Nie jest możliwe usuwanie planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

Usuń plan za pomocą [witryny Azure portal](../create-blueprint-portal.md#delete-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Przypisania

Istnieje kilka punktów podczas cyklu życia, który umożliwi można przypisać do subskrypcji. Po trybie wersji planu **opublikowano**, a następnie w tej wersji można przypisać do subskrypcji. Ten cykl życia temu wersji planu używanych i aktywnie przypisany podczas jest obecnie sporządzana nowszej wersji.

Ponieważ wersje schematy są przypisywane, jest ważne dowiedzieć się, gdy są przydzielone i parametrami, które zostały przypisane przy użyciu. Parametry mogą być statyczne lub dynamiczne. Aby dowiedzieć się więcej, zobacz [statycznych i dynamicznych parametrów](parameters.md).

### <a name="updating-assignments"></a>Aktualizowanie przypisań

Po przypisaniu planu można zaktualizować przypisania. Istnieje kilka powodów, dla aktualizowanie istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowanie zasobów](resource-locking.md)
- Zmień wartość właściwości [parametrów dynamicznych](parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowano** wersję planu

Aby dowiedzieć się więcej, zobacz temat [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).