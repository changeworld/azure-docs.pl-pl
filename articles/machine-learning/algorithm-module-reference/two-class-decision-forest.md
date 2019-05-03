---
title: 'Las decyzyjny Two-Class: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu las decyzyjny Two-Class w usłudze Azure Machine Learning, aby utworzyć model na podstawie decyzji lasów algorytmu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f3e7cab6e33fa9373095441685f6ecb04f1d91a5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029373"
---
# <a name="two-class-decision-forest-module"></a>Las decyzyjny Two-Class modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu na podstawie decyzji lasów algorytmu uczenia maszynowego.  

Decyzja lasach są szybkie, nadzorowanym zespołu modeli. Ten moduł jest dobrym rozwiązaniem, jeśli chcesz przewidzieć element docelowy z maksymalnie dwie sytuacje. 

## <a name="understanding-decision-forests"></a>Opis lasów decyzji

Ten algorytm lasu decyzji jest zespołu uczenia się metody przeznaczone do zadań klasyfikacji. Metody zespołu są oparte na ogólną zasadą, zamiast polegać na jednym modelu, można uzyskać lepsze wyniki i bardziej ogólnych modelu przez tworzenie wielu modeli powiązanych i łącząc je w jakiś sposób. Ogólnie rzecz biorąc zespołu zapewnia lepsze pokrycie i dokładność niż pojedynczy decyzyjne. 

Istnieje wiele sposobów tworzenia pojedynczych modeli i połączyć je w zespół. Tej określonej implementacji las decyzyjny polega na tworzeniu wielu drzewa decyzyjne i następnie **głosowania** najpopularniejszych klasy danych wyjściowych. Głosowania jest jedną z metod better-known do generowania wyników w modelu zespołu. 

+ Wiele drzew poszczególnych klasyfikacji są tworzone, za pomocą cały zestaw danych, ale różnych (zazwyczaj wybierane) punktów początkowych. Różni się to od podejścia losowe lasu, w której poszczególne decyzyjne może składać się tylko część losowego danych lub funkcji.
+ Każdego drzewa w lesie algorytm generuje histogram nieznormalizowanego częstotliwość etykiet. 
+ Proces agregacji sumuje te histogramów i normalizuje wynik, który ma uzyskać "prawdopodobieństwa" dla każdej etykiety. 
+ Drzewa, które mają wysokie prognoz będzie miała większa waga w ostateczną decyzję zespole.

Drzewa decyzyjne ogólnie rzecz biorąc mają wiele zalet dla zadań klasyfikacji:
  
- Można rejestrować granice nieliniowych decyzji.
- Można uczenia i przewidywania na dużą ilość danych, ponieważ są one swoją wydajność WE obliczeń oraz użycia pamięci.
- Wybór funkcji jest zintegrowana z procesami szkolenia i klasyfikacji.  
- Drzewa może obsłużyć hałas dane i wiele funkcji.  
- Są one-parametrycznych modeli, co oznacza, że można obsługują dane z różnych dystrybucjach. 

Jednak proste decyzyjne można overfit danych i są mniej generalizacji niż drzewa decyzyjne.

Aby uzyskać więcej informacji, zobacz [lasów decyzji](http://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Jak skonfigurować
  
1.  Dodaj **las decyzyjny Two-Class** modułu do eksperymentu w usłudze Azure Machine Learning i Otwórz **właściwości** okienko modułu. 

    Można znaleźć modułu w obszarze **uczenia maszynowego**. Rozwiń **zainicjować**, a następnie **klasyfikacji**.  
  
2.  Dla **próbkowanie metoda**, wybierz metodę używaną do tworzenia indywidualnych drzewa.  Możesz wybrać spośród **pakowanie** lub **replikować**.  
  
    -   **Pakowanie**: Skrót workowania *bootstrap agregowania*. W przypadku tej metody każdego drzewa jest wzrosła na nowe próbki, utworzone przez losowo próbkowanie oryginalnego zestawu danych za pomocą zastąpienia, dopóki nie ma rozmiar oryginalnego zestawu danych.  
  
         Dane wyjściowe modele są łączone za *głosowania*, czyli rodzaj agregacji. Każdy drzewa w lesie decyzji klasyfikacji danych wyjściowych histogram unnormalized częstotliwość etykiet. Agregacja jest suma tych histogramów i normalizacji uzyskać "prawdopodobieństwa" dla każdej etykiety. W ten sposób drzew, które mają wysokie prognoz będzie większa waga ostateczną decyzję zespole.  
  
         Aby uzyskać więcej informacji zobacz wpis Wikipedia do agregowania Bootstrap.  
  
    -   **Replikowanie**: W przypadku replikacji każdego drzewa jest uczony w dokładnie tymi samymi danymi wejściowymi. Określenie, które podziału predykatem, po którym jest używane dla każdego węzła drzewa pozostaje losowe drzew. zostanie ona zróżnicowany.   
  
3.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować modelu, możesz podać określonych wartości jako argumenty.
  
4.  Aby uzyskać **numer drzewa decyzyjne**, wpisz maksymalną liczbę drzewa decyzyjne, które mogą być tworzone w zespole. Tworząc więcej drzewa decyzyjne, mogą potencjalnie uzyskać lepszego pokrycia, ale zwiększa czasu szkoleń.  
  
    > [!NOTE]
    >  Ta wartość kontroluje również liczbę drzew wyświetlane, gdy wizualizacja uczonego modelu. Jeśli chcesz wyświetlić lub wydrukować jedno drzewo, można ustawić wartość 1. Jednak może być tylko jeden drzewa utworzone (drzewo za pomocą początkowego zestawu parametrów) i są wykonywane nie dalsze iteracji.
  
5.  Aby uzyskać **maksymalną głębokość drzewa decyzyjne**, wpisz liczbę aby ograniczać maksymalną głębokość wszelkie drzewa decyzyjnego. Zwiększenie Głębokość drzewa może zwiększyć dokładność ryzyko chwilę szkolenia overfitting i zwiększenia.
  
6.  Aby uzyskać **liczby losowe dzieli dane na węzeł**, wpisz liczbę dzieli dane do użycia podczas tworzenia każdego węzła drzewa. A *podziału* losowo dzielą oznacza, że funkcje w każdym poziomie drzewa (node).
  
7.  Aby uzyskać **minimalna liczba próbek na węzeł liścia**, wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła w terminalu (liść) w drzewie.
  
     Zwiększenie tej wartości, można zwiększyć wartość progową do tworzenia nowych zasad. Na przykład wartość domyślną 1, jeden przypadek może spowodować nową regułę, która ma zostać utworzony. Jeśli zwiększysz wartość 5 dane szkoleniowe musi zawierać co najmniej pięć przypadki, które spełniają te same warunki.  
  
8.  Wybierz **Zezwalaj na nieznane wartości podzielonych na kategorie funkcji** opcję, aby utworzyć grupę dla nieznanego wartości w zestawach szkolenia lub sprawdzania poprawności. Model może być mniej dokładny w przypadku znanych wartości, ale funkcja ta może zapewnić lepszą prognoz dotyczących nowych wartości (nieznany). 

     Jeśli wyłączysz tę opcję, model może zaakceptować tylko wartości, które są zawarte w danych szkoleniowych.
  
9. Dołącz etykietami zestawu danych, a jedna z [modułów szkoleniowych](module-reference.md):  
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](./train-model.md) modułu.  
  
    
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić drzewa, który został utworzony w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) modułu, a następnie wybierz **Visualize**.
  
    Kliknij każdy drzewo, aby przejść do dzieli dane i wyświetlić reguły dla każdego węzła.

+ Aby zapisać migawkę modelu, kliknij prawym przyciskiem myszy **Uczonego modelu** danych wyjściowych, a następnie wybierz **Zapisywanie modelu**. Zapisany model nie jest aktualizowany na kolejnych przebiegów eksperymentu.

+ Aby użyć modelu do oceniania, Dodaj **Score Model** modułu do eksperymentu.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 