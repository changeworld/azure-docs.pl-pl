---
title: 'Las decyzyjny z regresji: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu regresji lasu decyzji w usłudze Azure Machine Learning, aby utworzyć model regresji, w oparciu o zespołu drzewa decyzyjne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442360"
---
# <a name="decision-forest-regression-module"></a>Las decyzyjny z regresji modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Używaj tego modułu, aby utworzyć model regresji, w oparciu o zespołu drzewa decyzyjne.

Po skonfigurowaniu modelu musi nauczenia modelu używając etykietami zestawu danych i [uczenie modelu](./train-model.md) modułu.  Następnie można uczonego modelu do przewidywania przyszłych zdarzeń. 

## <a name="how-it-works"></a>Jak to działa

Drzewa decyzyjne są-parametrycznych dla modeli, które wykonują sekwencję proste testy dla każdego wystąpienia struktury drzewa danych binarnych przechodzenie, aż do osiągnięcia węzeł liścia (decyzja).

Drzewa decyzyjne ma następujące zalety:

- Są wydajne zarówno obliczeń, jak i użycie pamięci podczas uczenia i przewidywania.

- Mogą one reprezentować granice nieliniowych decyzji.

- One zintegrowaną funkcją wyboru i klasyfikacji i odporności obecności funkcje generujące dużo alertów.

Ten model regresji składa się z zespołu drzewa decyzyjne. Każdy drzewa w las decyzyjny regresji generuje dystrybucji gaussowskie jako prognozę. Agregacja odbywa się za pośrednictwem zespołu drzew odnaleźć dystrybucji gaussowskie najbliżej rozkład połączony dla wszystkich drzew w modelu.

Aby uzyskać więcej informacji na temat teoretycznych struktury do tego algorytmu i jego wdrożenie znajduje się w artykule: [Lasy decyzji: Ujednolicona struktura klasyfikacji, regresji, Szacowanie gęstość kolektora uczenia i uczenia nadzorowanego częściową](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak skonfigurować Model regresji lasu decyzji

1. Dodaj **regresji lasu decyzji** modułu do eksperymentu. Można znaleźć modułu w interfejsie, w obszarze **uczenia maszynowego**, **zainicjować modelu**, i **regresji**.

2. Otwórz właściwości modułu i **próbkowanie metoda**, wybierz metodę używaną do tworzenia indywidualnych drzewa.  Możesz wybrać spośród **pakowanie** lub **replikować**.

    - **Pakowanie**: Skrót workowania *bootstrap agregowania*. Każdy drzewa w las decyzyjny regresji generuje dystrybucji gaussowskie za pomocą prognozy. Agregacji ma na celu znalezienie gaussowskie, w której pierwsze dwa momenty dopasowania chwil mieszanki Gaussians podane, łącząc wszystkie Gaussians zwróconych przez poszczególne drzewa.

         Aby uzyskać więcej informacji, zobacz wpis Wikipedia dla [Bootstrap agregowania](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikowanie**: W przypadku replikacji każdego drzewa jest uczony w dokładnie tymi samymi danymi wejściowymi. Określenie, które podziału predykatem, po którym jest używane dla każdego węzła drzewa pozostaje losowe drzew. zostanie ona zróżnicowany.

         Aby uzyskać więcej informacji na temat procesu uczenia z **replikować** opcji, zobacz [lasy decyzji dla przetwarzania obrazów i analizy medycznej obrazu. Criminisi i J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.

    - **Jeden parametr**

      Jeśli wiesz, jak chcesz skonfigurować modelu, możesz podać określonych wartości jako argumenty. Może mieć wartości te rozpoznawane przez eksperymentowanie w usłudze lub ich odebrania jako wskazówki.



4. Aby uzyskać **numer drzewa decyzyjne**, wskazywać sumę drzewa decyzyjne, które można utworzyć w zespole. Tworząc więcej drzewa decyzyjne, mogą potencjalnie uzyskać lepszego pokrycia, ale wypoczynkowych więc ceny wzrosną.

    > [!TIP]
    > Ta wartość kontroluje również liczbę drzew wyświetlane, gdy wizualizacja uczonego modelu. Jeśli chcesz wyświetlić lub wydrukować jedno drzewo, wartość można ustawić na 1; oznacza to jednak tylko jeden tree będzie generowany (drzewo za pomocą początkowego zestawu parametrów), a zostaną wykonane nie dalsze iteracji.

5. Aby uzyskać **maksymalną głębokość drzewa decyzyjne**, wpisz liczbę aby ograniczać maksymalną głębokość wszelkie drzewa decyzyjnego. Zwiększenie Głębokość drzewa może zwiększyć dokładność ryzyko chwilę szkolenia overfitting i zwiększenia.

6. Aby uzyskać **liczby losowe dzieli dane na węzeł**, wpisz liczbę dzieli dane do użycia podczas tworzenia każdego węzła drzewa. A *podziału* losowo dzielą oznacza, że funkcje w każdym poziomie drzewa (node).

7. Aby uzyskać **minimalna liczba próbek na węzeł liścia**, wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła w terminalu (liść) w drzewie.

     Zwiększenie tej wartości, można zwiększyć wartość progową do tworzenia nowych zasad. Na przykład wartość domyślną 1, jeden przypadek może spowodować nową regułę, która ma zostać utworzony. Jeśli zwiększysz wartość 5 dane szkoleniowe musi zawierać co najmniej pięć przypadki, które spełniają te same warunki.


9. Łączą z etykietami zestawu danych, wybierz kolumnę pojedynczej etykiecie, zawierającą nie więcej niż dwie sytuacje i [Train Model](./train-model.md).

    - Jeśli ustawisz **trybie trainer tworzenia** opcję **pojedynczy parametr**, uczenie modelu przy użyciu [uczenie modelu](./train-model.md) modułu.

   

10. Uruchom eksperyment.

### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić drzewa, który został utworzony w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe modułu szkolenia, a następnie wybierz pozycję **Visualize**.

+ Aby wyświetlić zasady dla każdego węzła, kliknij pozycję w każdym drzewie i przejść do dzieli dane.

+ Aby zapisać migawkę trenowanego modelu, kliknij prawym przyciskiem myszy dane wyjściowe modułu szkolenia, a następnie wybierz pozycję **zapisania Trenowanego modelu**. Ta kopia modelu nie jest aktualizowany na kolejnych przebiegów eksperymentu. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 