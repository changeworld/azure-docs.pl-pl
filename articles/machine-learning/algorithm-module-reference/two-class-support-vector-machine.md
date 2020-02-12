---
title: 'Maszyna wektorowa obsługująca dwie klasy: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać **dwuklasowego modułu maszyny wektorowego** w Azure Machine Learning, aby utworzyć model oparty na algorytmie maszyny wektorowej obsługi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 072809949badb08e5b1e7078566e289c5a5fecd9
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153676"
---
# <a name="two-class-support-vector-machine-module"></a>Moduł maszyny wektorowej obsługujący dwie klasy

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu opartego na algorytmie maszyny wektorowej obsługi. 

Maszyny wektorowe obsługi (SVMs) to dobrze przeszukiwana Klasa metod uczenia nadzorowanego. Ta konkretna implementacja jest odpowiednia do przewidywania dwóch możliwych wyników na podstawie zmiennych ciągłych lub kategorii.

Po zdefiniowaniu parametrów modelu należy nauczyć model przy użyciu modułów szkoleniowych i dostarczając *znacznikowy zestaw danych* , który zawiera etykietę lub kolumnę wyników.

## <a name="about-support-vector-machines"></a>Informacje o obsłudze maszyn wektorowych

Obsługa maszyn wektorowych jest Najwcześniejsza spośród algorytmów uczenia maszynowego, a modele SVM są używane w wielu aplikacjach, od pobierania informacji do klasyfikacji tekstu i obrazów. SVMs można użyć do zadań klasyfikacji i regresji.

Ten model SVM to nadzorowany model uczenia, który wymaga danych z etykietami. W procesie szkoleń algorytm analizuje dane wejściowe i rozpoznaje wzorce w wielowymiarowym obszarze funkcji o nazwie " *Plan*".  Wszystkie przykłady danych wejściowych są reprezentowane jako punkty w tym miejscu i są mapowane na kategorie wyjściowe w taki sposób, że kategorie są podzielone na szeroką i wyznaczą odstępy.

W przypadku przewidywania algorytm SVM przypisuje nowe przykłady do jednej kategorii lub drugiej, mapując je na takie same miejsce. 

## <a name="how-to-configure"></a>Jak skonfigurować 

W przypadku tego typu modelu zaleca się normalizowanie zestawu danych przed użyciem go do uczenia klasyfikatora.
  
1.  Dodaj moduł **maszyny wektorowej obsługujący dwie klasy** do potoku.  
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  

3.  Dla **liczby iteracji**wpisz liczbę, która wskazuje liczbę iteracji użytych podczas kompilowania modelu.  
  
     Ten parametr może służyć do kontroli handlu między szybkością uczenia i dokładnością.  
  
4.  Dla **wyrażenia lambda**wpisz wartość, która ma być używana jako waga dla uregulowania L1.  
  
     Ten współczynnik uregulowania może służyć do dostrajania modelu. Większe wartości karają bardziej złożone modele.  
  
5.  Wybierz opcję **normalizing Features**, jeśli chcesz znormalizować funkcje przed szkoleniem.
  
     W przypadku zastosowania normalizacji przed szkoleniem punkty danych są wyśrodkowane w średniej i skalowane w celu uzyskania jednej jednostki odchylenia standardowego.
  
6.  Wybierz opcję **projekt do sfery jednostki**, aby znormalizować współczynniki.
  
     Projekcja wartości w przestrzeni jednostek oznacza, że przed szkoleniem punkty danych są wyśrodkowane w 0 i skalowane w celu uzyskania jednej jednostki odchylenia standardowego.
  
7.  W obszarze **inicjator liczby losowej**wpisz wartość całkowitą, która ma być używana jako inicjator, jeśli chcesz zapewnić odtwarzalność między przebiegami.  W przeciwnym razie wartość zegara systemowego jest używana jako inicjator, co może spowodować nieco inne wyniki w przebiegu.
  
9. Połącz zestaw danych z etykietą i jeden z [modułów szkoleniowych](module-reference.md):
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](train-model.md) .
  
10. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę dane **wyjściowe** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.

+ Aby użyć modelu do oceniania, Dodaj moduł **model oceny** do potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 