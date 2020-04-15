---
title: Aktualizowanie istniejącego przydziału z portalu
description: Dowiedz się więcej o mechanizmie aktualizowania istniejącego przypisania planu z portalu w planach platformy Azure.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381789"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak zaktualizować istniejące przypisanie planu

Po przypisaniu planu przypisanie może zostać zaktualizowane. Istnieje kilka przyczyn aktualizacji istniejącego przypisania, w tym:

- Dodawanie lub usuwanie [blokowania zasobów](../concepts/resource-locking.md)
- Zmienianie wartości [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters)
- Uaktualnienie przypisania do nowszej **opublikowanej** wersji planu

## <a name="updating-assignments"></a>Aktualizowanie przydziałów

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** ze strony po lewej stronie.

1. Na liście planów kliknij lewym przyciskiem myszy przypisanie planu. Następnie kliknij przycisk **Aktualizuj przypisanie** lub kliknij prawym przyciskiem myszy przypisanie planu i wybierz polecenie **Aktualizuj przypisanie**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Aktualizowanie istniejącego przydziału planu" border="false":::

1. Strona **Przypisz plan** zostanie załadowana wstępnie wypełniona wszystkimi wartościami z oryginalnego przypisania.
   Można zmienić **wersję definicji planu,** stan **Lock Assignment** i dowolny z parametrów dynamicznych, które istnieją w definicji planu. Kliknij **przycisk Przypisz** po wprowadzeniu zmian.

1. Na stronie zaktualizowane szczegóły przydziału zobacz nowy stan. W tym przykładzie dodaliśmy **blokowanie** do przypisania.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Zaktualizowano istniejące przypisanie planu — zmieniono tryb blokady" border="false":::

1. Zapoznaj się ze szczegółami dotyczącymi innych **operacji przypisywania** przy użyciu listy rozwijanej. Tabela **zarządzanych zasobów** jest aktualizowana przez wybraną operację przydziału.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Operacje przypisania przydziału planu" border="false":::

## <a name="rules-for-updating-assignments"></a>Reguły aktualizowania przydziałów

Wdrażanie zaktualizowanych przydziałów odbywa się zgodnie z kilkoma ważnymi regułami. Te reguły określają, co dzieje się z już wdrożonych zasobów. Żądana zmiana i typ zasób artefaktu wdrażany lub aktualizowany określają, które akcje są podejmowane.

- Przypisania ról
  - Jeśli rola lub przypisany do roli (użytkownik, grupa lub aplikacja) ulegnie zmianie, zostanie utworzone nowe przypisanie roli. Przypisania ról wcześniej wdrożone pozostają na swoim miejscu.
- Przypisania zasad
  - Jeśli parametry przypisania zasad zostaną zmienione, istniejące przypisanie zostanie zaktualizowane.
  - Jeśli definicja przypisania zasad zostanie zmieniona, zostanie utworzone nowe przypisanie zasad.
    Wcześniej wdrożone przypisania zasad pozostają na swoim miejscu.
  - Jeśli artefakt przypisania zasad zostanie usunięty z planu, wdrożone przypisania zasad pozostają na swoim miejscu.
- Szablony usługi Azure Resource Manager
  - Szablon jest przetwarzany za pośrednictwem Menedżera zasobów jako **PUT**. Ponieważ każdy typ zasobu obsługuje tę akcję inaczej, przejrzyj dokumentację dla każdego dołączonego zasobu, aby określić wpływ tej akcji po uruchomieniu przez plany.

## <a name="possible-errors-on-updating-assignments"></a>Możliwe błędy podczas aktualizowania przydziałów

Podczas aktualizowania przydziałów, można wprowadzić zmiany, które są przerywane podczas wykonywania. Przykładem jest zmiana lokalizacji grupy zasobów po jej już wdrożone. Wszelkie zmiany, które są obsługiwane przez [usługę Azure Resource Manager](../../../azure-resource-manager/management/overview.md) mogą być wprowadzone, ale wszelkie zmiany, które mogłyby spowodować błąd za pośrednictwem usługi Azure Resource Manager spowoduje również niepowodzenie przydziału.

Nie ma limitu liczby razy, ile można zaktualizować przypisania. Jeśli wystąpi błąd, należy określić błąd i dokonać kolejnej aktualizacji do przypisania.  Przykładowe scenariusze błędów:

- Zły parametr
- Już istniejący obiekt
- Zmiana nie jest obsługiwana przez usługę Azure Resource Manager

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [cyklu życia planu](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).