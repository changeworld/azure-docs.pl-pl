---
title: 'Regresja lasu decyzyjnego: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć model regresji na podstawie kompletu drzew decyzyjnych za pomocą modułu regresja lasu decyzyjnego w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b8bb3285aecb6aff399606e6263f014027a86581
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128889"
---
# <a name="decision-forest-regression-module"></a>Moduł regresji lasu decyzyjnego

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użyj tego modułu, aby utworzyć model regresji na podstawie kompletu drzew decyzyjnych.

Po skonfigurowaniu modelu należy przeprowadzić uczenie modelu przy użyciu zestawu danych z etykietami i modułu [uczenie modelu](./train-model.md) .  Model przeszkolony może być następnie używany do prognozowania. 

## <a name="how-it-works"></a>Jak to działa

Drzewa decyzyjne są modelami nieparametrycznymi, które wykonują sekwencję prostych testów dla każdego wystąpienia, przechodząc do struktury danych drzewa binarnego do momentu osiągnięcia węzła liścia (decyzja).

Drzewa decyzyjne mają następujące zalety:

- Są one wydajne w przypadku obliczeń i wykorzystania pamięci podczas uczenia i przewidywania.

- Mogą reprezentować granice decyzyjne nieliniowe.

- Zapewniają one zintegrowane Wybieranie funkcji i klasyfikację i są odporne na obecność funkcji szumów.

Ten model regresji składa się z kompletów drzew decyzyjnych. Każde drzewo w lesie z decyzją regresji wyprowadza rozkład gaussowskie jako prognozowanie. Agregacja jest wykonywana w ramach zestawu drzew, aby znaleźć rozkład gaussowskie najbliżej połączonej dystrybucji dla wszystkich drzew w modelu.

Aby uzyskać więcej informacji na temat teoretycznej struktury tego algorytmu i jego implementacji, zobacz ten artykuł: [Lasy decyzyjne: Ujednolicona platforma do klasyfikowania, regresji, oszacowania gęstości, uczenie kolektora i uczenie częściowo nadzorowane](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Konfigurowanie modelu regresji lasu decyzyjnego

1. Dodaj do eksperymentu moduł **regresji lasu** . Moduł można znaleźć w interfejsie w obszarze **Machine Learning**, **zainicjuj model**i **regresja**.

2. Otwórz właściwości modułu i dla **metody ponownej próbkowania**wybierz metodę użytą do utworzenia poszczególnych drzew.  Możesz wybrać jedną z opcji Working lub **replikacja**.

    - **Working**: Working jest również nazywane *agregacją Bootstrap*. Każde drzewo w lesie z decyzją regresji wyprowadza rozkład gaussowskie w drodze przewidywania. Agregacją jest znalezienie gaussowskie, którego pierwsze dwa momenty są zgodne z chwilą mieszaniny gaussowskie podaną przez połączenie wszystkich gaussowskie zwracanych przez poszczególne drzewa.

         Aby uzyskać więcej informacji, zobacz wpis Wikipedia na potrzeby [agregowania Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikacja**: W replikacji Każde drzewo jest przeszkolone dokładnie na te same dane wejściowe. Określenie, który predykat Split jest używany dla każdego węzła drzewa, pozostaje losowy, a drzewa będą różne.

         Aby uzyskać więcej informacji na temat procesu szkolenia z opcją **replikacja** , zobacz [lasy decyzyjne dla przetwarzanie obrazów i analizy obrazu medycznego. Criminisi i J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

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

   

10. Uruchom eksperyment.

### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zobaczyć drzewo, które zostało utworzone w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe modułu szkoleń i wybierz polecenie **Wizualizuj**.

+ Aby wyświetlić reguły dla każdego węzła, kliknij każde drzewo i przejdź do szczegółów.

+ Aby zapisać migawkę przeszkolonego modelu, kliknij prawym przyciskiem myszy dane wyjściowe modułu szkoleń i wybierz polecenie **Zapisz jako przeszkolony model**. Ta kopia modelu nie jest aktualizowana po kolejnych uruchomieniach eksperymentu. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 