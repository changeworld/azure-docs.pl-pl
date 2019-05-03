---
title: 'Maszyna wektor Two-Class pomocy technicznej: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać **Two-Class obsługuje Vector Machine** modułu w usłudze Azure Machine Learning, aby utworzyć model oparty na algorytm maszyny wektor pomocy technicznej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027933"
---
# <a name="two-class-support-vector-machine-module"></a>Moduł Two-Class pomocy technicznej wektor maszyny

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu, który jest oparty na algorytm maszyny wektor pomocy technicznej. 

Obsługa wektor maszyny (SVMs) są dobrze zbadane klas, metod uczenia nadzorowanego. Tej określonej implementacji nadaje się do prognozowania dwa możliwe wyniki na podstawie zmiennych ciągłych lub podzielonych na kategorie.

Po zdefiniowaniu parametry modelu, uczenie modelu przy użyciu modułów szkolenia i podając *oznakowane dataset* zawierającej kolumnę etykiety lub wynik.

## <a name="about-support-vector-machines"></a>Informacje o maszynach wektor pomocy technicznej

Obsługa wektor maszyn są pomiędzy najwcześniejszym algorytmów uczenia maszynowego i modele SVM były używane w wielu aplikacjach z pobierania informacji z klasyfikacją tekstu i obrazów. SVMs może służyć do klasyfikacji i regresji zadań.

Ten model SVM jest model uczenia nadzorowanego, który wymaga etykietami danych. W procesie szkolenia algorytm analizuje dane wejściowe i rozpoznaje wzorce przestrzeni wielowymiarowych funkcji o nazwie *hyperplane*.  Wszystkie przykłady wejściowe są reprezentowane jako punkty, w tym miejscu i są mapowane na dane wyjściowe kategorie w taki sposób, że kategorie są podzielone według jako całej i wyczyść przerwa jak to możliwe.

Algorytm SVM przewidywania, przypisuje nowe przykłady w jednej kategorii lub innych, mapując je do tego samego obszaru. 

## <a name="how-to-configure"></a>Jak skonfigurować 

Dla tego typu modelu zalecane jest znormalizować zestawu danych przed użyciem w celu nauczenia klasyfikatora.
  
1.  Dodaj **Two-Class pomocy technicznej Vector Machine** modułu do eksperymentu.  
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować modelu, możesz podać określonych wartości jako argumenty.  

3.  Aby uzyskać **liczby iteracji**, wpisz numer, który oznacza liczbę iteracji podczas tworzenia modelu.  
  
     Ten parametr może służyć do kontrolowania kompromisu między szkolenia szybkość i dokładność.  
  
4.  Aby uzyskać **Lambda**, wpisz wartość do użycia jako wagę L1 uregulowania.  
  
     Współczynnika uregulowania można dostrajanie modelu. Większe wartości ukarania bardziej złożonych modeli.  
  
5.  Wybierz opcję **normalizacji funkcji**, jeśli chcesz znormalizować funkcji przed szkolenia.
  
     Jeśli zastosujesz normalizacji przed szkolenia, punkty danych są skupia się na średnią i dostosowana tak, aby mieć jedną jednostkę odchylenia standardowego.
  
6.  Wybierz opcję **projektu go na kulę jednostki**, aby znormalizować współczynniki.
  
     Prognozowanie wartości do obszaru jednostki oznacza, że wyśrodkowany w lokalizacji 0 i dostosowana tak, aby mieć jedną jednostkę odchylenie standardowe przed szkolenia, punktów danych.
  
7.  W **inicjatora liczb losowych**, wpisać wartość całkowitą do użycia jako inicjator, jeśli chcesz zapewnić odtwarzaniem we wszystkich przebiegach aktualizacji.  W przeciwnym razie wartość Zegar systemu jest używana jako zalążek, co może skutkować nieco różne wyniki we wszystkich przebiegach aktualizacji.
  
9. Połącz etykietami zestawu danych, a jedna z [modułów szkoleniowych](module-reference.md):
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](train-model.md) modułu.
  

10. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu, wraz z wagi funkcji z szkolenia, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md)i wybierz **Visualize**.

+ Aby użyć uczone modele do prognozowania, należy połączyć nauczonemu modelowi w celu [Score Model](score-model.md) modułu.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 