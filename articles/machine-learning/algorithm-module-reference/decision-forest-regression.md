---
title: 'Regresja lasu decyzji: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji lasu decyzji w usłudze Azure Machine Learning do utworzenia modelu regresji na podstawie zestawu drzew decyzyjnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456543"
---
# <a name="decision-forest-regression-module"></a>Moduł regresji lasu decyzji

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu regresji na podstawie zespołu drzew decyzyjnych.

Po skonfigurowaniu modelu należy trenować model przy użyciu zestawu danych z etykietą i modułu [Model pociągu.](./train-model.md) Przeszkolony model może następnie służyć do prognozowania. 

## <a name="how-it-works"></a>Jak to działa

Drzewa decyzyjne są modelami nie parametrycznymi, które wykonują sekwencję prostych testów dla każdego wystąpienia, przechodząc przez strukturę danych drzewa binarnego, dopóki nie zostanie osiągnięty węzeł liścia (decyzja).

Drzewa decyzyjne mają następujące zalety:

- Są one wydajne w obliczeniach i użycia pamięci podczas szkolenia i przewidywania.

- Mogą one reprezentować nieliniowe granice decyzji.

- Wykonują one zintegrowany wybór i klasyfikację funkcji i są odporne w obecności hałaśliwych funkcji.

Ten model regresji składa się z zespołu drzew decyzyjnych. Każde drzewo w lesie decyzji regresji wyprowadza rozkład Gaussa jako prognozę. Agregacja jest wykonywana przez zespół drzew, aby znaleźć rozkład gaussowski najbliżej połączonego rozkładu dla wszystkich drzew w modelu.

Aby uzyskać więcej informacji na temat teoretycznych ram dla tego algorytmu i jego implementacji, zobacz ten artykuł: [Lasy decyzyjne: Ujednolicone ramy klasyfikacji, regresji, szacowania gęstości, uczenia się kolektora i uczenia się półnadzorowanego](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak skonfigurować model regresji lasu decyzji

1. Dodaj moduł **regresji lasu decyzji** do potoku. Moduł można znaleźć w projektancie w obszarze **Uczenie maszynowe**, **Inicjalizuj model**i **Regresja**.

2. Otwórz właściwości modułu, a w przypadku **metody ponownej próby**wybierz metodę używaną do tworzenia poszczególnych drzew.  Możesz wybrać **opcję Bagging** lub **Replicate**.

    - **Bagging**: Bagging jest również nazywany *bootstrap agregacji*. Każde drzewo w lesie decyzji regresji wyprowadza rozkład Gaussa w drodze przewidywania. Agregacja polega na znalezieniu Gaussa, którego pierwsze dwie chwile pasują do momentów mieszaniny rozkładów gaussowskich, które zostały podane przez połączenie wszystkich rozkładów zwróconych przez poszczególne drzewa.

         Aby uzyskać więcej informacji, zobacz wpis w Wikipedii dla [agregowania Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikacja:** W replikacji każde drzewo jest przeszkolone na dokładnie tych samych danych wejściowych. Określenie, które predykatu podziału jest używany dla każdego węzła drzewa pozostaje losowe i drzewa będą zróżnicowane.

         Aby uzyskać więcej informacji na temat procesu szkolenia z opcją **Replikacja,** zobacz [Prawa decyzyjne dla wizji komputerowej i analizy obrazu medycznego. Criminisi i J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**

    - **Pojedynczy parametr**

      Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty. Być może nauczyłeś się tych wartości przez eksperymentowanie lub otrzymałeś je jako wskazówki.

    - **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki. 



4. W przypadku **liczby drzew decyzyjnych**należy wskazać całkowitą liczbę drzew decyzyjnych do utworzenia w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia wzrośnie.

    > [!TIP]
    > Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji modelu uczonego. Jeśli chcesz wyświetlić lub wydrukować pojedyncze drzewo, możesz ustawić wartość na 1; oznacza to jednak, że zostanie wyprodukowane tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie zostaną wykonane żadne dalsze iteracje.

5. W przypadku **maksymalnej głębokości drzew decyzyjnych**wpisz liczbę, aby ograniczyć maksymalną głębokość dowolnego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję, na ryzyko niektórych overfitting i wydłużony czas szkolenia.

6. W **polach Liczba podziałów losowych na węzeł**wpisz liczbę podziałów, które mają być używane podczas tworzenia każdego węzła drzewa. *Podział* oznacza, że operacje na każdym poziomie drzewa (węzła) są losowo podzielone.

7. Dla **minimalnej liczby próbek na węzeł liścia**, należy wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminala (liść) w drzewie.

     Zwiększając tę wartość, można zwiększyć próg tworzenia nowych reguł. Na przykład z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. Jeśli zwiększysz wartość do 5, dane szkoleniowe będą musiały zawierać co najmniej pięć przypadków, które spełniają te same warunki.


9. Połącz oznaczony etykietą zestaw danych, wybierz pojedynczą kolumnę etykiet zawierającą nie więcej niż dwa wyniki i połącz się z [modelem pociągu](./train-model.md).

    - Jeśli **ustawisz** opcję Utwórz tryb trenera na **Pojedynczy parametr,** trenuj model przy użyciu modułu [Model pociągu.](./train-model.md)

   

10. Prześlij potok.

### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz moduł szkolenia, a następnie przełącz się na kartę **Wyjścia** w prawym panelu. Kliknij ikonę **Zarejestruj model**.  Zapisany model można znaleźć jako moduł w drzewie modułów. 

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 