---
title: 'Las decyzyjny dwuklasowy: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Dwuklasowy las decyzji w usłudze Azure Machine Learning do utworzenia modelu uczenia maszynowego na podstawie algorytmu lasów decyzyjnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916714"
---
# <a name="two-class-decision-forest-module"></a>Moduł dwuklasowy moduł "Decision Forest"

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie algorytmu lasów decyzji.  

Lasy decyzyjne są szybkimi, nadzorowane modele zespołów. Ten moduł jest dobrym wyborem, jeśli chcesz przewidzieć cel z maksymalnie dwoma wynikami. 

## <a name="understanding-decision-forests"></a>Zrozumienie lasów decyzyjnych

Ten algorytm lasu decyzji jest metoda uczenia zespołu przeznaczone do zadań klasyfikacji. Metody zespołu są oparte na ogólnej zasadzie, że zamiast polegać na jednym modelu, można uzyskać lepsze wyniki i bardziej uogólniony model, tworząc wiele powiązanych modeli i łącząc je w jakiś sposób. Ogólnie rzecz biorąc, modele zespołu zapewniają lepsze pokrycie i dokładność niż drzewa pojedynczej decyzji. 

Istnieje wiele sposobów tworzenia poszczególnych modeli i łączenia ich w zespole. To konkretne wdrożenie decyzji leśnej działa poprzez budowę wielu drzew decyzyjnych, a następnie **głosowanie** na najbardziej popularne klasy produkcji. Głosowanie jest jedną z lepiej znanych metod generowania wyników w modelu zespołu. 

+ Wiele poszczególnych drzew klasyfikacji są tworzone przy użyciu całego zestawu danych, ale różne (zwykle losowo) punkty początkowe. Różni się to od metody losowego lasu, w którym poszczególne drzewa decyzyjne mogą używać tylko niektórych losowych części danych lub funkcji.
+ Każde drzewo w drzewie leśnym decyzji wyprowadza histogram częstotliwości nieznormalizowany etykiet. 
+ Proces agregacji sumuje te histogramy i normalizuje wynik, aby uzyskać "prawdopodobieństwa" dla każdej etykiety. 
+ Drzewa, które mają wysoką pewność przewidywania będą miały większą wagę w ostatecznej decyzji zespołu.

Drzewa decyzyjne w ogóle mają wiele zalet dla zadań klasyfikacyjnych:
  
- Mogą przechwytywać nieliniowe granice decyzji.
- Można trenować i przewidywać na wiele danych, ponieważ są one wydajne w obliczeniach i użycia pamięci.
- Wybór funkcji jest zintegrowany z procesami szkolenia i klasyfikacji.  
- Drzewa mogą pomieścić hałaśliwe dane i wiele funkcji.  
- Są to modele nie parametryczne, co oznacza, że mogą obsługiwać dane z różnymi dystrybucjami. 

Jednak proste drzewa decyzyjne mogą przepasowyać dane i są mniej uogólnione niż zespoły drzew.

Aby uzyskać więcej informacji, zobacz ["Lasy decyzyjne](https://go.microsoft.com/fwlink/?LinkId=403677)"  

## <a name="how-to-configure"></a>Jak skonfigurować
  
1.  Dodaj moduł **Las decyzji dwuklasowej** do potoku w usłudze Azure Machine Learning i otwórz okienko **Właściwości** modułu. 

    Moduł można znaleźć w obszarze **Uczenie maszynowe**. Rozwiń pozycję **Inicjowanie**, a następnie **klasyfikacja**.  
  
2.  W przypadku **metody ponownego**próby wybierz metodę używaną do tworzenia poszczególnych drzew.  Możesz wybrać **opcję Bagging** lub **Replicate**.  
  
    -   **Bagging**: Bagging jest również nazywany *bootstrap agregacji*. W tej metodzie każde drzewo jest uprawiane na nowej próbce, tworzone przez losowe próbkowanie oryginalnego zestawu danych z zastąpieniem, dopóki nie masz zestawu danych o rozmiarze oryginału.  
  
         Wyniki modeli są łączone przez *głosowanie,* co jest formą agregacji. Każde drzewo w lesie decyzji o klasyfikacji wyprowadza histogram częstotliwości nienormalizowanej etykiet. Agregacja jest zsumowanie tych histogramów i normalizacji, aby uzyskać "prawdopodobieństwa" dla każdej etykiety. W ten sposób drzewa, które mają wysoką pewność przewidywania będą miały większą wagę w ostatecznej decyzji zespołu.  
  
         Aby uzyskać więcej informacji, zobacz wpis w Wikipedii dla agregowania Bootstrap.  
  
    -   **Replikacja:** W replikacji każde drzewo jest przeszkolone na dokładnie tych samych danych wejściowych. Określenie, które predykatu podziału jest używany dla każdego węzła drzewa pozostaje losowe i drzewa będą zróżnicowane.   
  
3.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.

    -   **Zakres parametrów:** Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry za pomocą modułu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Należy podać zakres wartości, a trener iteruje przez wiele kombinacji ustawień, aby określić kombinację wartości, która daje najlepszy wynik.
  
4.  W przypadku **liczby drzew decyzyjnych**wpisz maksymalną liczbę drzew decyzyjnych, które mogą być tworzone w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia wzrasta.  
  
    > [!NOTE]
    >  Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji modelu uczonego. Jeśli chcesz wyświetlić lub wydrukować pojedyncze drzewo, możesz ustawić wartość na 1. Jednak tylko jedno drzewo może być produkowane (drzewo z początkowym zestawem parametrów) i nie są wykonywane dalsze iteracje.
  
5.  W przypadku **maksymalnej głębokości drzew decyzyjnych**wpisz liczbę, aby ograniczyć maksymalną głębokość dowolnego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję, na ryzyko niektórych overfitting i wydłużony czas szkolenia.
  
6.  W **polach Liczba podziałów losowych na węzeł**wpisz liczbę podziałów, które mają być używane podczas tworzenia każdego węzła drzewa. *Podział* oznacza, że operacje na każdym poziomie drzewa (węzła) są losowo podzielone.
  
7.  Dla **minimalnej liczby próbek na węzeł liścia**, należy wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminala (liść) w drzewie.
  
     Zwiększając tę wartość, można zwiększyć próg tworzenia nowych reguł. Na przykład z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. Jeśli zwiększysz wartość do 5, dane szkoleniowe będą musiały zawierać co najmniej pięć przypadków, które spełniają te same warunki.  
  
8.  Wybierz opcję **Zezwalaj na nieznane wartości dla obiektów kategorii,** aby utworzyć grupę dla nieznanych wartości w zestawach szkolenia lub sprawdzania poprawności. Model może być mniej precyzyjne dla znanych wartości, ale może zapewnić lepsze prognozy dla nowych (nieznanych) wartości. 

     Jeśli usuniesz zaznaczenie tej opcji, model może zaakceptować tylko wartości, które są zawarte w danych szkoleniowych.
  
9. Dołącz oznaczony zestaw danych i jeden z [modułów szkoleniowych:](module-reference.md)  
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](./train-model.md)  
    
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

+ Aby użyć modelu do oceniania, dodaj **wynik modelu** modułu do potoku.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 