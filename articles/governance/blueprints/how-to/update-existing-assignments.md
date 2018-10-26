---
title: Jak zaktualizować istniejącego przypisania planu platformy Azure
description: Więcej informacji na temat aktualizowania istniejącego przypisania w plany usługi Azure przy użyciu mechanizmu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2c9f660e54da50e32ce1d0dc43b0efeacd643c57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093789"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak zaktualizować istniejące przypisanie planu

Po przypisaniu planu można zaktualizować przypisania. Istnieje kilka powodów, dla aktualizowanie istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowanie zasobów](../concepts/resource-locking.md)
- Zmień wartość właściwości [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowano** wersję planu

## <a name="updating-assignments"></a>Aktualizowanie przypisań

1. Kliknij pozycję **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na stronie **Zasady** kliknij pozycję **Strategie**.

1. Wybierz pozycję **Przypisane strategie** w lewej części strony.

1. Na liście Schematy kliknij lewym przyciskiem myszy przypisanie planu. Następnie kliknij przycisk **przypisanie aktualizacji** lub kliknij prawym przyciskiem myszy przypisanie planu i wybrać **przypisanie aktualizacji**.

   ![Przypisanie aktualizacji](../media/update-existing-assignments/update-assignment.png)

1. **Przypisać planu** strony załaduje wstępnie wypełnione przy użyciu wszystkich wartości z oryginalnego przypisania. Możesz zmienić **wersji definicji planu**, **przypisania blokady** stanu i dowolny z parametrów dynamicznych, które istnieją w definicji planu. Kliknij przycisk **przypisać** po zakończeniu wprowadzania zmian.

1. Na stronie szczegółów przypisania zaktualizowane Zobacz nowy stan. W tym przykładzie dodaliśmy **blokowanie** do przypisania.

   ![Zaktualizowano przypisanie — zablokowane](../media/update-existing-assignments/updated-assignment.png)

1. Poznaj szczegółowe informacje o innych **operacji przypisania** za pomocą listy rozwijanej. Tabela **zarządzanych zasobów** aktualizacji przez operację wybrane przypisania.

   ![Operacji przypisania](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Aktualizowanie przypisania zasad

Wdrożenie zaktualizowanego przypisania regułom kilka ważnych. Reguły te określają, co się dzieje z już wdrożonych zasobów. Żądana zmiana i typu zasobu artefaktu jest wdrożony lub zaktualizowano Określ akcje, które są pobierane.

- Przypisania ról
  - Jeśli zmiany roli lub osoby przypisanej roli, (użytkownika, grupy lub aplikacji), jest tworzone nowe przypisanie roli. Przypisania ról, które wcześniej wdrożone są pozostawiane w miejscu.
- Przypisania zasad
  - Zmiana parametrów przypisania zasad jest aktualizowany istniejącego przypisania.
  - Zmiana definicji przypisania zasad jest tworzony nowe przypisanie zasad. Przypisania zasad, które wcześniej wdrożone są pozostawiane w miejscu.
  - Jeśli artefaktu przypisania zasad jest usuwany z planu, należy wdrożyć zasady, które przydziały są pozostawiane w miejscu.
- Szablony usługi Azure Resource Manager
  - Szablon jest przetwarzany za pomocą usługi Resource Manager jako **umieścić**. Zgodnie z każdego typu zasobu obsługuje tę akcję w inny sposób, można znaleźć w dokumentacji dla każdego zasobu dołączone do określenia wpływu tej akcji uruchamiania przez schematy.

## <a name="possible-errors-on-updating-assignments"></a>Ewentualne błędy na aktualizowanie przypisań

Podczas aktualizowania przypisań, istnieje możliwość zmiany naruszające podczas wykonywania. Przykład zmienia lokalizację grupy zasobów po została już wdrożona. Wszelkie zmiany, które są obsługiwane przez [usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) może zostać wykonane, ale dowolny to zmienić, będą zwracały błędy za pomocą usługi Azure Resource Manager spowoduje niepowodzenie przypisania.

Nie ma żadnego limitu, na ile razy moduł przypisania, które mogą być aktualizowane. Jeśli wystąpi błąd, należy określić ten błąd i wprowadzać inną aktualizację przypisania.  Przykładowe scenariusze błąd:

- Nieprawidłowy parametr
- Już istniejący obiekt
- Zmiany nie obsługiwane przez usługę Azure Resource Manager

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](../concepts/lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md)
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md)
