---
title: 'Dwuklasowa maszyna wektorowa obsługi: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu **Wektorowa obsługa dwóch klas** w usłudze Azure Machine Learning, aby utworzyć model oparty na algorytmie maszyny wektorowej obsługi technicznej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455812"
---
# <a name="two-class-support-vector-machine-module"></a>Dwuklasowy moduł wektorowej obsługi technicznej

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu, który jest oparty na algorytmie maszyny wektorowej obsługi. 

Maszyny wektorowe wsparcia (SVMs) są dobrze zbadane klasy nadzorowanych metod uczenia się. Ta konkretna implementacja jest odpowiednia do przewidywania dwóch możliwych wyników, opartych na zmiennych ciągłych lub kategorycznych.

Po zdefiniowaniu parametrów modelu, trenuj model przy użyciu modułów szkoleniowych i podając *oznakowany zestaw danych,* który zawiera etykietę lub kolumnę wyników.

## <a name="about-support-vector-machines"></a>Informacje o maszynach wektorowych wsparcia

Maszyny wektorowe obsługi są jednymi z pierwszych algorytmów uczenia maszynowego i modele SVM zostały wykorzystane w wielu aplikacjach, od pobierania informacji do klasyfikacji tekstu i obrazu. SVMs mogą być używane zarówno do klasyfikacji i regresji zadań.

Ten model SVM jest nadzorowanym modelem uczenia się, który wymaga danych oznaczonych etykietą. W procesie szkolenia algorytm analizuje dane wejściowe i rozpoznaje wzorce w wielowymiarowej przestrzeni obiektowej zwanej *hiperplane.*  Wszystkie przykłady danych wejściowych są reprezentowane jako punkty w tym obszarze i są mapowane na kategorie wyjściowe w taki sposób, że kategorie są dzielone przez jak najszerszą i wyraźną lukę, jak to możliwe.

Do prognozowania algorytm SVM przypisuje nowe przykłady do jednej kategorii lub drugiej, mapując je w tej samej przestrzeni. 

## <a name="how-to-configure"></a>Jak skonfigurować 

Dla tego typu modelu zaleca się normalizacji zestawu danych przed użyciem go do szkolenia klasyfikatora.
  
1.  Dodaj moduł **Maszyny Wektorowej obsługi technicznej** dwuklasowej do potoku.  
  
2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  

    -   **Zakres parametrów:** Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry za pomocą modułu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Należy podać zakres wartości, a trener iteruje przez wiele kombinacji ustawień, aby określić kombinację wartości, która daje najlepszy wynik.

3.  W obszarze **Liczba iteracji**wpisz liczbę, która oznacza liczbę iteracji używanych podczas tworzenia modelu.  
  
     Ten parametr może służyć do kontrolowania kompromisu między szybkością treningu a dokładnością.  
  
4.  W polu **Lambda**wpisz wartość, która ma być używana jako waga do legalizacji L1.  
  
     Ten współczynnik regularyzacji może służyć do strojenia modelu. Większe wartości karze bardziej złożone modele.  
  
5.  Wybierz opcję **Normalizuj operacje**, jeśli chcesz znormalizować operacje przed treningiem.
  
     Jeśli zastosujesz normalizację, przed szkoleniem punkty danych są wyśrodkowane na średniej i skalowane, aby mieć jedną jednostkę odchylenia standardowego.
  
6.  Wybierz opcję **Project to the unit sphere**, aby znormalizować współczynniki.
  
     Rzutowanie wartości do miejsca jednostkowego oznacza, że przed szkoleniem punkty danych są wyśrodkowane na 0 i skalowane, aby mieć jedną jednostkę odchylenia standardowego.
  
7.  W **polu Ilość losowa liczba siewna**wpisz wartość całkowitą, która ma być używana jako materiał siewny, jeśli chcesz zapewnić odtwarzalność w przebiegach.  W przeciwnym razie wartość zegara systemowego jest używana jako materiał siewny, co może spowodować nieco inne wyniki w różnych przebiegach.
  
9. Podłącz oznaczony zestaw danych i jeden z [modułów szkoleniowych:](module-reference.md)
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](train-model.md)
  
10. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

+ Aby użyć modelu do oceniania, dodaj **wynik modelu** modułu do potoku.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 