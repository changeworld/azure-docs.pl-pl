---
title: Zrozumienie zautomatyzowanych wyników ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i zrozumieć wykresy oraz metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129686"
---
# <a name="understand-automated-machine-learning-results"></a>Informacje o zautomatyzowanych wynikach uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyświetlać i zrozumieć wykresy i metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego. 

Dowiedz się więcej o usługach:
+ [Metryki, wykresy i krzywe dla modeli klasyfikacji](#classification)
+ [Metryki, wykresy i wykresy dla modeli regresji](#regression)
+ [Interpretacja modelu i ważność funkcji](#explain-model)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Utwórz eksperyment dla automatycznego przebiegu uczenia maszynowego z zestawem SDK lub w programie Azure Machine Learning Studio.

    * Użyj zestawu SDK, aby utworzyć [model klasyfikacji](how-to-auto-train-remote.md) lub [model regresji](tutorial-auto-train-models.md)
    * Użyj [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) , aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-the-run"></a>Wyświetlanie przebiegu

Po uruchomieniu zautomatyzowanego eksperymentu w usłudze Machine Learning historia przebiegów jest dostępna w obszarze roboczym uczenia maszynowego. 

1. Przejdź do swojego obszaru roboczego.

1. W lewym panelu obszaru roboczego wybierz pozycję **eksperymenty**.

   ![Zrzut ekranu przedstawiający menu eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na liście eksperymentów wybierz ten, który chcesz poznać.

   [Lista eksperymentów ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. W dolnej tabeli wybierz **przebieg**.

   [![uruchomienie eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. W obszarze modele wybierz **nazwę algorytmu** dla modelu, który chcesz szczegółowo zbadać.

   [Model eksperymentu ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Te same wyniki są również widoczne podczas uruchamiania, gdy używasz [widżetu `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Wyniki klasyfikacji

Thee następujące metryki i wykresy są dostępne dla każdego modelu klasyfikacji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#classification-metrics)
+ [Zamieszanie macierzy](#confusion-matrix)
+ [Wykres dokładności do odzyskania](#precision-recall-chart)
+ [Charakterystyki operacyjne odbiornika (lub ROC)](#roc)
+ [Krzywa podnoszenia](#lift-curve)
+ [Krzywa zysków](#gains-curve)
+ [Wykres kalibracyjny](#calibration-plot)

### <a name="classification-metrics"></a>Metryki klasyfikacji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania klasyfikacji.

Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--
AUC_Macro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "makro"|
AUC_Micro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie z każdej klasy.| [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "micro"|
AUC_Weighted  | AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Ważone to średnia arytmetyczna wyniku dla każdej klasy, ważona przez liczbę wystąpień prawdziwych w każdej klasie.| [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średni = "ważona"
accuracy|Dokładność jest procent przewidywane etykiety, które dokładnie pasują do etykiety wartość true. |[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Makro jest średnią arytmetyczną średniego wyniku dokładności dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "makro"|
average_precision_score_micro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie dla każdego odcięcia.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "micro"|
average_precision_score_weighted|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Ważone jest średnią arytmetyczną średniego oceny dokładności dla każdej klasy, ważonej przez liczbę wystąpień prawdziwych w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "ważona"|
balanced_accuracy|Dokładność o zrównoważonym obciążeniu jest średnią arytmetyczną odwołania dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
f1_score_macro|F1 wynik jest harmoniczna dokładności i odwołania. Makro jest średnią arytmetyczną wyniku F1 dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "makro"|
f1_score_micro|F1 wynik jest harmoniczna dokładności i odwołania. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "micro"|
f1_score_weighted|F1 wynik jest harmoniczna dokładności i odwołania. Średnia ważona przez klasy częstotliwość oceny F1 dla każdej klasy|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "ważona"|
log_loss|Jest to funkcja strat użyta w (MULTINOMIAL) logistyki i rozszerzenia, takie jak sieci neuronowych, zdefiniowane jako negatywna prawdopodobieństwo rejestrowania dla prawdziwych etykiet z przewidywaniami klasyfikatora probabilistyczne. Dla pojedynczego przykładu o prawdziwej etykiecie yt w {0,1} i szacowanego prawdopodobieństwa YP yt = 1, utrata dziennika to-log P (&#124;yt YP) =-(YT log (YP) + (1-yt) log (1-YP)).|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Odwołaj znormalizowane — makro jest makro odwołania znormalizowane, dzięki czemu losowe wydajności ma wynik 0 i doskonałe wydajności ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro-R)/(1-R), gdzie R jest oczekiwaną wartością recall_score_macro dla prognoz losowych (tj. R = 0,5 dla klasyfikacji binarnej i języka R = (1/C) dla błędów klasyfikacji klasy C).|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Makro jest arytmetyczną dokładnością dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "makro"|
precision_score_micro|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Mikro jest obliczany globalnie przez obliczenie całkowitej liczby pozytywnych dodatnich i fałszywych wartości dodatnich.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "micro"|
precision_score_weighted|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Ważone jest średnią arytmetyczną dokładności dla każdej klasy, ważonej według liczby prawdziwych wystąpień w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "ważona"|
recall_score_macro|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Makro jest średnią arytmetyczną operacji odwoływania dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
recall_score_micro|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych wartości dodatnich|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "micro"|
recall_score_weighted|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Ważone to średnia arytmetyczna operacji odwoływania dla każdej klasy, ważona według liczby prawdziwych wystąpień w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "ważona"|
weighted_accuracy|Waga ważona jest dokładnością, gdzie waga określona dla każdego przykładu jest równa proporcji prawdziwe wystąpienia w tym przykładzie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równą część tej klasy, dla każdego elementu w elemencie docelowym|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Macierz pomyłek

#### <a name="what-is-a-confusion-matrix"></a>Co to jest macierz niepomylena?
Macierz pomyłek służy do opisywania wydajność model klasyfikacji. Każdy wiersz zawiera wystąpienia prawdy lub rzeczywistej klasy w zestawie danych, a każda kolumna reprezentuje wystąpienia klasy, które zostały przewidywalne przez model. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Co robią automatyczne znaczenie w odróżnieniu od macierzy?
W przypadku problemów z klasyfikacji usługi Azure Machine Learning automatycznie zapewnia macierz pomyłek dla każdego modelu, który jest skompilowany. Dla każdej matrycy nieistotnej, zautomatyzowany ML będzie wyświetlał częstotliwość każdej tabeli przewidywanej (kolumny) w porównaniu z rzeczywistą etykietą (wiersz). Ciemniejszy kolor, im wyższy licznik w danej części macierzy. 

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Porównujemy rzeczywistą wartość zestawu danych z przewidywanymi wartościami, które otrzymał model. Z tego powodu modele uczenia maszynowego mają wyższą dokładność, jeśli model ma większość wartości na przekątnej, co oznacza, że model przewiduje poprawną wartość. Jeśli model ma niezrównoważoną klasę, macierz niezauważalna pomoże wykryć model z obciążeniem.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Przykład 1: model klasyfikacji z niską dokładnością
![Model klasyfikacji z niską dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Przykład 2: model klasyfikacji o wysokiej dokładności 
![Model klasyfikacji o wysokiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Przykład 3: model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu
![Model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precision-recall wykresu
#### <a name="what-is-a-precision-recall-chart"></a>Co to jest wykres z dokładnoocią dokładności?
Krzywa odwołań dokładności przedstawia relację między dokładnością i odwołaniem z modelu. Termin dokładność reprezentuje możliwość poprawnego etykietowania wszystkich wystąpień przez model. Odwołania reprezentuje możliwości klasyfikatora znaleźć wszystkie wystąpienia określonej etykiecie.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Co robią automatyczne ML z wykresem dokładności do odzyskania?

Z tego wykresu można porównać krzywych precision-recall dla każdego modelu określić model, który ma akceptowalnego relacja dokładności i odwołania dla danego problemu określonego rodzaju. Ten wykres przedstawia średnią Precision-Recall — makro, Micro średni Precision-Recall i dokładności recall skojarzone z wszystkich klas dla modelu. 

Średnie makro obliczy metrykę niezależnie od klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak funkcja Micro Average agreguje wkłady wszystkich klas, aby obliczyć średnią. Większość średniej jest preferowana, jeśli istnieje nierównoważność klasy w zestawie danych.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
W zależności od celu problemu biznesowego, idealna krzywa odwołań może się różnić. Poniżej przedstawiono kilka przykładów

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Przykład 1: model klasyfikacji z małą dokładnością i niskim wycofywaniem
![Model klasyfikacji z małą dokładnością i niskim odwołaniem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Przykład 2: model klasyfikacji o 100% dokładności i ~ 100% odwoływania 
![model klasyfikacji o wysokiej precyzji i odwołaniu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Wykres ROC

#### <a name="what-is-a-roc-chart"></a>Co to jest wykres ROC?
Odbiornik operacyjnego cechy (lub ROC) jest wykres poprawnie sklasyfikowane etykiety, a niepoprawnie sklasyfikowane etykiety dla określonego modelu. Może być mniej informacyjne krzywej ROC, gdy szkolenie modeli w zestawach danych przy użyciu wysokie odchylenie, ponieważ nie będą widoczne fałszywie dodatnie etykiety.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Co robią automatyczne ML przy użyciu wykresu ROC?
Funkcja zautomatyzowanej sieci generuje średnią precyzję makra — odwołanie, średnią precyzję i odwołanie do zestawu, które są skojarzone ze wszystkimi klasami modelu. 

Średnie makro obliczy metrykę niezależnie od klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak funkcja Micro Average agreguje wkłady wszystkich klas, aby obliczyć średnią. Większość średniej jest preferowana, jeśli istnieje nierównoważność klasy w zestawie danych.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
W idealnym przypadku model będzie miał wartość zbliżoną do 100% true dodatnią i zbliżoną do 0% fałszywych dodatniej. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Przykład 1: model klasyfikacji z niską rzeczywistą etykietami i silnymi etykietami fałszywymi
![Model klasyfikacji z niskimi etykietami o niskiej wartości i fałszywych etykiet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Przykład 2: model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywych
![model klasyfikacji przy użyciu wysokich etykiet i etykiet o niskiej wartości false](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Wykres przyrostu
#### <a name="what-is-a-lift-chart"></a>Co to jest wykres przyrostu?
Wykresy przyrostu służą do oceny wydajności model klasyfikacji. Pokazuje, ile lepiej można wykonać z wygenerowanym modelem w porównaniu z niemodelem pod względem dokładności.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Co robią automatyczne ML z wykresem przyrostowym?
Możesz porównać przyrostu modelu tworzone automatycznie za pomocą usługi Azure Machine Learning do linii bazowej Aby wyświetlić zysk wartość określonego modelu.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Przykład 1: model klasyfikacji, który jest niezgodny z losowym modelem wyboru
![Model klasyfikacji, który jest niezgodny z losowym modelem wyboru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Przykład 2: model klasyfikacji, który wykonuje lepszy niż losowy model wyboru
![model klasyfikacji, który wykonuje lepsze](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Wykres zysków
#### <a name="what-is-a-gains-chart"></a>Co to jest wykres zysków?

Wykres zyski ocenia wydajność model klasyfikacji przez każdy fragment danych. Pokazuje dla każdego percentylu zestawu danych, ile lepiej można oczekiwać, że do wykonania porównana z modelem losowej.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Co robią automatyczne ML z wykresem zysków?
Użyj wykresu skumulowanego zyski ułatwiające wybór odcięcia Klasyfikacja za pomocą wartość procentową, która odnosi się do żądanego korzyści z modelu. Informacje te stanowią innym sposobem spojrzenie na wyniki na wykresie przyrostu towarzyszącej.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Przykład 1: model klasyfikacji z minimalnym wzrostem
![model klasyfikacji z minimalnym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Przykład 2: model klasyfikacji z znaczącym wzrostem
![model klasyfikacji z znaczącym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Wykres kalibracyjny

#### <a name="what-is-a-calibration-chart"></a>Co to jest wykres kalibracyjny?
Wykres odwzorowania służy do wyświetlania zaufania modelu predykcyjnego. Robi to, pokazując zależność między przewidywanym prawdopodobieństwem a rzeczywistym prawdopodobieństwem, gdzie "prawdopodobieństwo" oznacza prawdopodobieństwo, że określone wystąpienie należy do pewnej etykiety.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Co robią automatyczne ML z wykresem kalibracyjnym?
W przypadku wszystkich problemów klasyfikacji możesz przejrzeć odwzorowania linii średniej micro, średnia — makro i każda klasa w danym modelu predykcyjnego.

Średnie makro obliczy metrykę niezależnie od klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak funkcja Micro Average agreguje wkłady wszystkich klas, aby obliczyć średnią. 
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
 Dobrze skalibrowane modele są wyrównane z linią y = x, gdzie w jej przewidywaniach jest to uzasadnione. Model nadmiernego confident wyrównane y = 0 wiersza, w którym przewidywany prawdopodobieństwo, że jest zainstalowany, ale istnieje nie rzeczywiste prawdopodobieństwo. 


##### <a name="example-1-a-well-calibrated-model"></a>Przykład 1: model dobrze kalibrowany
![ bardziej dobrze skalibrowane modele](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Przykład 2: model o nadmiernej pewności
![Model o nadmiernej pewności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Wyniki regresji

Thee następujące metryki i wykresy są dostępne dla każdego modelu regresji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#reg-metrics)
+ [Przewidywany a prawdziwy](#pvt)
+ [Histogram reszty](#histo)


### <a name="reg-metrics"></a>Metryki regresji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania regresji lub prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wariancja wyjaśniono to modelu matematycznego kont odmiany danego zestawu danych. Jest to procent spadek wariancji oryginalne dane do wariancję błędy. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 to określenie lub procent redukcji kwadratów błędów w porównaniu z modelem linii bazowej, który wyprowadza średniej. |[Podstaw](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Korelacja metodą Spearman jest miarą nonparametric monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacji metodą Spearman nie zakłada, że oba zestawy danych zwykle są rozpowszechniane. Podobnie jak inne współczynniki korelacji ta różni się od -1 do + 1 od 0, co oznacza brak korelacji. Korelacji -1 lub + 1 oznacza dokładne monotoniczny relacji. Dodatnia korelacji oznacza, że x rosną, więc nie y. Korelacji ujemna oznacza, że x rosną, y zmniejsza.|[Podstaw](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Oznacza to, że błąd absolutny jest oczekiwanej wartości bezwzględnej wartości różnicy między obiektu docelowego i prognozowania|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Znormalizowany średni bezwzględny błąd to średni bezwzględny błąd podzielona przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Podziel według zakresu danych|
median_absolute_error|Mediana bezwzględny błąd jest medianę wszystkich bezwzględnych różnic między obiektu docelowego i prognozowania. To jest odporny elementy odstające.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Znormalizowana mediany bezwzględny błąd jest mediany błąd absolutny podzielona przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Podziel według zakresu danych|
root_mean_squared_error|Główny oznacza to, że błąd kwadratów jest pierwiastek kwadratowy z oczekiwanym kwadratu różnicy między obiektu docelowego i prognozowania|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Znormalizowana głównego oznacza to, że błąd kwadratów błędu głównego średniej kwadratów podzielona przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Podziel według zakresu danych|
root_mean_squared_log_error|Główny oznaczać błędów w dzienniku kwadratów pierwiastek kwadratowy z oczekiwanym błędem logarytmicznej kwadrat|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Znormalizowany błąd oznaczający, że w przypadku standardowego elementu głównego jest średni kwadratowy błąd dziennika podzielony przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Podziel według zakresu danych|

### <a name="pvt"></a>Wykres przewidywany a rzeczywisty
#### <a name="what-is-a-predicted-vs-true-chart"></a>Co to jest wykres predykcyjny a prawda?
Przewidywany a prawda przedstawia relację między przewidywaną wartością a jej skorelowane wartości prawdziwe dla problemu z regresją. Ten wykres może służyć do pomiaru wydajności modelu jako bliżej y = x wiersza przewidywane wartości są lepsze dokładności modelu predykcyjnego.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Co robią automatyczne ML z przewidzianym wykresem a prawdziwym?
Po każdym uruchomieniu widać przewidywane a true wykresu dla każdego modelu regresji. Aby zapewnić ochronę prywatności danych, wartości są ze sobą kwanty, a rozmiar każdego kwantu jest wyświetlany jako wykres słupkowy w dolnej części obszaru wykresu. Model predykcyjny, można porównać z modułem jaśniejszy odcień marginesów błąd, względem idealne wartości, z którym model powinien być.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Przykład 1: model klasyfikacji o niskiej dokładności
![Model regresji z niską dokładnością w prognozie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Przykład 2: model regresji o wysokiej dokładności 
[![model regresji z wysoką dokładnością w jego przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram wykresu reszty
#### <a name="what-is-a-residuals-chart"></a>Co to jest wykres reszty?
Pozostały reprezentuje obserwowanych y — prognozowanej wartości y. Aby pokazać marginesu błędu i odchylenie niski, histogram reszty powinny mieć kształt krzywą dzwonka skupia się wokół 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Co robią automatyczne ML z wykresem reszty?
Automatyczna część ML udostępnia wykres reszty, aby pokazać dystrybucję błędów w przewidywaniach.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Dobry model zazwyczaj ma krzywą dzwonka lub błędy wokół zera.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Przykład 1: model regresji z bias w jego błędach
![Model regresji SA z odchyleniami w jego błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Przykład 2: model regresji z bardziej równomiernym rozkładem błędów
![Model regresji z większą ilością równomiernej dystrybucji błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Interpretacja modelu i ważność funkcji
Zautomatyzowana ML udostępnia pulpit nawigacyjny do tłumaczenia maszynowego dla przebiegów.
Aby uzyskać więcej informacji na temat włączania funkcji interpretacji, zobacz [instrukcje](how-to-machine-learning-interpretability-automl.md) włączania interpretacji w zautomatyzowanych eksperymentach ml.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Wypróbuj przykładowe notesy w programie [zautomatyzowanego modelowania Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
