---
title: 'Las decyzyjny kontra: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu las decyzyjny kontra w usłudze Azure Machine Learning w celu utworzenia usługi machine learning model oparty na *decyzji lasu* algorytmu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 10364d014431a500e7c38a02d47f432cd464feb6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411479"
---
# <a name="multiclass-decision-forest-module"></a>Las decyzyjny kontra modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia usługi machine learning model oparty na *decyzji lasu* algorytmu. Las decyzyjny jest model zespołu, który szybko tworzy serię drzewa decyzyjne, podczas uczenia z danych oznakowanych.

## <a name="more-about-decision-forests"></a>Więcej informacji na temat decyzji lasów

Algorytm lasu decyzji jest zespołu uczenia metody klasyfikacji. Algorytm polega na tworzeniu wielu drzewa decyzyjne i następnie *głosowania* najpopularniejszych klasy danych wyjściowych. Głosowanie jest formą agregacji, w której każdy drzewie decyzji klasyfikacji lasu generuje histogram nieznormalizowanego częstotliwość etykiet. Proces agregacji sumuje te histogramów i normalizuje wynik, który ma uzyskać "prawdopodobieństwa" dla każdej etykiety. Drzewa, które mają wysokie prognoz mają większą wagę w ostateczną decyzję zespole.

Drzewa decyzyjne są ogólnie rzecz biorąc — parametrycznych modeli, co oznacza, że obsługują one danych przy użyciu różnorodnych dystrybucji. W każdym drzewie sekwencję proste testy jest uruchamiana dla każdej klasy, coraz większe poziomy struktura drzewa, aż do osiągnięcia węzeł liścia (decyzja).

Drzewa decyzyjne mają wiele zalet:

+ Mogą one reprezentować granice nieliniowych decyzji.
+ Są wydajne obliczeń i użycie pamięci podczas uczenia i przewidywania.
+ Wykonują zintegrowaną funkcją wyboru i klasyfikacji.
+ Są one odporność obecności funkcje generujące dużo alertów.

Klasyfikator lasu decyzji w usłudze Azure Machine Learning składa się z zespołu drzewa decyzyjne. Ogólnie rzecz biorąc zespołu zapewnia lepsze pokrycie i dokładność niż pojedynczy decyzyjne. Aby uzyskać więcej informacji, zobacz [decyzji drzew](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak skonfigurować las decyzyjny kontra



1. Dodaj **las decyzyjny kontra** modułu do eksperymentu w interfejsie. Można znaleźć w tym module w ramach **uczenia maszynowego**, **zainicjować modelu**, i **klasyfikacji**.

2. Kliknij dwukrotnie moduł, aby otworzyć **właściwości** okienka.

3. Dla **próbkowanie metoda**, wybierz metodę używaną do tworzenia indywidualnych drzewa.  Możesz wybrać z workowania lub replikacji.

    + **Pakowanie**: Skrót workowania *bootstrap agregowania*. W przypadku tej metody każdego drzewa jest wzrosła na nowe próbki, utworzone przez losowo próbkowanie oryginalnego zestawu danych za pomocą zastąpienia, dopóki nie ma rozmiar oryginalnego zestawu danych. Dane wyjściowe modele są łączone za *głosowania*, czyli rodzaj agregacji. Aby uzyskać więcej informacji zobacz wpis Wikipedia do agregowania Bootstrap.

    + **Replikowanie**: W przypadku replikacji każdego drzewa jest uczony w dokładnie tymi samymi danymi wejściowymi. Określenie, które podziału predykatem, po którym jest używane dla każdego węzła drzewa pozostaje losowe tworzenia różnorodnych drzew.

   

4. Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.

    + **Pojedynczy parametr**: Wybierz tę opcję, jeśli wiesz, jak chcesz skonfigurować modelu i udostępniają zestaw wartości jako argumenty.


5. **Liczba drzew decyzyjnych**: Wpisz maksymalną liczbę drzewa decyzyjne, które mogą być tworzone w zespole. Tworząc więcej drzewa decyzyjne, mogą potencjalnie uzyskać lepszego pokrycia, ale może zwiększyć czas szkolenia.

    Ta wartość kontroluje również liczbę drzew wyświetlane w wynikach, gdy wizualizacja uczonego modelu. Aby wyświetlić lub wydrukować jedno drzewo, należy ustawić wartość na 1; oznacza to jednak tylko jeden tree może być generowany (drzewo za pomocą początkowego zestawu parametrów), a są wykonywane nie dalsze iteracji.

6. **Maksymalna głębokość drzewa decyzyjne**: Wpisz liczbę Aby ograniczyć maksymalną głębokość wszelkie drzewa decyzyjnego. Zwiększenie Głębokość drzewa może zwiększyć dokładność ryzyko chwilę szkolenia overfitting i zwiększenia.

7. **Liczby losowe dzieli dane na węzeł**: Wpisz liczbę dzieli dane do użycia podczas tworzenia każdego węzła drzewa. A *podziału* losowo dzielą oznacza, że funkcje w każdym poziomie drzewa (node).

8. **Minimalna liczba próbek na węzeł liścia**: Wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła w terminalu (liść) w drzewie. Zwiększenie tej wartości, można zwiększyć wartość progową do tworzenia nowych zasad.

    Na przykład wartość domyślną 1, jeden przypadek może spowodować nową regułę, która ma zostać utworzony. Jeśli zwiększysz wartość 5 dane szkoleniowe musi zawierać co najmniej pięć przypadki, które spełniają te same warunki.



10. Połącz z etykietami zestawu danych, a jeden z modułów szkolenia:

    + Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](./train-model.md) modułu.

11. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić drzewa, który został utworzony w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) modułu, a następnie wybierz **Visualize**.
+ Aby wyświetlić zasady dla każdego węzła, kliknij każdy drzewa, aby przejść do dzieli dane.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 