---
title: Multiklasa jeden a wszystko
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu one vs-all multiclass w usłudze Azure Machine Learning, aby utworzyć model klasyfikacji wieloklasowej z zestawu modeli klasyfikacji binarnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456084"
---
# <a name="one-vs-all-multiclass"></a>Multiklasa jeden a wszystko

W tym artykule opisano sposób korzystania z modułu one vs-all multiclass w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Celem jest utworzenie modelu klasyfikacji, który można przewidzieć wiele klas, przy użyciu podejścia *jeden na wszystkie.*

Ten moduł jest przydatny do tworzenia modeli, które przewidują trzy lub więcej możliwych wyników, gdy wynik zależy od ciągłych lub kategorycznych zmiennych predykcyjnych. Ta metoda umożliwia również użycie metod klasyfikacji binarnej dla problemów, które wymagają wielu klas wyjściowych.

### <a name="more-about-one-versus-all-models"></a>Więcej o modelach typu "jeden na wszystkie"

Niektóre algorytmy klasyfikacji zezwalają na użycie więcej niż dwóch klas według projektu. Inne ograniczyć możliwe wyniki do jednej z dwóch wartości (binarny lub model dwuklasowy). Ale nawet algorytmy klasyfikacji binarnej mogą być dostosowane do zadań klasyfikacji wieloklasowej za pomocą różnych strategii. 

Ten moduł implementuje metodę jeden na wszystkie, w której model binarny jest tworzony dla każdej z wielu klas wyjściowych. Moduł ocenia każdy z tych modeli binarnych dla poszczególnych klas względem jego dopełnienia (wszystkie inne klasy w modelu), tak jakby jest to problem klasyfikacji binarnej. Moduł następnie wykonuje przewidywanie, uruchamiając te klasyfikatory binarne i wybierając przewidywanie z najwyższym wynikiem zaufania.  

W istocie moduł tworzy zespół poszczególnych modeli, a następnie scala wyniki, aby utworzyć pojedynczy model, który przewiduje wszystkie klasy. Każdy klasyfikator binarny może służyć jako podstawa dla modelu jeden na wszystkie.  

Załóżmy na przykład, że konfigurujesz model [maszyny wektorowej obsługi dwóch klas](two-class-support-vector-machine.md) i udostępniasz go jako dane wejściowe do modułu One-vs-All Multiclass. Moduł utworzy dwuklasowe modele maszyn wektorowych dla wszystkich elementów członkowskich klasy wyjściowej. Następnie należy zastosować metodę jeden na wszystkie, aby połączyć wyniki dla wszystkich klas.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Jak skonfigurować klasyfikator multiklasy jeden vs wszystkie  

Ten moduł tworzy zespół modeli klasyfikacji binarnej do analizowania wielu klas. Aby użyć tego modułu, należy najpierw skonfigurować i wyszkolić model *klasyfikacji binarnej.* 

Model binarny można podłączyć do modułu one-vs-all multiclass. Następnie trenujesz zespół modeli przy użyciu [modelu pociągu](train-model.md) z oznaczonym zestawem danych szkolenia.

Po połączeniu modeli One-vs-All Multiclass tworzy wiele modeli klasyfikacji binarnej, optymalizuje algorytm dla każdej klasy, a następnie scala modele. Moduł wykonuje te zadania, nawet jeśli zestaw danych szkolenia może mieć wiele wartości klasy.

1. Dodaj moduł one-vs-all multiclass do potoku w projektancie. Ten moduł można znaleźć w obszarze **Uczenie maszynowe — inicjowanie**, w kategorii **Klasyfikacja.**

   Klasyfikator multiklasy jeden vs wszystko nie ma własnych konfigurowalnych parametrów. Wszelkie dostosowania muszą być wykonywane w modelu klasyfikacji binarnej, który jest dostarczany jako dane wejściowe.

2. Dodaj model klasyfikacji binarnej do potoku i skonfiguruj ten model. Na przykład można użyć [dwuklasowej obsługi technicznej maszyny wektorowej](two-class-support-vector-machine.md) lub [dwuklasowego drzewa decyzyjnego wzmocnionego.](two-class-boosted-decision-tree.md)

3. Dodaj [moduł Modelu pociągu](train-model.md) do potoku. Połącz niewprawnego klasyfikatora, który jest dane wyjściowe One-vs-All Multiclass.

4. Na innych danych wejściowych [modelu pociągu](train-model.md), połączyć zestaw danych szkolenia oznaczone, który ma wiele wartości klasy.

5. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia można użyć modelu do przewidywania wielu klas.

Alternatywnie można przekazać nieprzeszkolonego klasyfikatora do [modelu sprawdzania poprawności krzyżowej](cross-validate-model.md) do sprawdzania poprawności krzyżowej względem zestawu danych sprawdzania poprawności etykietą.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
