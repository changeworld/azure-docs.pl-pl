---
title: Polityka edukacyjna - Personalizer
description: Ustawienia uczenia się określają *hiperparametry* szkolenia modelu. Dwa modele tych samych danych, które są przeszkolone w różnych ustawieniach uczenia się skończy się inaczej.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219347"
---
# <a name="learning-policy-and-settings"></a>Zasady i ustawienia uczenia się

Ustawienia uczenia się określają *hiperparametry* szkolenia modelu. Dwa modele tych samych danych, które są przeszkolone w różnych ustawieniach uczenia się skończy się inaczej.

[Zasady i ustawienia uczenia się](how-to-settings.md#configure-rewards-for-the-feedback-loop) są ustawiane w zasobie Personalizer w witrynie Azure portal.

## <a name="import-and-export-learning-policies"></a>Zasady nauki importu i eksportu

Można importować i eksportować pliki zasad uczenia się z witryny Azure portal. Ta metoda służy do zapisywania istniejących zasad, testowania ich, zastępowania i archiwizowania ich w formancie kodu źródłowego jako artefaktów do przyszłych odwołań i inspekcji.

Dowiedz [się, jak importować](how-to-manage-model.md#import-a-new-learning-policy) i eksportować zasady uczenia się w witrynie Azure portal dla zasobu Personalizer.

## <a name="understand-learning-policy-settings"></a>Opis ustawień zasad uczenia się

Ustawienia w zasadach uczenia się nie są przeznaczone do zmiany. Zmień ustawienia tylko wtedy, gdy rozumiesz, jak wpływają one na Personalizer. Bez tej wiedzy można spowodować problemy, w tym unieważnienie modeli Personalizer.

Personalizer używa [vowpalwabbit](https://github.com/VowpalWabbit) do szkolenia i ocena wydarzeń. Zapoznaj się z [dokumentacją vowpalwabbit,](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) jak edytować ustawienia uczenia się za pomocą vowpalwabbit. Po uzyskaniu poprawnych argumentów wiersza polecenia zapisz polecenie w pliku o następującym formacie (zastąp wartość właściwości arguments żądanym poleceniem) i przekaż plik do importu ustawień uczenia się w okienku **Ustawienia modelu i uczenia się** w witrynie Azure portal dla zasobu Personalizer.

Poniżej `.json` przedstawiono przykład zasad uczenia się.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Porównaj zasady uczenia się

Można porównać, jak różne zasady uczenia się działają w stosunku do przeszłych danych w dziennikach Personalizer, wykonując [oceny offline.](concepts-offline-evaluation.md)

[Prześlij własne zasady uczenia się,](how-to-manage-model.md) aby porównać je z bieżącymi zasadami uczenia się.

## <a name="optimize-learning-policies"></a>Optymalizacja zasad uczenia się

Personalizator może utworzyć zoptymalizowaną politykę uczenia się w [ocenie offline.](how-to-offline-evaluation.md) Zoptymalizowana polityka uczenia się, która ma lepsze nagrody w ocenie offline przyniesie lepsze wyniki, gdy jest używana online w Personalizer.

Po zoptymalizowaniu zasad uczenia się, można zastosować go bezpośrednio do Personalizer, więc natychmiast zastępuje bieżące zasady. Możesz też zapisać zoptymalizowane zasady do dalszej oceny, a później zdecydować, czy ją odrzucić, zapisać lub zastosować.

## <a name="next-steps"></a>Następne kroki

* Naucz się [aktywnych i nieaktywnych zdarzeń](concept-active-inactive-events.md).
