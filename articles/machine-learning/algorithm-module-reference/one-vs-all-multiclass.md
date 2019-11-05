---
title: moduł wieloklasowy "jeden przeciw wszystkim"
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu moduł wieloklasowy "jeden przeciw wszystkim" w usłudze Azure Machine Learning do tworzenia wieloklasowego modelu klasyfikacji na podstawie zestawy danych binarnych modeli klasyfikacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518166"
---
# <a name="one-vs-all-multiclass"></a>moduł wieloklasowy "jeden przeciw wszystkim"

W tym artykule opisano, jak używać modułu **moduł wieloklasowy "jeden przeciw wszystkim"** platformy Azure Machine Learning Designer (wersja zapoznawcza) w celu utworzenia modelu klasyfikacji, który może przewidzieć wiele klas przy użyciu podejścia "one a. All".

Ten moduł jest przydatny do tworzenia modeli predykcyjnych trzy lub więcej wyników, gdy wynik zależy od zmiennych predykcyjnych ciągłych lub kategorii. Ta metoda umożliwia także używanie binarnych metod klasyfikacji dla problemów wymagających wielu klas wyjściowych.

### <a name="more-about-one-vs-all-models"></a>Więcej informacji o modelach jeden-a-wszystkie

Chociaż niektóre algorytmy klasyfikacji umożliwiają użycie więcej niż dwóch klas przez zaprojektowanie, inne ograniczają możliwe wyniki do jednej z dwóch wartości (binarny lub dwuklasowy model). Jednak nawet binarne algorytmy klasyfikacji można dostosować do wieloklasowych zadań klasyfikacji przy użyciu różnych strategii. 

Ten moduł implementuje *metodę "a. All*", w której jest tworzony model binarny dla każdej klasy danych wyjściowych. Każdy z tych modeli binarnych dla poszczególnych klas jest oceniany względem jego uzupełnienia (wszystkie inne klasy w modelu), tak jakby był to binarny problem klasyfikacji. Funkcja przewidywania jest następnie wykonywana przez uruchomienie tych klasyfikatorów binarnych i wybranie przewidywania z najwyższym wynikiem ufności.  

W zasadzie tworzony jest kompletność poszczególnych modeli, a następnie wyniki są scalane, aby utworzyć pojedynczy model, który przewiduje wszystkie klasy. W związku z tym każdy klasyfikator binarny może być używany jako podstawa dla modelu typu jeden-vs-ALL.  

 Załóżmy na przykład, że skonfigurowano model [maszyny wektorowej obsługujący dwie klasy](two-class-support-vector-machine.md) i zapewnia to, że jako dane wejściowe do modułu **moduł wieloklasowy "jeden przeciw wszystkim"** . Moduł utworzy modele maszyn wektorowych z obsługą dwóch klas dla wszystkich elementów członkowskich klasy wyjściowej, a następnie zastosuje metodę "one-a-All", aby połączyć wyniki dla wszystkich klas.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Jak skonfigurować klasyfikator jeden-i-All  

Ten moduł tworzy kompletną liczbę binarnych modeli klasyfikacji do analizowania wielu klas. W związku z tym, aby użyć tego modułu, należy najpierw skonfigurować i szkolić **binarny model klasyfikacji** . 

Następnie można połączyć model binarny z modułem **moduł wieloklasowy "jeden przeciw wszystkim"** i przeszkolić kompletność modeli przy użyciu [modelu uczenia](train-model.md) z zestawem danych szkolenia z etykietą.

Podczas łączenia modeli, chociaż zestaw danych szkoleniowych może mieć wiele wartości klas, **moduł wieloklasowy "jeden przeciw wszystkim"** tworzy wiele binarnych modeli klasyfikacji, optymalizuje algorytm dla każdej klasy, a następnie scala modele.

1. Dodaj **moduł wieloklasowy "jeden przeciw wszystkim"** do potoku w projektancie. Ten moduł można znaleźć w obszarze Machine Learning-Initialize w kategorii **Klasyfikacja** .

    Klasyfikator **moduł wieloklasowy "jeden przeciw wszystkim"** nie ma samodzielnie skonfigurowanych parametrów. Wszelkie dostosowania należy wykonać w modelu klasyfikacji binarnej, który jest dostarczany jako dane wejściowe.

2. Dodaj binarny model klasyfikacji do potoku i skonfiguruj ten model. Można na przykład użyć [dwuklasowej maszyny wektorowej](two-class-support-vector-machine.md) lub [dwuklasowego drzewa decyzyjnego](two-class-boosted-decision-tree.md).

3. Dodaj moduł [uczenie modelu](train-model.md) do potoku i Połącz niepociąg klasyfikatora, który jest danymi wyjściowymi **moduł wieloklasowy "jeden przeciw wszystkim"** .

4. Na innym wejściu [modelu uczenia](train-model.md)Połącz zestaw danych szkolenia z etykietą, który ma wiele wartości klas.

5. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia możesz użyć modelu, aby utworzyć prognozy wieloklasowe.

Alternatywnie, można przekazać [niesprawdzony klasyfikator do modelu krzyżowego](cross-validate-model.md) sprawdzania poprawności, aby przeprowadzić walidację krzyżową względem zestawu danych walidacji z etykietami.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
