---
title: 'Wykrywanie anomalii oparte na PCA: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu wykrywania anomalii opartego na pca do utworzenia modelu wykrywania anomalii na podstawie analizy głównego komponentu (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502986"
---
# <a name="pca-based-anomaly-detection"></a>Wykrywanie anomalii oparte na pca

W tym artykule opisano, jak używać **modułu wykrywania anomalii opartego na usłudze PCA** w projektancie usługi Azure Machine Learning (wersja zapoznawcza) w celu utworzenia modelu wykrywania anomalii opartego na analizie składników głównych (PCA).

Ten moduł pomaga tworzyć model w scenariuszach, w których jest łatwe do uzyskania danych szkoleniowych z jednej klasy, takich jak prawidłowe transakcje, ale trudne do uzyskania wystarczających próbek anomalii docelowych. 

Na przykład, aby wykryć oszukańcze transakcje, bardzo często nie masz wystarczającej liczby przykładów oszustw, aby trenować, ale masz wiele przykładów dobrych transakcji. Moduł **wykrywania anomalii oparty na stępce PCA** rozwiązuje problem, analizując dostępne funkcje w celu określenia, co stanowi klasę "normalną", i stosując metryki odległości w celu zidentyfikowania przypadków, które reprezentują anomalie. Dzięki temu można trenować modelu przy użyciu istniejących danych nierówne.

## <a name="more-about-principal-component-analysis"></a>Więcej informacji o analizie głównego komponentu

*Analiza głównego komponentu*, która jest często skracana do PCA, jest ustaloną techniką w uczeniu maszynowym. PCA jest często używany w analizie danych badawczych, ponieważ ujawnia wewnętrzną strukturę danych i wyjaśnia wariancję w danych.

PcA działa poprzez analizowanie danych, które zawierają wiele zmiennych. Wyszukuje korelacje między zmiennymi i określa kombinację wartości, które najlepiej przechwytuje różnice w wynikach. Te połączone wartości operacji są używane do tworzenia bardziej zwartej przestrzeni obiektowej zwanej *głównymi komponentami*.

W przypadku wykrywania anomalii każde nowe dane wejściowe są analizowane, a algorytm wykrywania anomalii oblicza jego projekcję na wektorach własnych wraz ze znormalizowanym błędem rekonstrukcji. Znormalizowany błąd jest używany jako wynik anomalii. Im wyższy błąd, tym bardziej nietypowe jest wystąpienie.

Aby uzyskać więcej informacji na temat działania pca i implementacji wykrywania anomalii, zobacz następujące dokumenty:

- [Randomizowany algorytm do analizy głównego komponentu](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan i Tygert

- [Znajdowanie struktury z losowością: algorytmy probabilistyczne do konstruowania przybliżonych rozkładów macierzy](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (pobieranie w formacie PDF). Halko, Martinsson i Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Jak skonfigurować wykrywanie anomalii PCA

1. Dodaj moduł **wykrywania anomalii oparty na umPR** do potoku w projektancie. Ten moduł można znaleźć w kategorii **Wykrywanie anomalii.**

2. W prawym panelu modułu **wykrywania anomalii opartego na u pca** kliknij opcję **Tryb szkolenia** i wskaż, czy chcesz trenować model przy użyciu określonego zestawu parametrów, czy użyć wyciągnięcia po parametrze, aby znaleźć najlepsze parametry.

    - **Pojedynczy parametr:** Wybierz tę opcję, jeśli wiesz, jak chcesz skonfigurować model, i podaj określony zestaw wartości jako argumenty.

3. **Liczba składników do użycia w ELEMENTÓW PCA**: Określ liczbę operacji wyjściowych lub składników, które mają zostać wyekslizowe.

    Decyzja o tym, ile składników należy uwzględnić, jest ważną częścią projektu eksperymentu przy użyciu TWW. Ogólne wskazówki są takie, że nie należy dołączać takiej samej liczby składników PCA, jak istnieją zmienne. Zamiast tego należy rozpocząć od mniejszej liczby składników i zwiększyć je, dopóki nie zostanie spełnione niektóre kryteria.

    Najlepsze wyniki uzyskuje się, gdy liczba składników wyjściowych jest **mniejsza niż** liczba kolumn elementów dostępnych w zestawie danych.

4. Określ ilość przesadowania do wykonania podczas losowego szkolenia PCA. W przypadku problemów z wykrywaniem anomalii niezrównoważone dane utrudniają stosowanie standardowych technik 10 10 000 PCA. Określając pewną ilość przesamochonia, można zwiększyć liczbę wystąpień docelowych.

    Jeśli określisz 1, nie jest wykonywane przesądzanie. Jeśli określisz dowolną wartość wyższą niż 1, dodatkowe próbki są generowane do użycia w szkoleniu modelu.

    Istnieją dwie opcje, w zależności od tego, czy używasz wyciągnięcia po parametrze, czy nie:

    - **Parametr oversampling dla randomizowanego PCA:** Wpisz pojedynczą liczbę całkowita, która reprezentuje stosunek przesadowania klasy mniejszości w stosunku do klasy normalnej. (Dostępne w przypadku korzystania z metody szkolenia **pojedynczego** parametru.)

    > [!NOTE]
    > Nie można wyświetlić oversampled zestaw danych. Aby uzyskać więcej informacji na temat sposobu nadmiernego przesączanie jest używane z PCA, zobacz [Uwagi techniczne](#technical-notes).

5. **Włącz normalizację funkcji wprowadzania**: Wybierz tę opcję, aby znormalizować wszystkie operacje wejściowe do średniej zero. Normalizacja lub skalowanie do zera jest ogólnie zalecane dla PCA, ponieważ celem PCA jest zmaksymalizowanie wariancji między zmiennymi.

     Ta opcja jest domyślnie wybrana. Usuń zaznaczenie tej opcji, jeśli wartości zostały już znormalizowane przy użyciu innej metody lub skali.

6. Połącz oznakowany zestaw danych szkoleniowych i jeden z modułów szkoleniowych:

    - Jeśli ustawisz opcję **Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model wykrywania anomalii pociągu.](train-anomaly-detection-model.md)

7. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia można zapisać przeszkolony model lub podłączyć go do modułu [Score Model,](score-model.md) aby przewidzieć wyniki anomalii.

Ocena wyników modelu wykrywania anomalii wymaga kilku dodatkowych kroków:

1. Upewnij się, że kolumna wyników jest dostępna w obu zestawach danych

    Jeśli spróbujesz ocenić model wykrywania anomalii i uzyskać błąd, "Nie ma kolumny wynik w zestawie danych ocena do porównania", oznacza to, że używasz typowego zestawu danych oceny, który zawiera kolumnę etykiety, ale nie ma wyników prawdopodobieństwa. Należy wybrać zestaw danych, który pasuje do danych wyjściowych schematu dla modeli wykrywania anomalii, który zawiera **ocenione etykiety** i **ocenione prawdopodobieństwa** kolumny.

2. Upewnij się, że kolumny etykiet są oznaczone

    Czasami metadane skojarzone z kolumną etykiety są usuwane na wykresie potoku. W takim przypadku podczas korzystania z modułu [Oceń model](evaluate-model.md) do porównania wyników dwóch modeli wykrywania anomalii może pojawić się błąd "Nie ma kolumny etykiety w zestawie danychscored" lub "Nie ma kolumny etykiety w zestawie danychscored do porównania".

    Ten błąd można uniknąć, dodając moduł [Edytuj metadane](edit-metadata.md) przed modułem [Oceń model.](evaluate-model.md) Użyj selektora kolumn, aby wybrać kolumnę klasy, a na liście rozwijanej **Pola** wybierz pozycję **Etykieta**.

3. [Użyj skryptu Wykonywanie języka Python,](execute-python-script.md) aby dostosować kategorie kolumn etykiet jako 1 (dodatnie, normalne) i 0 (ujemne, nieprawidłowe).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Uwagi techniczne

Ten algorytm używa PCA do przybliżenia podprzestrzeni zawierającej normalną klasę. Podprzestrzeń jest łączona przez wektory wektorów własnych skojarzone z najwyższymi wartościami własnych macierzy kowariancji danych. Dla każdego nowego wejścia detektor anomalii najpierw oblicza jego projekcję na wektorach własnych, a następnie oblicza błąd rekonstrukcji znormalizowanej. Ten błąd jest wynikiem anomalii. Im wyższy błąd, tym bardziej nietypowe wystąpienie. Szczegółowe informacje na temat obliczania normalnej przestrzeni można znaleźć w wikipedii: [Analiza głównych komponentów](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta (wersja zapoznawcza)](designer-error-codes.md) dla listy błędów specyficznych dla modułów projektanta.''