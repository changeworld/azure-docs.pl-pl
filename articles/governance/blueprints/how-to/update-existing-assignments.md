---
title: Jak zaktualizować istniejącego przypisania planu platformy Azure
description: Więcej informacji na temat aktualizowania istniejącego przypisania w plany usługi Azure przy użyciu mechanizmu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956204"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak zaktualizować istniejące przypisanie planu

Po przypisaniu planu można zaktualizować przypisania. Istnieje kilka powodów, dla aktualizowanie istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowanie zasobów](../concepts/resource-locking.md)
- Zmień wartość właściwości [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowano** wersję planu

## <a name="updating-assignments"></a>Aktualizowanie przypisań

1. Uruchom usługę Azure schematy w witrynie Azure portal, klikając **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na **zasad** kliknij na **plany**.

1. Wybierz **przypisane plany** ze strony po lewej stronie.

1. Na liście Schematy, kliknij lewym przyciskiem myszy przypisanie planu, a następnie kliknij przycisk **przypisanie aktualizacji** lub kliknij prawym przyciskiem myszy przypisanie planu i wybrać **przypisanie aktualizacji**.

   ![Przypisanie aktualizacji](../media/update-existing-assignments/update-assignment.png)

1. **Przypisać planu** strony załaduje wstępnie wypełnione przy użyciu wszystkich wartości z oryginalnego przypisania. Możesz zmienić **wersji definicji planu**, **przypisania blokady** stanu i dowolny z parametrów dynamicznych, które istnieją w definicji planu. Kliknij przycisk **przypisać** po zakończeniu wprowadzania zmian.

1. Na stronie szczegółów przypisania zaktualizowane Zobacz nowy stan. W tym przykładzie dodaliśmy **blokowanie** do przypisania.

   ![Zaktualizowano przypisanie — zablokowane](../media/update-existing-assignments/updated-assignment.png)

1. Poznaj szczegółowe informacje o innych **operacji przypisania** za pomocą listy rozwijanej. Tabela **zarządzanych zasobów** aktualizacji przez operację wybrane przypisania.

   ![Operacji przypisania](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Aktualizowanie przypisania zasad

Wdrożenie zaktualizowanego przypisania regułom kilka ważnych. Reguły te określają, co się dzieje z istniejącego zasobu w zależności od tego, czy żądana zmiana i rodzaj artefaktu zasobów jest wdrożony lub aktualizowane.

- Przypisania ról
  - Jeśli zmiany roli lub osoby przypisanej roli, (użytkownika, grupy lub aplikacji), jest tworzone nowe przypisanie roli. Przypisanie roli wdrożonej wcześniej pozostanie w miejscu.
- Przypisania zasad
  - Zmiana parametrów przypisania zasad jest aktualizowany istniejącego przypisania.
  - Zmiana definicji przypisania zasad jest tworzony nowe przypisanie zasad. Przypisanie zasad wdrożonej wcześniej pozostanie w miejscu.
  - Jeśli artefaktu przypisania zasad jest usuwany z planu, przypisanie zasad wdrożonej wcześniej pozostanie w miejscu.
- Szablony usługi Azure Resource Manager
  - Szablon jest przetwarzany za pomocą usługi Resource Manager jako **umieścić**. Zgodnie z każdego typu zasobu obsługuje to inaczej, można znaleźć w dokumentacji dla każdego zasobu dołączone do określenia wpływu tej akcji uruchamiania przez schematy.

## <a name="possible-errors-on-updating-assignments"></a>Ewentualne błędy na aktualizowanie przypisań

Podczas aktualizowania przypisań, istnieje możliwość zmiany naruszające podczas wykonywania. Na przykład zmienia lokalizację grupy zasobów po została już wdrożona. Wszelkie zmiany, które są obsługiwane przez [usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) może zostać wykonane, ale dowolny to zmienić, będą zwracały błędy za pomocą usługi Azure Resource Manager spowoduje niepowodzenie przypisania.

Nie ma żadnego limitu, na ile razy moduł przypisania, które mogą być aktualizowane. W związku z tym jeśli wystąpi błąd, albo z powodu zły parametr, istniejącego obiektu lub zmiana zabroniona przez usługę Azure Resource Manager określania błędu i wprowadzić inną aktualizację przypisania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [planu cyklu życia](../concepts/lifecycle.md)
- Opis sposobu użycia [statycznych i dynamicznych parametrów](../concepts/parameters.md)
- Dowiedz się, jak dostosować [planu sekwencjonowania](../concepts/sequencing-order.md)
- Dowiedz się, jak używać [planu blokowanie zasobów](../concepts/resource-locking.md)
- Rozwiązywanie problemów podczas przypisywania planu z [Ogólne rozwiązywanie problemów](../troubleshoot/general.md)
