---
title: 'Regresja lasu decyzyjnego: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu regresji lasów w Azure Machine Learning, aby utworzyć model regresji na podstawie kompletu drzew decyzyjnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 3c412937e59927a9f3f3218b0da3382f0b67c230
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152384"
---
# <a name="decision-forest-regression-module"></a>Moduł regresji lasu decyzyjnego

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Użyj tego modułu, aby utworzyć model regresji na podstawie kompletu drzew decyzyjnych.

Po skonfigurowaniu modelu należy przeprowadzić uczenie modelu przy użyciu zestawu danych z etykietami i modułu [uczenie modelu](./train-model.md) . Model przeszkolony może być następnie używany do prognozowania. 

## <a name="how-it-works"></a>Jak to działa

Drzewa decyzyjne są modelami nieparametrycznymi, które wykonują sekwencję prostych testów dla każdego wystąpienia, przechodząc do struktury danych drzewa binarnego do momentu osiągnięcia węzła liścia (decyzja).

Drzewa decyzyjne mają następujące zalety:

- Są one wydajne w przypadku obliczeń i wykorzystania pamięci podczas uczenia i przewidywania.

- Mogą reprezentować granice decyzyjne nieliniowe.

- Zapewniają one zintegrowane Wybieranie funkcji i klasyfikację i są odporne na obecność funkcji szumów.

Ten model regresji składa się z kompletów drzew decyzyjnych. Każde drzewo w lesie z decyzją regresji wyprowadza rozkład gaussowskie jako prognozowanie. Agregacja jest wykonywana w ramach zestawu drzew, aby znaleźć rozkład gaussowskie najbliżej połączonej dystrybucji dla wszystkich drzew w modelu.

Aby uzyskać więcej informacji na temat teoretycznej struktury tego algorytmu i jego implementacji, zobacz ten artykuł: [lasy decyzyjne: ujednolicona struktura klasyfikacji, regresji, oszacowania gęstości, uczenie kolektora i uczenie Międzynadzorowane](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Konfigurowanie modelu regresji lasu decyzyjnego

1. Dodaj moduł **regresji lasu decyzji** do potoku. Moduł można znaleźć w projektancie w obszarze **Machine Learning**, **zainicjuj model**i **regresję**.

2. Otwórz właściwości modułu i dla **metody ponownej próbkowania**wybierz metodę użytą do utworzenia poszczególnych drzew.  Możesz wybrać jedną z opcji **Working** lub **replikacja**.

    - **Working**: w workach jest również wywoływana *agregacja Bootstrap*. Każde drzewo w lesie z decyzją regresji wyprowadza rozkład gaussowskie w drodze przewidywania. Agregacją jest znalezienie gaussowskie, którego pierwsze dwa momenty są zgodne z chwilą mieszaniny rozkładów gaussowskie przyznanych przez połączenie wszystkich dystrybucji zwracanych przez poszczególne drzewa.

         Aby uzyskać więcej informacji, zobacz wpis Wikipedia na potrzeby [agregowania Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikacja**: w replikacji Każde drzewo jest przeszkolone dokładnie na te same dane wejściowe. Określenie, który predykat Split jest używany dla każdego węzła drzewa, pozostaje losowy, a drzewa będą różne.

         Aby uzyskać więcej informacji na temat procesu szkolenia z opcją **replikacja** , zobacz [lasy decyzyjne dla przetwarzanie obrazów i analizy obrazu medycznego. Criminisi i J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)..

3. Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .

    - **Pojedynczy parametr**

      Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty. Te wartości mogą być uzyskane przez eksperymentowanie lub otrzymywanie jako wskazówki.



4. W przypadku **liczby drzew decyzyjnych**należy wskazać łączną liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale zwiększy się czas uczenia.

    > [!TIP]
    > Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji przeszkolonego modelu. Jeśli chcesz zobaczyć lub wydrukować pojedyncze drzewo, możesz ustawić wartość 1. oznacza to jednak, że zostanie utworzone tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i żadna kolejna iteracja nie zostanie wykonana.

5. W celu uzyskania **maksymalnej głębokości drzew decyzyjnych**wpisz liczbę, aby ograniczyć maksymalną głębokość każdego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję w przypadku ryzyka pewnego przekroczenia i zwiększenia czasu uczenia się.

6. Dla **liczby losowych podziałów na węzeł**wpisz liczbę podziałów, która ma być używana podczas kompilowania każdego węzła drzewa. *Podział* oznacza, że funkcje na każdym poziomie drzewa (węzeł) są losowo podzielone.

7. Dla **minimalnej liczby próbek na węzeł liścia**wskaż minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminalu (liścia) w drzewie.

     Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.


9. Połącz zestaw danych z etykietą, wybierz jedną kolumnę etykiety zawierającą nie więcej niż dwa wyniki, a następnie połącz się z [modelem uczenia](./train-model.md).

    - Jeśli ustawisz opcję **tworzenia trybu Trainer** na **pojedynczy parametr**, nauczenie modelu przy użyciu modułu [uczenie modelu](./train-model.md) .

   

10. Uruchamianie potoku.

### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz moduł szkoleń, a następnie przejdź do karty Output (dane **wyjściowe** ) na prawym panelu. Kliknij ikonę **zarejestruj model**.  Zapisany model można znaleźć jako moduł w drzewie modułów. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 