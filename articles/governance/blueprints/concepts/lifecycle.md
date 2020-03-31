---
title: Zrozumienie cyklu życia planu
description: Dowiedz się więcej o cyklu życia, przez który przechodzi definicja planu i szczegóły dotyczące każdego etapu, w tym aktualizowanie i usuwanie przypisań planu.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd5cb7d085744377cf12998f14c994fb1dcd2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74404593"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Opis cyklu życia planu azure

Podobnie jak wiele zasobów w ramach platformy Azure, plan w planach platformy Azure ma typowy i naturalny cykl życia. Są one tworzone, wdrażane i ostatecznie usuwane, gdy nie są już potrzebne lub istotne.
Plany obsługuje standardowe operacje cyklu życia. Następnie opiera się na nich, aby zapewnić dodatkowe poziomy stanu, które obsługują wspólną ciągłą integrację i ciągłe potoki wdrażania dla organizacji, które zarządzają swoją infrastrukturą jako kod — kluczowym elementem w DevOps.

Aby w pełni zrozumieć plan i etapy, omówimy standardowy cykl życia:

> [!div class="checklist"]
> - Tworzenie i edytowanie planu
> - Publikowanie planu
> - Tworzenie i edytowanie nowej wersji planu
> - Publikowanie nowej wersji planu
> - Usuwanie określonej wersji planu
> - Usuwanie planu

## <a name="creating-and-editing-a-blueprint"></a>Tworzenie i edytowanie planu

Podczas tworzenia planu, dodać artefakty do niego, zapisać do grupy zarządzania lub subskrypcji i pod warunkiem unikatową nazwę i unikatową wersję. Plan jest teraz w trybie **roboczym** i nie można go jeszcze przypisać. W trybie **roboczym** można go nadal aktualizować i zmieniać.

Nigdy nie opublikowany plan w trybie **roboczym** wyświetla inną ikonę na stronie **Definicje planu** niż te, które zostały **opublikowane.** **Najnowsza wersja** jest wyświetlana jako **wersja robocza** dla tych nigdy nie opublikowanych planów.

Tworzenie i edytowanie planu za pomocą [interfejsu API portalu Azure](../create-blueprint-portal.md#create-a-blueprint) lub [REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikowanie planu

Po wprowadzeniu wszystkich planowanych zmian w planie w trybie **roboczym** można go **opublikować** i udostępnić do przypisania. **Opublikowanej** wersji planu nie można zmienić. Po **opublikowaniu**plan jest wyświetlany z inną ikoną niż **Plany robocze** i wyświetla podany numer wersji w kolumnie **Najnowsza wersja.**

Opublikuj plan za pomocą [witryny Azure portal](../create-blueprint-portal.md#publish-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Tworzenie i edytowanie nowej wersji planu

**Opublikowanej** wersji planu nie można zmienić. Jednak nowa wersja planu można dodać do istniejącego planu i w razie potrzeby zmodyfikować. Wprowadzać zmiany w istniejącym planie, edytując go. Po zapisaniu nowych zmian plan ma teraz **nieopublikowane zmiany.** Te zmiany są nową wersją **planu wersji roboczej.**

Edytuj plan za pomocą [portalu Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikowanie nowej wersji planu

Każda edytowana wersja planu musi zostać **opublikowana,** zanim będzie można ją przypisać. Po **nieopublikowanych zmian** zostały wprowadzone do planu, ale nie **opublikowane,** przycisk **Opublikuj plan** jest dostępny na stronie planu edycji. Jeśli przycisk nie jest widoczny, plan został już **opublikowany** i nie ma **nieopublikowanych zmian**.

> [!NOTE]
> Pojedynczy plan może mieć wiele **opublikowanych** wersji, które można przypisać do subskrypcji.

Aby opublikować plan z **nieopublikowanymi zmianami,** użyj tych samych kroków, aby opublikować nowy plan.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Usuwanie określonej wersji planu

Każda wersja planu jest unikalnym obiektem i może być indywidualnie **opublikowana.** W związku z tym każda wersja planu można również usunąć. Usunięcie wersji planu nie ma żadnego wpływu na inne wersje tego planu.

> [!NOTE]
> Nie można usunąć planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **definicje planu** ze strony po lewej stronie i użyj opcji filtru, aby zlokalizować plan, którego chcesz usunąć wersję. Wybierz go, aby otworzyć stronę edycji.

1. Wybierz kartę **Opublikowane wersje** i znajdź wersję, którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wersję, aby usunąć i wybierz pozycję **Usuń tę wersję**.

## <a name="deleting-the-blueprint"></a>Usuwanie planu

Podstawowy plan można również usunąć. Usunięcie planu podstawowego powoduje również usunięcie wszystkich wersji planu tego planu, w tym planów **wersji roboczej** i **opublikowanych.** Podobnie jak w przypadku usuwania wersji planu, usunięcie planu podstawowego nie powoduje usunięcia istniejących przydziałów żadnej z wersji planu.

> [!NOTE]
> Nie można usunąć planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

Usuń plan za pomocą [witryny Azure portal](../create-blueprint-portal.md#delete-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Przypisania

Istnieje kilka punktów w cyklu życia planu można przypisać do subskrypcji. Gdy tryb wersji planu jest **opublikowany**, a następnie tej wersji można przypisać do subskrypcji. Ten cykl życia umożliwia wersje planu do użycia i aktywnie przypisane podczas opracowywania nowszej wersji.

Ponieważ wersje planów są przypisane, ważne jest, aby zrozumieć, gdzie są one przypisane i jakie parametry zostały przypisane do. Parametry mogą być statyczne lub dynamiczne. Aby dowiedzieć się więcej, zobacz [parametry statyczne i dynamiczne](parameters.md).

### <a name="updating-assignments"></a>Aktualizowanie przydziałów

Po przypisaniu planu przypisanie może zostać zaktualizowane. Istnieje kilka przyczyn aktualizacji istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowania zasobów](resource-locking.md)
- Zmienianie wartości [parametrów dynamicznych](parameters.md#dynamic-parameters)
- Uaktualnienie przypisania do nowszej **opublikowanej** wersji planu

Aby dowiedzieć się, jak to zrobić, zobacz [aktualizowanie istniejących przydziałów](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Niepodpisywanie przypisań

Jeśli plan nie jest już potrzebny, można go nie przypisać z grupy zarządzania lub subskrypcji. Podczas zmiany planu występuje:

- Usuwanie [blokowania zasobów planu](resource-locking.md)
- Usuwanie obiektu przypisania planu
- (Warunkowe) Jeśli **użyto tożsamości zarządzanej przypisanej do systemu,** jest ona również usuwana

> [!NOTE]
> Wszystkie zasoby wdrożone przez przypisanie planu pozostają na swoim miejscu, ale nie są już chronione przez plany platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).