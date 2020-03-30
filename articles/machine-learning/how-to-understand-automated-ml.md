---
title: Zrozumienie zautomatyzowanych wyników ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i rozumieć wykresy i metryki dla każdego z automatycznych przebiegów uczenia maszynowego.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283461"
---
# <a name="understand-automated-machine-learning-results"></a>Opis wyników zautomatyzowanego uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyświetlić i zrozumieć wykresy i metryki dla każdego z automatycznych uruchomień uczenia maszynowego. 

Dowiedz się więcej o usługach:
+ [Metryki, wykresy i krzywe dla modeli klasyfikacji](#classification)
+ [Metryki, wykresy i wykresy dla modeli regresji](#regression)
+ [Możliwość interpretacji modelu i znaczenie funkcji](#explain-model)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Utwórz eksperyment dla automatycznego uruchamiania uczenia maszynowego za pomocą sdk lub w studio usługi Azure Machine Learning.

    * Tworzenie [modelu klasyfikacji](how-to-auto-train-remote.md) lub [modelu regresji](tutorial-auto-train-models.md) za pomocą sdk
    * Użyj [usługi Azure Machine Learning studio,](how-to-use-automated-ml-for-ml-models.md) aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-the-run"></a>Zobacz bieg

Po uruchomieniu eksperymentu automatycznego uczenia maszynowego historię przebiegów można znaleźć w obszarze roboczym uczenia maszynowego. 

1. Przejdź do swojego obszaru roboczego.

1. W lewym panelu obszaru roboczego wybierz pozycję **Eksperymenty**.

   ![Zrzut ekranu przedstawiający menu eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na liście eksperymentów wybierz eksperymenty, które chcesz eksplorować.

   [![Lista eksperymentów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. W dolnej tabeli wybierz **pozycję Uruchom**.

   Przebieg eksperymentu ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. W models wybierz **nazwę algorytmu** dla modelu, który chcesz zbadać dalej.

   [![Model eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Te same wyniki są również widoczne podczas `RunDetails`biegu podczas korzystania z [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Wyniki klasyfikacji

Poniższe metryki i wykresy są dostępne dla każdego modelu klasyfikacji, który tworzysz przy użyciu automatycznych funkcji uczenia maszynowego usługi Azure Machine Learning

+ [Metryki](#classification-metrics)
+ [Macierz zamieszania](#confusion-matrix)
+ [Wykres precyzyjnego wycofywania](#precision-recall-chart)
+ [Charakterystyka działania odbiornika (lub ROC)](#roc)
+ [Krzywa podnoszenia](#lift-curve)
+ [Krzywa zysków](#gains-curve)
+ [Wykres kalibracji](#calibration-plot)

### <a name="classification-metrics"></a>Metryki klasyfikacji

Następujące metryki są zapisywane w każdej iteracji uruchamiania dla zadania klasyfikacji.

Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--
AUC_Macro| AUC jest obszarem pod krzywą charakterystyki charakterystycznej dla odbiorcy. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | średnia ="makro"|
AUC_Micro| AUC jest obszarem pod krzywą charakterystyki charakterystycznej dla odbiorcy. Micro jest obliczany globalnie przez połączenie prawdziwych alarmów i fałszywych alarmów z każdej klasy.| [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | średnia ="mikro"|
AUC_Weighted  | AUC jest obszarem pod krzywą charakterystyki charakterystycznej dla odbiorcy. Ważona jest średnia arytmetyczna wynik dla każdej klasy, ważona przez liczbę wystąpień true w każdej klasie.| [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|średnia ="ważona"
accuracy|Dokładność to procent przewidywanych etykiet, które dokładnie odpowiadają prawdziwym etykietom. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia precyzja podsumowuje krzywą precyzyjnego przypomnienia jako średnią ważoną dokładności osiągniętą przy każdym progu, przy czym wzrost wycofywania z poprzedniego progu jest używany jako waga. Makro jest średnią arytmetyczną średniej precyzji każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|średnia ="makro"|
average_precision_score_micro|Średnia precyzja podsumowuje krzywą precyzyjnego przypomnienia jako średnią ważoną dokładności osiągniętą przy każdym progu, przy czym wzrost wycofywania z poprzedniego progu jest używany jako waga. Micro jest obliczany globalnie przez połączenie prawdziwych pozytywów i fałszywych alarmów przy każdym odcięciu.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|średnia ="mikro"|
average_precision_score_weighted|Średnia precyzja podsumowuje krzywą precyzyjnego przypomnienia jako średnią ważoną dokładności osiągniętą przy każdym progu, przy czym wzrost wycofywania z poprzedniego progu jest używany jako waga. Ważona jest średnią arytmetyczną średniej precyzji dla każdej klasy, ważona przez liczbę wystąpień rzeczywistych w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|średnia ="ważona"|
balanced_accuracy|Zrównoważona dokładność jest średnią arytmetyczną odwołania dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|średnia ="makro"|
f1_score_macro|Wynik F1 jest harmonicznym pod względem precyzji i przywołania. Makro jest średnią arytmetyczną wyniku F1 dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|średnia ="makro"|
f1_score_micro|Wynik F1 jest harmonicznym pod względem precyzji i przywołania. Micro jest obliczany globalnie przez zliczanie całkowitej liczby prawdziwych wartości dodatnich, fałszywych negatywów i fałszywych alarmów.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|średnia ="mikro"|
f1_score_weighted|Wynik F1 jest harmonicznym pod względem precyzji i przywołania. Średnia ważona według częstotliwości klasy wyniku F1 dla każdej klasy|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|średnia ="ważona"|
log_loss|Jest to funkcja utraty używana w (wielonomiastowej) regresji logistycznej i rozszerzeniach, takich jak sieci neuronowe, zdefiniowana jako ujemne prawdopodobieństwo logowania prawdziwych etykiet, biorąc pod uwagę prognozy klasyfikatora probabilistycznego. Dla pojedynczej próbki z prawdziwą {0,1} etykietą yt in i szacowanym prawdopodobieństwem yp, które yt = 1, utrata dziennika to -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Znormalizowane makro Przywołanie jest makro Przypomnieć znormalizowane tak, że losowa wydajność ma wynik 0 i doskonała wydajność ma wynik 1. Osiąga się to poprzez norm_macro_recall := (recall_score_macro - R)/(1 - R), gdzie R jest oczekiwaną wartością recall_score_macro dla prognoz losowych (tj. R=0,5 dla klasyfikacji binarnej i R=(1/C) dla problemów z klasyfikacją klasy C).|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|średnia = "makro" |
precision_score_macro|Dokładność jest procentem pozytywnie przewidywanych elementów, które są poprawnie oznaczone. Makro jest arytmetycznym średnią precyzji dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|średnia ="makro"|
precision_score_micro|Dokładność jest procentem pozytywnie przewidywanych elementów, które są poprawnie oznaczone. Micro jest obliczany globalnie przez zliczanie całkowitej liczby prawdziwych alarmów i fałszywych alarmów.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|średnia ="mikro"|
precision_score_weighted|Dokładność jest procentem pozytywnie przewidywanych elementów, które są poprawnie oznaczone. Ważona jest arytmetyczną średnią precyzji dla każdej klasy, ważoną liczbą wystąpień true w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|średnia ="ważona"|
recall_score_macro|Odwołanie jest procent poprawnie oznaczone elementy określonej klasy. Makro jest średnią arytmetyczną odwołania dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|średnia ="makro"|
recall_score_micro|Odwołanie jest procent poprawnie oznaczone elementy określonej klasy. Micro jest obliczany globalnie przez zliczanie całkowitej liczby prawdziwych alarmów, fałszywych negatywów i fałszywych alarmów|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|średnia ="mikro"|
recall_score_weighted|Odwołanie jest procent poprawnie oznaczone elementy określonej klasy. Ważona jest średnią arytmetyczną odwołania dla każdej klasy, ważona przez liczbę wystąpień true w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|średnia ="ważona"|
weighted_accuracy|Dokładność ważona to dokładność, w której waga podana do każdego przykładu jest równa proporcji prawdziwych wystąpień w prawdziwej klasie tego przykładu.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równym proporcji tej klasy dla każdego elementu w docelowym|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Macierz zamieszania

#### <a name="what-is-a-confusion-matrix"></a>Co to jest matryca zamieszania?
Macierz pomyłek jest używana do opisywania wydajności modelu klasyfikacji. Każdy wiersz wyświetla wystąpienia true lub rzeczywistej klasy w zestawie danych, a każda kolumna reprezentuje wystąpienia klasy, która została przewidziana przez model. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Co zautomatyzowane ML zrobić z matrycy zamieszania?
W przypadku problemów z klasyfikacją usługa Azure Machine Learning automatycznie udostępnia macierz nieporozumień dla każdego zbudowanego modelu. Dla każdej macierzy pomyłek automatyczna ml pokaże częstotliwość każdej przewidywanej etykiety (kolumny) w porównaniu z prawdziwą etykietą (wiersz). Im ciemniejszy kolor, tym wyższa liczba w tej konkretnej części macierzy. 

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Porównujemy rzeczywistą wartość zestawu danych z przewidywanymi wartościami, które podał model. Z tego powodu modele uczenia maszynowego mają większą dokładność, jeśli model ma większość swoich wartości wzdłuż przekątnej, co oznacza, że model przewidział poprawną wartość. Jeśli model ma nierównowagę klasy, macierz pomyłek pomoże wykryć tendencyjny model.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Przykład 1: Model klasyfikacji o niskiej dokładności
![Model klasyfikacji o niskiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Przykład 2: Model klasyfikacji z wysoką dokładnością 
![Model klasyfikacji z wysoką dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Przykład 3: Model klasyfikacji z wysoką dokładnością i wysoką stronniczością w prognozach modelu
![Model klasyfikacji z wysoką dokładnością i wysoką stronniczością w prognozach modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Wykres precyzyjnego wycofywania
#### <a name="what-is-a-precision-recall-chart"></a>Co to jest wykres precyzyjnego wycofywania?
Krzywa precyzyjnego przypomnienia pokazuje relację między precyzją a odwołaniem z modelu. Termin precyzja reprezentuje tę możliwość dla modelu do etykiety wszystkich wystąpień poprawnie. Odwołanie reprezentuje możliwość klasyfikatora, aby znaleźć wszystkie wystąpienia określonej etykiety.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Co zautomatyzowane ml zrobić z wykresu precyzyjnego przypomnienia?

Za pomocą tego wykresu można porównać krzywe precyzyjnego przypomnienia dla każdego modelu, aby określić, który model ma akceptowalną relację między precyzją a odwołaniem dla danego problemu biznesowego. Na tym wykresie przedstawiono makro średniej precyzji przypomnieć, Mikro średnia precyzja przypomnieć i precyzyjnewywołanie skojarzone ze wszystkimi klasami dla modelu. 

Makro-średnia obliczy metrykę niezależnie od każdej klasy, a następnie wziąć średnią, traktując wszystkie klasy jednakowo. Jednak mikro-średnia będzie agregować składki wszystkich klas do obliczenia średniej. Mikro-średnia jest korzystnie, jeśli istnieje nierównowaga klasy występuje w zestawie danych.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
W zależności od celu problemu biznesowego, idealna krzywa precyzyjnego wycofywania może się różnić. Poniżej podano kilka przykładów

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Przykład 1: Model klasyfikacji o niskiej precyzji i niskim odwołaniu
![Model klasyfikacji o niskiej precyzji i niskim przywoływaniu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Przykład 2: Model klasyfikacji z ~100% precyzją i ~100% przypomnieć 
![Model klasyfikacji wysoka precyzja i przywołanie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Wykres ROC

#### <a name="what-is-a-roc-chart"></a>Co to jest wykres ROC?
Charakterystyka pracy odbiornika (lub ROC) jest wykresem prawidłowo sklasyfikowanych etykiet w porównaniu z nieprawidłowo sklasyfikowanymi etykietami dla danego modelu. Krzywa ROC może być mniej pouczająca, gdy modele szkoleniowe na zestawach danych o wysokiej odchyleniu, ponieważ nie będą wyświetlane etykiety fałszywie dodatnie.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Co zautomatyzowane ML zrobić z wykresu ROC?
Automatyczne ML generuje makro średniej precyzji przypomnieć, Micro Średnia precyzja przypomnieć i precyzyjnewywołanie skojarzone ze wszystkimi klasami dla modelu. 

Makro-średnia obliczy metrykę niezależnie od każdej klasy, a następnie wziąć średnią, traktując wszystkie klasy jednakowo. Jednak mikro-średnia będzie agregować składki wszystkich klas do obliczenia średniej. Mikro-średnia jest korzystnie, jeśli istnieje nierównowaga klasy występuje w zestawie danych.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Idealnie, model będzie miał bliżej 100% true dodatniej stopy i bliżej 0% fałszywie dodatnie stawki. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Przykład 1: Model klasyfikacji z niskimi etykietami true i wysokimi fałszywymi etykietami
![Model klasyfikacji z niskimi etykietami true i wysokimi fałszywymi etykietami](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Przykład 2: Model klasyfikacji z wysokimi etykietami true i niskimi fałszywymi etykietami
![model klasyfikacji z wysokimi etykietami true i niskimi fałszywymi etykietami](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Wykres podnoszenia
#### <a name="what-is-a-lift-chart"></a>Co to jest wykres windy?
Wykresy dźwigu są używane do oceny wydajności modelu klasyfikacji. Pokazuje, o ile lepiej można oczekiwać, aby zrobić z wygenerowanego modelu w porównaniu do bez modelu pod względem dokładności.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Co zautomatyzowane ml zrobić z wykresu windy?
Można porównać windy modelu utworzonego automatycznie z usługi Azure Machine Learning do linii bazowej w celu wyświetlenia przyrostu wartości tego konkretnego modelu.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Przykład 1: Model klasyfikacji, który wypada gorzej niż model losowego wyboru
![Model klasyfikacji, który wypada gorzej niż model losowego wyboru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Przykład 2: Model klasyfikacji, który działa lepiej niż model losowego wyboru
![Model klasyfikacji, który działa lepiej](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Wykres zysków
#### <a name="what-is-a-gains-chart"></a>Co to jest wykres zysków?

Wykres zysków ocenia wydajność modelu klasyfikacji przez każdą część danych. Pokazuje dla każdego percentyla zestawu danych, o ile lepiej można oczekiwać, aby wykonać w porównaniu z modelu losowego wyboru.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Co zautomatyzowane ML zrobić z wykresu zysków?
Użyj skumulowanego wykresu zysków, aby ułatwić wybór odcięcia klasyfikacji przy użyciu wartości procentowej odpowiadającej żądanemu zyskowi z modelu. Informacje te zapewniają inny sposób patrzenia na wyniki na załączonym wykresie dźwigu.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Przykład 1: Model klasyfikacji z minimalnym wzmocnieniem
![model klasyfikacji z minimalnym wzmocnieniem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Przykład 2: Model klasyfikacji ze znacznym wzmocnieniem
![Model klasyfikacji ze znacznym zyskiem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Wykres kalibracji

#### <a name="what-is-a-calibration-chart"></a>Co to jest wykres kalibracji?
Wykres kalibracji służy do wyświetlania ufności modelu predykcyjnego. Czyni to, pokazując relację między przewidywane prawdopodobieństwo i rzeczywiste prawdopodobieństwo, gdzie "prawdopodobieństwo" reprezentuje prawdopodobieństwo, że określone wystąpienie należy pod pewną etykietą.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Co zautomatyzowane ml zrobić z wykresu kalibracji?
W przypadku wszystkich problemów z klasyfikacją można przejrzeć linię kalibracji dla mikroprzeciwowej, średniej makro i każdej klasy w danym modelu predykcyjnym.

Makro-średnia obliczy metrykę niezależnie od każdej klasy, a następnie wziąć średnią, traktując wszystkie klasy jednakowo. Jednak mikro-średnia będzie agregować składki wszystkich klas do obliczenia średniej. 
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
 Dobrze skalibrowany model jest wyrównany z linią y=x, gdzie jest dość pewny swoich prognoz. Zbyt pewny siebie model jest wyrównany z wierszem y=0, w którym występuje przewidywane prawdopodobieństwo, ale nie ma rzeczywistego prawdopodobieństwa. 


##### <a name="example-1-a-well-calibrated-model"></a>Przykład 1: Dobrze skalibrowany model
![ bardziej dobrze skalibrowany model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Przykład 2: Model zbyt pewny siebie
![Zbyt pewny siebie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Wyniki regresji

Poniższe metryki i wykresy są dostępne dla każdego modelu regresji, który tworzysz przy użyciu automatycznych funkcji uczenia maszynowego usługi Azure Machine Learning

+ [Metryki](#reg-metrics)
+ [Przewidywane vs. True](#pvt)
+ [Histogram pozostałości](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Metryki regresji

Następujące metryki są zapisywane w każdej iteracji uruchamiania dla zadania regresji lub prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wariancja wyjaśniona jest proporcją, do której model matematyczny uwzględnia odmianę danego zestawu danych. Jest to procentowy spadek wariancji oryginalnych danych do odchylenia błędów. Gdy średnia błędów wynosi 0, jest równa wyjaśnionej wariancji.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R2 jest współczynnikiem oznaczania lub procentowej redukcji błędów kwadratowych w porównaniu z modelem bazowym, który wyprowadza średnią. |[Obliczenia](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja spearmana jest nieparametryczną miarą monotonności relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacji Spearman nie zakłada, że oba zestawy danych są zwykle rozproszone. Podobnie jak inne współczynniki korelacji, ten waha się między -1 i +1 z 0 oznacza brak korelacji. Korelacje -1 lub +1 oznaczają dokładną relację monotoniczną. Pozytywne korelacje sugerują, że wraz ze wzrostem x, tak samo jak y. Negatywne korelacje oznaczają, że wraz ze wzrostem x zmniejsza się y.|[Obliczenia](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Średni błąd bezwzględny jest oczekiwaną wartością wartości bezwzględnej różnicy między celem a przewidywaniem|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany średni błąd bezwzględny oznacza błąd bezwzględny podzielony przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dzielenie według zakresu danych|
median_absolute_error|Mediana błędu bezwzględnego jest medianą wszystkich różnic bezwzględnych między obiektem docelowym a przewidywaniem. Ta strata jest odporna na odstające.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowana mediana błędu bezwzględnego to mediana błędu bezwzględnego podzielona przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dzielenie według zakresu danych|
root_mean_squared_error|Główny średni kwadratowy błąd jest pierwiastek kwadratowy oczekiwanej kwadratowej różnicy między celem a przewidywaniem|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowany błąd do kwadratu jest głównym średnia kwadratu błąd podzielony przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dzielenie według zakresu danych|
root_mean_squared_log_error|Główny średnia kwadratowy błąd dziennika jest pierwiastek kwadratowy oczekiwanego kwadratu logarytmicznego błędu|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowany główny średnia kwadratowy błąd dziennika jest głównym średnia kwadratowy błąd dziennika podzielone przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dzielenie według zakresu danych|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Przewidywany wykres a prawdziwy
#### <a name="what-is-a-predicted-vs-true-chart"></a>Co to jest wykres Przewidywany vs. Prawda?
Przewidywane vs True pokazuje relację między przewidywaną wartością a jej korelacją wartości true dla problemu regresji. Ten wykres może służyć do pomiaru wydajności modelu, jak bliżej linii y = x przewidywane wartości są, tym lepsza dokładność modelu predykcyjnego.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Co zautomatyzowane ml zrobić z przewidywane vs. True wykresu?
Po każdym uruchomieniu można zobaczyć przewidywany wykres vs. true dla każdego modelu regresji. Aby chronić prywatność danych, wartości są łączone razem, a rozmiar każdego pojemnika jest wyświetlany jako wykres słupkowy w dolnej części obszaru wykresu. Można porównać model predykcyjny z jaśniejszym obszarem cienia przedstawiającym marginesy błędu z idealną wartością, w której powinien znajdować się model.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Przykład 1: Model klasyfikacji o niskiej dokładności
![Model regresji z niską dokładnością w prognozach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Przykład 2: Model regresji z wysoką dokładnością 
[![Model regresji z dużą dokładnością w swoich przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Histogram wykresu pozostałości
#### <a name="what-is-a-residuals-chart"></a>Co to jest wykres resztkowy?
Rezydual reprezentuje obserwowane y – przewidywane y. Aby pokazać margines błędu przy niskim odchyleniu, histogram resztk powinien być kształtowany jako krzywa dzwonu, wyśrodkowany wokół 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Co zautomatyzowane ml zrobić z wykresu resztkowego?
Automatyczne ML automatycznie udostępnia wykres pozostałości, aby pokazać rozkład błędów w prognozach.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Dobry model zazwyczaj ma krzywą dzwonka lub błędy wokół zera.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Przykład 1: Model regresji z błędami
![Model regresji SA z odchyleniami w błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Przykład 2: Model regresji z bardziej równomiernym rozkładem błędów
![Model regresji z bardziej równomiernym rozkładem błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Możliwość interpretacji modelu i znaczenie funkcji
Automatyczna ml zapewnia pulpit nawigacyjny interpretability uczenia maszynowego dla swoich przebiegów.
Aby uzyskać więcej informacji na temat włączania funkcji interpretalności, zobacz [instrukcje](how-to-machine-learning-interpretability-automl.md) dotyczące włączania interpretowania w zautomatyzowanych eksperymentach uczenia się.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w usłudze Azure Machine Learning.
+ Wypróbuj przykładowe notesy [przykładowe objaśnienia modelu automatycznego uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
