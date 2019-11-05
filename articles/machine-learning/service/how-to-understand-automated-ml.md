---
title: Zrozumienie zautomatyzowanych wyników ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i zrozumieć wykresy oraz metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489393"
---
# <a name="understand-automated-machine-learning-results"></a>Informacje o zautomatyzowanych wynikach uczenia maszynowego
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyświetlać i zrozumieć wykresy i metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego. 

Dowiedz się więcej o usługach:
+ [Metryki, wykresy i krzywe dla modeli klasyfikacji](#classification)
+ [Metryki, wykresy i wykresy dla modeli regresji](#regression)
+ [Interpretacja modelu i ważność funkcji](#explain-model)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Utwórz eksperyment dla automatycznego przebiegu uczenia maszynowego z zestawem SDK lub w programie Azure Machine Learning Studio.

    * Użyj zestawu SDK, aby utworzyć [model klasyfikacji](how-to-auto-train-remote.md) lub [model regresji](tutorial-auto-train-models.md)
    * Użyj [Azure Machine Learning Studio](how-to-create-portal-experiments.md) , aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-the-run"></a>Wyświetlanie przebiegu

Po uruchomieniu zautomatyzowanego eksperymentu w usłudze Machine Learning historia przebiegów jest dostępna w obszarze roboczym uczenia maszynowego. 

1. Przejdź do swojego obszaru roboczego.

1. W lewym panelu obszaru roboczego wybierz pozycję **eksperymenty**.

   ![Zrzut ekranu przedstawiający menu eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na liście eksperymentów wybierz ten, który chcesz poznać.

   [Lista eksperymentów ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. W dolnej tabeli wybierz **numer uruchomienia**.

   [![uruchomienie eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. W tabeli iteracje wybierz **numer iteracji** dla modelu, który ma zostać poddany dalszej analizie.

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

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
AUC_Macro| AUC to obszar pod krzywą charakterystyczną dla odbiornika. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "Macro"|
AUC_Micro| AUC to obszar pod krzywą charakterystyczną dla odbiornika. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie z każdej klasy.| [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średnia = "mikro"|
AUC_Weighted  | AUC to obszar pod krzywą charakterystyczną dla odbiornika. Ważone to średnia arytmetyczna wyniku dla każdej klasy, ważona przez liczbę wystąpień prawdziwych w każdej klasie.| [Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średnia = "ważone"
accuracy|Dokładność to procent prognozowanych etykiet, które dokładnie pasują do prawdziwych etykiet. |[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Makro jest średnią arytmetyczną średniego wyniku dokładności dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "Macro"|
average_precision_score_micro|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie dla każdego odcięcia.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średnia = "mikro"|
average_precision_score_weighted|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Ważone jest średnią arytmetyczną średniego oceny dokładności dla każdej klasy, ważonej przez liczbę wystąpień prawdziwych w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średnia = "ważone"|
balanced_accuracy|Równoważna dokładność to arytmetyczna średnia odwołania dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
f1_score_macro|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Makro jest średnią arytmetyczną wyniku F1 dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "Macro"|
f1_score_micro|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średnia = "mikro"|
f1_score_weighted|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Ważone średnie według częstotliwości klasy wyników F1 dla każdej klasy|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średnia = "ważone"|
log_loss|Jest to funkcja strat użyta w (MULTINOMIAL) logistyki i rozszerzenia, takie jak sieci neuronowych, zdefiniowane jako negatywna prawdopodobieństwo rejestrowania dla prawdziwych etykiet z przewidywaniami klasyfikatora probabilistyczne. Dla pojedynczego przykładu o prawdziwej etykiecie yt w {0,1} i szacowanego prawdopodobieństwa YP yt = 1, utrata dziennika to-log P (&#124;yt YP) =-(YT log (YP) + (1-yt) log (1-YP)).|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Znormalizowane makro odwołuje się do znormalizowanego, tak aby losowo wydajność miało wynik 0, a doskonałe wydajność ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro-R)/(1-R), gdzie R jest oczekiwaną wartością recall_score_macro dla losowych prognoz (tj. R = 0,5 dla klasyfikacji binarnej i języka R = (1/C) dla błędów klasyfikacji klasy C).|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Precyzja to procent elementów oznaczonych jako niektóre klasy, które faktycznie znajdują się w tej klasie. Makro jest arytmetyczną dokładnością dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "Macro"|
precision_score_micro|Precyzja to procent elementów oznaczonych jako niektóre klasy, które faktycznie znajdują się w tej klasie. Mikro jest obliczany globalnie przez obliczenie całkowitej liczby pozytywnych dodatnich i fałszywych wartości dodatnich.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średnia = "mikro"|
precision_score_weighted|Precyzja to procent elementów oznaczonych jako niektóre klasy, które faktycznie znajdują się w tej klasie. Ważone jest średnią arytmetyczną dokładności dla każdej klasy, ważonej według liczby prawdziwych wystąpień w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średnia = "ważone"|
recall_score_macro|Funkcja odwoływania jest wartością procentową elementów znajdujących się w określonej klasie, które są poprawnie oznaczone etykietami. Makro jest średnią arytmetyczną operacji odwoływania dla każdej klasy.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
recall_score_micro|Funkcja odwoływania jest wartością procentową elementów znajdujących się w określonej klasie, które są poprawnie oznaczone etykietami. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych wartości dodatnich|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średnia = "mikro"|
recall_score_weighted|Funkcja odwoływania jest wartością procentową elementów znajdujących się w określonej klasie, które są poprawnie oznaczone etykietami. Ważone to średnia arytmetyczna operacji odwoływania dla każdej klasy, ważona według liczby prawdziwych wystąpień w każdej klasie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średnia = "ważone"|
weighted_accuracy|Waga ważona jest dokładnością, gdzie waga określona dla każdego przykładu jest równa proporcji prawdziwe wystąpienia w tym przykładzie.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równym proporcji tej klasy dla każdego elementu w elemencie docelowym|

### <a name="confusion-matrix"></a>Zamieszanie macierzy

Macierz niepomylena służy do opisywania wydajności modelu klasyfikacji. Każdy wiersz zawiera wystąpienia klasy true, a każda kolumna reprezentuje wystąpienia klasy predykcyjnej. Macierz nieporozumiena pokazuje poprawnie sklasyfikowane etykiety i niewłaściwie sklasyfikowane etykiety dla danego modelu.

W przypadku problemów z klasyfikacją Azure Machine Learning automatycznie zapewnia niepomyleną macierz dla każdego skompilowanego modelu. W przypadku każdej matrycy nieistotnej, zautomatyzowany ML będzie wyświetlać częstotliwość każdej etykiety predykcyjnej i każdej przedziału prawdziwych etykiet. Ciemniejszy kolor, im wyższy licznik w danej części macierzy. Najlepiej, najciemniejszymi kolorami byłyby ukośne na przekątnej macierzy. 

Przykład 1: model klasyfikacji z niską dokładnością ![modelem klasyfikacji z niską dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Przykład 2: model klasyfikacji o wysokiej dokładności (idealny) ![model klasyfikacji o wysokiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Wykres dokładności do odzyskania

Za pomocą tego wykresu można porównać krzywe odwołań dokładności dla każdego modelu, aby określić model, który ma akceptowalną relację między dokładnością i odwołaniem dla konkretnego problemu biznesowego. Ten wykres pokazuje średnią precyzję makra — odwołanie, średnią precyzję i odwołanie do odwołania do wszystkich klas modelu.

Termin dokładność reprezentuje możliwość poprawnego etykietowania wszystkich wystąpień przez klasyfikatora. Funkcja odwoływania reprezentuje możliwość, aby klasyfikator znalazł wszystkie wystąpienia określonej etykiety. Krzywa odwołań dokładności przedstawia relację między tymi dwoma koncepcjami. W idealnym przypadku model ma dokładność 100% precyzji i 100%.

Przykład 1: model klasyfikacji z niską dokładnością i niskim odwołaniem ![model klasyfikacji z niską dokładnością i niskim użyciem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Przykład 2: model klasyfikacji z ~ 100% precyzji i ~ 100% odzyskanie (idealne) ![model klasyfikacji o wysokiej dokładności i odzyskanie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Charakterystyka operacyjna odbiornika (lub ROC) to wykres poprawnie sklasyfikowanych etykiet i niewłaściwie sklasyfikowanych etykiet dla określonego modelu. Krzywa ROC może być mniej informacyjna podczas uczenia modeli w zestawach danych o wysokiej odchylenia, ponieważ nie będą one pokazywały fałszywych etykiet pozytywnych.

Przykład 1: model klasyfikacji z niską rzeczywistą etykietami i silnymi etykietami fałszywymi ![model klasyfikacji z niską ilością etykiet i etykietami o wysokich wartościach fałszywych](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Przykład 2: model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywych ![model klasyfikacji z wysokimi etykietami o wysokiej prawdziwej etykiecie i niskiej wartości fałszów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Krzywa podnoszenia

Można porównać automatycznie dźwig modelu z Azure Machine Learningm z linią bazową, aby wyświetlić jego wartość w tym konkretnym modelu.

Wykresy przyrostowe służą do oceny wydajności modelu klasyfikacji. Pokazuje, ile lepiej można wykonać z modelem w porównaniu z modelem. 

Przykład 1: model jest niezgodny z przypadkowym modelem wyboru ![model klasyfikacji, który jest niezgodny z losowym modelem zaznaczania](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Przykład 2: model działa lepiej niż losowy model wyboru ![model klasyfikacji, który wykonuje lepsze](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Krzywa zysków

Wykres zysków oblicza wydajność modelu klasyfikacji według każdej części danych. Pokazuje ona dla każdego percentylu zestawu danych, ile lepiej można oczekiwać w porównaniu z losowym modelem wyboru.

Użyj wykresu skumulowanych zysków, aby pomóc w wyborze odcięcia klasyfikacji przy użyciu wartości procentowej odpowiadającej żądanemu zyskowi z modelu. Te informacje zapewniają inny sposób przeglądania wyników na dołączonym wykresie podnoszenia.

Przykład 1: model klasyfikacji z minimalnym wzrostem ![model klasyfikacji z minimalnym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Przykład 2: model klasyfikacji z znaczącym wpływem ![model klasyfikacji z znaczącym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Wykres kalibracyjny

W przypadku wszystkich problemów z klasyfikacją można sprawdzić linię kalibracji dla mikrośredniej, średniej przez makro i każdej klasy w danym modelu predykcyjnym. 

Wykres kalibracyjny służy do wyświetlania pewności dotyczącej modelu predykcyjnego. Robi to, pokazując zależność między przewidywanym prawdopodobieństwem a rzeczywistym prawdopodobieństwem, gdzie "prawdopodobieństwo" oznacza prawdopodobieństwo, że określone wystąpienie należy do pewnej etykiety. Dobrze skalibrowane modele są wyrównane z linią y = x, gdzie w jej przewidywaniach jest to uzasadnione. Model z nadmiernym prawdopodobieństwem jest wyrównany do osi y = 0, w której przewidywane prawdopodobieństwo jest obecne, ale nie ma rzeczywistego prawdopodobieństwa.

Przykład 1: bardziej dobrze skalibrowane modele ![ bardziej wydajny model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Przykład 2: model z nadmiernym użyciem ![modelem o nadmiernej pewności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Wyniki regresji

Thee następujące metryki i wykresy są dostępne dla każdego modelu regresji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#reg-metrics)
+ [Przewidywany a prawdziwy](#pvt)
+ [Histogram reszty](#histo)


### <a name="reg-metrics"></a>Metryki regresji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania regresji lub prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wyjaśniono wariancję to proporcja, do której model matematyczny jest przeznaczony dla odmiany danego zestawu danych. Jest to procentowy spadek wariancji oryginalnych danych do wariancji błędów. Gdy średnią z błędów jest 0, jest równa objaśnionej wariancji.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R2 to współczynnik wyznaczania lub procent redukcji w przypadku błędów kwadratowych w porównaniu z modelem bazowym, który wyprowadza średnią. |[Podstaw](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja Spearman jest miarą nieparametryczną monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacja Spearman nie zakłada, że oba zestawy danych są zwykle dystrybuowane. Podobnie jak inne Współczynniki korelacji, ta wartość różni się od-1 do + 1 i 0 oznacza brak korelacji. Korelacje-1 lub + 1 implikują dokładną relację monotoniczny. Korelacje pozytywne implikują, że w miarę wzrostu x, więc jest to wartość y. Korelacje negatywne oznacza, że w miarę wzrostu x zmniejsza się wartość y.|[Podstaw](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Średni błąd bezwzględny jest oczekiwaną wartością bezwzględną różnicy między obiektem docelowym a prognozą|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany błąd bezwzględny oznacza błąd bezwzględny podzielony przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dzielenie według zakresu danych|
median_absolute_error|Średni błąd bezwzględny to mediana wszystkich bezwzględnych różnic między obiektem docelowym a przewidywaniam. Ta utrata jest niezawodna dla wartości odstających.|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowany błąd bezwzględny jest średnim błędem bezwzględnym podzielonym przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dzielenie według zakresu danych|
root_mean_squared_error|Wartość "pierwiastek" oznacza pierwiastek kwadratowy o oczekiwanej różnicy kwadratowej między elementem docelowym a prognozą|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowany, średni błąd oznaczający, że błąd oznaczający pierwiastek jako średni, podzielony przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dzielenie według zakresu danych|
root_mean_squared_log_error|Główny średni kwadratowy błąd w dzienniku jest pierwiastek kwadratowy oczekiwanego kwadratowego błędu logarytmicznego|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowany błąd oznaczający, że w przypadku standardowego elementu głównego jest średni kwadratowy błąd dziennika podzielony przez zakres danych|[Podstaw](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dzielenie według zakresu danych|

### <a name="pvt"></a>Przewidywany a prawdziwy

Przewidywany a prawda przedstawia relację między przewidywaną wartością a jej skorelowane wartości prawdziwe dla problemu z regresją. Ten wykres może służyć do mierzenia wydajności modelu, jak bliżej osi y = x, a wartości przewidywane to lepsza dokładność modelu predykcyjnego.

Po każdym uruchomieniu można zobaczyć przewidywany wykres a true dla każdego modelu regresji. Aby chronić prywatność danych, wartości są Binned razem, a rozmiar każdego pojemnika jest pokazywany jako wykres słupkowy w dolnej części obszaru wykresu. Można porównać model predykcyjny, korzystając z jaśniejszego obszaru cieniowania pokazującego marginesy błędów, względem idealnej wartości, w której powinien się znajdować model.

Przykład 1: model regresji o niskiej dokładności w prognozie ![model regresji z niską dokładnością w przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Przykład 2: model regresji o wysokiej dokładności w jej prognozach [![model regresji z wysoką dokładnością w jego przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram reszty

Reszta reprezentuje zaobserwowane y — przewidywane y. Aby pokazać margines błędu z niską ilością bias, histogram reszty powinien być w kształcie krzywej dzwonka, wyśrodkowany wokół 0. 

Przykład 1: model regresji z bias w błędach ![model regresji programu SA z odchyleniami w jego błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Przykład 2: model regresji z większą ilością nawet dystrybucji błędów ![model regresji z większą ilością nawet dystrybucji błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Interpretacja modelu i ważność funkcji

Ważność funkcji pozwala zobaczyć, jak cenne są poszczególne funkcje w konstrukcji modelu. To obliczenie jest domyślnie wyłączone, ponieważ może znacząco zwiększyć czas wykonywania.   Możesz włączyć wyjaśnienie modelu dla wszystkich modeli lub wyjaśnić tylko model najlepiej pasujący.

Ocenę znaczenia funkcji dla modelu ogólnego, a także dla każdej klasy w modelu predykcyjnym. Na podstawie funkcji można zobaczyć, jak istotna jest porównuje ważność z każdą klasą.

![Możliwość wyjaśnienia funkcji](./media/how-to-understand-automated-ml/feature-importance.gif)

Aby uzyskać więcej informacji na temat włączania funkcji interpretacji, zobacz [instrukcje](how-to-machine-learning-interpretability-automl.md) włączania interpretacji w zautomatyzowanych eksperymentach ml.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Wypróbuj przykładowy Notes [Machine Learning objaśnienie modelu automatycznego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
