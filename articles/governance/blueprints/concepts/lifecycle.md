---
title: Zrozumienie cyklu życia planu platformy Azure
description: Informacje o cyklu życia, które przechodzi planu i szczegółowe informacje o każdym etapie.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991543"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Zrozumienie cyklu życia planu platformy Azure

Podobnie jak wiele zasobów na platformie Azure planu w plany usługi Azure ma typowe i fizyczne cyklu życia. Ich tworzenia, wdrożona i na końcu usuwane po nie są już potrzebne lub istotne.
Plany obsługuje tradycyjne operacje cyklu życia operacji CRUD (Tworzenie/odczyt/aktualizowanie/usuwanie), ale sprzyja wytwarzaniu się na nich, aby zapewnić dodatkowe poziomy stanu, które obsługują typowe ciągłej integracji / ciągłego wdrażania (CI/CD) potoków do użytku przez osoby Zarządzanie swoją infrastrukturę za pomocą kodu — kluczowy element w infrastrukturze DevOps określane jako infrastruktura jako kod (IaC).

Aby w pełni zrozumieć planu i etapów, omówimy standardowego cyklu:

> [!div class="checklist"]
> - Tworzenie i edytowanie planu
> - Publikowanie planu
> - Tworzenie i edytowanie nową wersję planu
> - Publikowanie nową wersję planu
> - Usuwanie określoną wersję planu
> - Trwa usuwanie planu

## <a name="creating-and-editing-a-blueprint"></a>Tworzenie i edytowanie planu

Podczas tworzenia planu, Dodaj artefakty do niego, Zapisz z grupą zarządzania i podać unikatową nazwę i wersję unikatowy. W tym momencie planu znajduje się w **projekt** tryb i jeszcze nie można przypisać. Jednakże podczas w **projekt** mogą nadal być aktualizowane i zmienić tryb.

Plan w **projekt** tryb, który nigdy nie został opublikowany wyświetli inną ikonę w **definicje planów** strony niż te, które zostały **opublikowano**. **Najnowszej wersji** , będą również wyświetlane jako **projekt** podczas nigdy nie są publikowane schematy.

Tworzenie i edytowanie planu z [witryny Azure portal](../create-blueprint-portal.md#create-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikowanie planu

Gdy wszystkie wymagane zmiany zostały wprowadzone do planu w **projekt** tryb, może być **opublikowano** i dostępne do przypisania. **Opublikowano** wersję planu nie może być zmieniona.
Raz **opublikowano**, planu wyświetlane z ikoną różnych niż **projekt** plany i wyświetla numer wersji podana w **najnowszej wersji** kolumny.

Publikuj plan z [witryny Azure portal](../create-blueprint-portal.md#publish-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Tworzenie i edytowanie nową wersję planu

Mimo że **opublikowano** wersję planu nie może zostać zmieniona, nowa wersja planu można dodać do istniejącego planu i modyfikować zgodnie z potrzebami. Jest to zrobić, wprowadzając zmiany do istniejącego planu. Jeśli już planu **opublikowano**, gdy te zmiany są zapisywane, są wyświetlane jako **nieopublikowane zmiany** na liście definicji planu. Zapisywanie zapisuje zmiany **projekt** wersję planu.

Edytuj plan z [witryny Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikowanie nową wersję planu

Tak samo, jak został pierwszą wersję planu **opublikowano** go przypisać każdej kolejnej wersji tego samego planu musi być **opublikowano** przed może ona zostać przypisana. Gdy **nieopublikowane zmiany** zostały wprowadzone do planu, ale jeszcze nie zostały jeszcze **opublikowano**, **publikowania planu** przycisk jest dostępny na stronie edycji planu. Jeśli przycisk jest niewidoczny, planu został już **opublikowano**, ale nie ma **nieopublikowane zmiany**.

> [!NOTE]
> Pojedynczego planu może mieć wiele **opublikowano** wersje, które każdy może przypisać do subskrypcji.

Kroki publikowania planu z **nieopublikowane zmiany** jako nową wersję istniejącego planu są takie same jak kroki publikowania nowego planu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Usuwanie określoną wersję planu

Każda wersja planu jest obiektem unikatowy i może być indywidualnie **opublikowano**. Oznacza to również, czy można usunąć każdą wersję planu. Usuwanie wersję planu nie ma żadnego wpływu na inne wersje tego planu.

> [!NOTE]
> Nie jest możliwe usuwanie planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

1. Uruchom usługę Azure schematy w witrynie Azure portal, klikając **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na **zasad** kliknij na **plany**.

1. Wybierz **definicje planów** ze strony po lewej i użyj opcji filtru, można zlokalizować planu, aby usunąć wersję. Kliknij go, aby otworzyć stronę edycji.

1. Kliknij przycisk **opublikowane wersje** kartę i Znajdź wersję, którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy w wersji do usunięcia i wybierz **Usuń tę wersję**.

## <a name="deleting-the-blueprint"></a>Trwa usuwanie planu

Plan core mogą być również usuwane. Usuwanie planu core spowoduje również usunięcie planu wszystkie wersje tego planu, niezależnie od tego **projekt** lub **opublikowano** stanu. Jako usuwanie wersję planu, usuwanie planu podstawowego nie powoduje usunięcia istniejące przypisania z dowolnej wersji planu.

> [!NOTE]
> Nie jest możliwe usuwanie planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie usuń wersję, którą chcesz usunąć.

Usuń plan za pomocą [witryny Azure portal](../create-blueprint-portal.md#delete-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Przypisania

Istnieje kilka punktów podczas cyklu życia, planu można przypisać do subskrypcji.
Zawsze, gdy tryb wersji planu jest **opublikowano**, a następnie w tej wersji można przypisać do subskrypcji. Nawet w przypadku **projekt** wersję planu, jeśli co najmniej wersji planu w **opublikowano** trybu, a następnie je **opublikowano** wersjami polega na dostępne do przypisania. Dzięki temu wersji planu używanych i aktywnie przypisany podczas jest obecnie sporządzana nowszej wersji.

Ponieważ wersje schematy są przypisywane, jest ważne dowiedzieć się, gdy są przydzielone i parametrami, które zostały przypisane przy użyciu. Parametry mogą być statyczne lub dynamiczne. Aby dowiedzieć się więcej, zobacz [statycznych i dynamicznych parametrów](parameters.md).

### <a name="updating-assignments"></a>Aktualizowanie przypisań

Po przypisaniu planu można zaktualizować przypisania. Istnieje kilka powodów, dla aktualizowanie istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowanie zasobów](resource-locking.md)
- Zmień wartość właściwości [parametrów dynamicznych](parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowano** wersję planu

Aby dowiedzieć się więcej, zobacz temat [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Kolejne kroki

- Opis sposobu użycia [statycznych i dynamicznych parametrów](parameters.md)
- Dowiedz się, jak dostosować [planu sekwencjonowania](sequencing-order.md)
- Dowiedz się, jak używać [planu blokowanie zasobów](resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania planu z [Ogólne rozwiązywanie problemów](../troubleshoot/general.md)