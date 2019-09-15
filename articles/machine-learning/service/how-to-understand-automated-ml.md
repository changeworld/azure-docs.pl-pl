---
title: Zrozumienie zautomatyzowanych wyników ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i zrozumieć wykresy oraz metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 56d8dff7d158cedb4ab33e811cf5134cdbc475ab
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999446"
---
# <a name="understand-automated-machine-learning-results"></a>Informacje o zautomatyzowanych wynikach uczenia maszynowego

W tym artykule dowiesz się, jak wyświetlać i zrozumieć wykresy i metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego. 

Dowiedz się więcej o usługach:
+ [Metryki, wykresy i krzywe dla modeli klasyfikacji](#classification)
+ [Metryki, wykresy i wykresy dla modeli regresji](#regression)
+ [Interpretacja modelu i ważność funkcji](#explain-model)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Utwórz automatyczny eksperyment uczenia maszynowego z zestawem SDK w Azure Portal lub na stronie docelowej obszaru roboczego (wersja zapoznawcza).

    * Użyj zestawu SDK, aby utworzyć [model klasyfikacji](how-to-auto-train-remote.md) lub [model regresji](tutorial-auto-train-models.md)
    * Użyj [Azure Portal lub strony docelowej obszaru roboczego (wersja zapoznawcza)](how-to-create-portal-experiments.md) , aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-the-run"></a>Wyświetlanie przebiegu

Po uruchomieniu zautomatyzowanego eksperymentu w usłudze Machine Learning historia przebiegów będzie dostępna w obszarze roboczym usługi Machine Learning. 

1. Przejdź do swojego obszaru roboczego.

1. W lewym panelu obszaru roboczego wybierz pozycję **eksperymenty**.

   ![Zrzut ekranu przedstawiający menu eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na liście eksperymentów wybierz ten, który chcesz poznać.

   [![Lista eksperymentów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. W dolnej tabeli wybierz **numer uruchomienia**.

   Przebieg eksperymentu) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. W tabeli iteracje wybierz **numer iteracji** dla modelu, który ma zostać poddany dalszej analizie.

   [![Model eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Te same wyniki są również widoczne podczas uruchamiania, gdy używasz `RunDetails` [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Wyniki klasyfikacji

Thee następujące metryki i wykresy są dostępne dla każdego modelu klasyfikacji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#classification-metrics)
+ [Macierz pomyłek](#confusion-matrix)
+ [Precision-Recall wykresu](#precision-recall-chart)
+ [Odbiornik operacyjne cechy (lub ROC)](#roc)
+ [Przenoszenie krzywej](#lift-curve)
+ [Krzywa zysków](#gains-curve)
+ [Wykres odwzorowania](#calibration-plot)

### <a name="classification-metrics"></a>Metryki klasyfikacji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania klasyfikacji.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
AUC_Macro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "makro"|
AUC_Micro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie z każdej klasy.| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "micro"|
AUC_Weighted  | AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Ważone to średnia arytmetyczna wyniku dla każdej klasy, ważona przez liczbę wystąpień prawdziwych w każdej klasie.| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średni = "ważona"
accuracy|Dokładność jest procent przewidywane etykiety, które dokładnie pasują do etykiety wartość true. |[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Makro jest średnią arytmetyczną średniego wyniku dokładności dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "makro"|
average_precision_score_micro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie dla każdego odcięcia.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "micro"|
average_precision_score_weighted|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Ważone jest średnią arytmetyczną średniego oceny dokładności dla każdej klasy, ważonej przez liczbę wystąpień prawdziwych w każdej klasie.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "ważona"|
balanced_accuracy|Dokładność o zrównoważonym obciążeniu jest średnią arytmetyczną odwołania dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
f1_score_macro|F1 wynik jest harmoniczna dokładności i odwołania. Makro jest średnią arytmetyczną wyniku F1 dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "makro"|
f1_score_micro|F1 wynik jest harmoniczna dokładności i odwołania. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "micro"|
f1_score_weighted|F1 wynik jest harmoniczna dokładności i odwołania. Średnia ważona przez klasy częstotliwość oceny F1 dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "ważona"|
log_loss|To jest funkcja utraty używanych w regresji logistycznej (wielomian) i rozszerzenia takich jak sieci neuronowych, zdefiniowane jako ujemny dziennika prawdopodobieństwo true etykiet podane przewidywania prawdopodobieństwa klasyfikatora. Dla pojedynczego przykładu o prawdziwej etykiecie yt {0,1} w i szacowane prawdopodobieństwo YP, że yt = 1, utrata dziennika to-log P (&#124;yt YP) =-(YT log (YP) + (1-yt) log (1-YP)).|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Odwołaj znormalizowane — makro jest makro odwołania znormalizowane, dzięki czemu losowe wydajności ma wynik 0 i doskonałe wydajności ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro-R)/(1-R), gdzie R jest oczekiwaną wartością recall_score_macro dla losowych prognoz (tj. R = 0,5 dla klasyfikacji binarnej i języka R = (1/C) dla błędów klasyfikacji klasy C).|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Makro jest arytmetyczną dokładnością dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "makro"|
precision_score_micro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Mikro jest obliczany globalnie przez obliczenie całkowitej liczby pozytywnych dodatnich i fałszywych wartości dodatnich.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "micro"|
precision_score_weighted|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Ważone jest średnią arytmetyczną dokładności dla każdej klasy, ważonej według liczby prawdziwych wystąpień w każdej klasie.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "ważona"|
recall_score_macro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Makro jest średnią arytmetyczną operacji odwoływania dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
recall_score_micro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych wartości dodatnich|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "micro"|
recall_score_weighted|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Ważone to średnia arytmetyczna operacji odwoływania dla każdej klasy, ważona według liczby prawdziwych wystąpień w każdej klasie.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "ważona"|
weighted_accuracy|Waga ważona jest dokładnością, gdzie waga określona dla każdego przykładu jest równa proporcji prawdziwe wystąpienia w tym przykładzie.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równą część tej klasy, dla każdego elementu w elemencie docelowym|

### <a name="confusion-matrix"></a>Macierz pomyłek

Macierz pomyłek służy do opisywania wydajność model klasyfikacji. Każdy wiersz zawiera wystąpienia klasy wartość true, a każda kolumna reprezentuje wystąpienia to przewidywana klasa. Macierz pomyłek pokazuje poprawnie sklasyfikowane etykiety i etykiety niepoprawnie sklasyfikowanych w danym modelu.

W przypadku problemów z klasyfikacji usługi Azure Machine Learning automatycznie zapewnia macierz pomyłek dla każdego modelu, który jest skompilowany. W przypadku każdej matrycy nieistotnej, zautomatyzowany ML będzie wyświetlać częstotliwość każdej etykiety predykcyjnej i każdej przedziału prawdziwych etykiet. Ciemniejszy kolor, im wyższy licznik w danej części macierzy. Najlepiej, najciemniejszymi kolorami byłyby ukośne na przekątnej macierzy. 

Przykład 1: Model klasyfikacji z niską dokładnością ![modelu klasyfikacji z niską dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Przykład 2: Model klasyfikacji z wysoką dokładnością (idealnym ![) modelem klasyfikacji o wysokiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Precision-recall wykresu

Z tego wykresu można porównać krzywych precision-recall dla każdego modelu określić model, który ma akceptowalnego relacja dokładności i odwołania dla danego problemu określonego rodzaju. Ten wykres przedstawia średnią Precision-Recall — makro, Micro średni Precision-Recall i dokładności recall skojarzone z wszystkich klas dla modelu.

Termin dokładności reprezentuje zdolność klasyfikatora poprawnie oznaczenie wszystkich wystąpień. Odwołania reprezentuje możliwości klasyfikatora znaleźć wszystkie wystąpienia określonej etykiecie. Krzywa precision-recall przedstawiono relację między tymi dwoma pojęciami. Najlepiej, jeśli model musi 100% dokładności i 100-procentową dokładnością.

Przykład 1: Model klasyfikacji z małą dokładnością i niskim odwołaniem ![modelu klasyfikacji z niską dokładnością i niskim wycofywaniem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Przykład 2: Model klasyfikacji o wartości ~ 100% Precision i ~ 100% (idealne) ![model klasyfikacji o wysokiej dokładności i odwołaniu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Odbiornik operacyjnego cechy (lub ROC) jest wykres poprawnie sklasyfikowane etykiety, a niepoprawnie sklasyfikowane etykiety dla określonego modelu. Może być mniej informacyjne krzywej ROC, gdy szkolenie modeli w zestawach danych przy użyciu wysokie odchylenie, ponieważ nie będą widoczne fałszywie dodatnie etykiety.

Przykład 1: Model klasyfikacji z niską rzeczywistą etykietami i wysokimi fałszywymi ![etykietami model klasyfikacji z niską rzeczywistą etykietami i silnymi etykietami fałszywymi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Przykład 2: Model klasyfikacji z wysoką prawdziwymi etykietami i niskimi ![etykietami fałszywych z modelem klasyfikacji z wysokimi etykietami i niskimi etykietami fałszywymi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Przenoszenie krzywej

Możesz porównać przyrostu modelu tworzone automatycznie za pomocą usługi Azure Machine Learning do linii bazowej Aby wyświetlić zysk wartość określonego modelu.

Wykresy przyrostu służą do oceny wydajności model klasyfikacji. Pokazuje, ile lepiej można oczekiwać, że sposób korzystania z modelu, w porównaniu do bez modelu. 

Przykład 1: Model jest niezgodny z przypadkowym modelem ![wyboru model klasyfikacji, który jest niezgodny z losowym modelem wyboru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Przykład 2: Model jest lepszy niż losowy model ![wyboru modelu klasyfikacji, który wykonuje lepsze](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Krzywa zysków

Wykres zyski ocenia wydajność model klasyfikacji przez każdy fragment danych. Pokazuje dla każdego percentylu zestawu danych, ile lepiej można oczekiwać, że do wykonania porównana z modelem losowej.

Użyj wykresu skumulowanego zyski ułatwiające wybór odcięcia Klasyfikacja za pomocą wartość procentową, która odnosi się do żądanego korzyści z modelu. Informacje te stanowią innym sposobem spojrzenie na wyniki na wykresie przyrostu towarzyszącej.

Przykład 1: Model klasyfikacji z minimalnym wzrostem ![modelu klasyfikacji z minimalnym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Przykład 2: Model klasyfikacji ze znaczącym wzrostem ![modelu klasyfikacji z znaczącym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Wykres odwzorowania

W przypadku wszystkich problemów klasyfikacji możesz przejrzeć odwzorowania linii średniej micro, średnia — makro i każda klasa w danym modelu predykcyjnego. 

Wykres odwzorowania służy do wyświetlania zaufania modelu predykcyjnego. Odbywa się to poprzez przedstawiający relację między prawdopodobieństwa przewidywanych i rzeczywistych prawdopodobieństwo, gdzie "prawdopodobieństwo" oznacza prawdopodobieństwo, że konkretnego wystąpienia, należy w ramach niektóre etykiety. Dobrze pomiarowej modelu wyrównane y = x wiersza, gdzie jest względnie pewność, że jej prognozy. Model nadmiernego confident wyrównane y = 0 wiersza, w którym przewidywany prawdopodobieństwo, że jest zainstalowany, ale istnieje nie rzeczywiste prawdopodobieństwo.

Przykład 1: Bardziej dobrze skalibrowane ![ modele modelu bardziej dobrze skalibrowanego](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Przykład 2: Model ![z nadmiernym priorytetem modelem o nadmiernej pewności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Wyniki regresji

Thee następujące metryki i wykresy są dostępne dla każdego modelu regresji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#reg-metrics)
+ [Przewidywane programu vs. Wartość true](#pvt)
+ [Histogram reszty](#histo)


### <a name="reg-metrics"></a>Metryki regresji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania regresji lub prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wariancja wyjaśniono to modelu matematycznego kont odmiany danego zestawu danych. Jest to procent spadek wariancji oryginalne dane do wariancję błędy. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R2 to określenie lub procent redukcji kwadratów błędów w porównaniu z modelem linii bazowej, który wyprowadza średniej. |[Obliczanie](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja metodą Spearman jest miarą nonparametric monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacji metodą Spearman nie zakłada, że oba zestawy danych zwykle są rozpowszechniane. Podobnie jak inne współczynniki korelacji ta różni się od -1 do + 1 od 0, co oznacza brak korelacji. Korelacji -1 lub + 1 oznacza dokładne monotoniczny relacji. Dodatnia korelacji oznacza, że x rosną, więc nie y. Korelacji ujemna oznacza, że x rosną, y zmniejsza.|[Obliczanie](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Oznacza to, że błąd absolutny jest oczekiwanej wartości bezwzględnej wartości różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany średni bezwzględny błąd to średni bezwzględny błąd podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Podziel według zakresu danych|
median_absolute_error|Mediana bezwzględny błąd jest medianę wszystkich bezwzględnych różnic między obiektu docelowego i prognozowania. To jest odporny elementy odstające.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowana mediany bezwzględny błąd jest mediany błąd absolutny podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Podziel według zakresu danych|
root_mean_squared_error|Główny oznacza to, że błąd kwadratów jest pierwiastek kwadratowy z oczekiwanym kwadratu różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowana głównego oznacza to, że błąd kwadratów błędu głównego średniej kwadratów podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Podziel według zakresu danych|
root_mean_squared_log_error|Główny oznaczać błędów w dzienniku kwadratów pierwiastek kwadratowy z oczekiwanym błędem logarytmicznej kwadrat|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowany błąd oznaczający, że w przypadku standardowego elementu głównego jest średni kwadratowy błąd dziennika podzielony przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Podziel według zakresu danych|

### <a name="pvt"></a>Przewidywany a Prawda

Przewidywane programu vs. Wartość true przedstawiono relacje między wartością prognozowaną a jego korelację wartość true, aby uzyskać problem regresji. Ten wykres może służyć do pomiaru wydajności modelu jako bliżej y = x wiersza przewidywane wartości są lepsze dokładności modelu predykcyjnego.

Po każdym uruchomieniu widać przewidywane a true wykresu dla każdego modelu regresji. Aby zapewnić ochronę prywatności danych, wartości są ze sobą kwanty, a rozmiar każdego kwantu jest wyświetlany jako wykres słupkowy w dolnej części obszaru wykresu. Model predykcyjny, można porównać z modułem jaśniejszy odcień marginesów błąd, względem idealne wartości, z którym model powinien być.

Przykład 1: Model regresji z niską dokładnością ![w przypadku prognozowania modelu regresji z niską dokładnością w przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Przykład 2: Model regresji z wysoką dokładnością w prognozie [ ![modelu regresji o wysokiej dokładności w jej prognozach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram reszty

Pozostały reprezentuje obserwowanych y — prognozowanej wartości y. Aby pokazać marginesu błędu i odchylenie niski, histogram reszty powinny mieć kształt krzywą dzwonka skupia się wokół 0. 

Przykład 1: Model regresji z bias w jego błędach ![model regresji sa z odchyleniami w jego błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Przykład 2: Model regresji z większą ilością równomiernej ![dystrybucji błędów modelu regresji z większą ilością nawet dystrybucji błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Interpretacja modelu i ważność funkcji

Ważność funkcji pozwala zobaczyć, jak cenne są poszczególne funkcje w konstrukcji modelu. To obliczenie jest domyślnie wyłączone, ponieważ może znacząco zwiększyć czas wykonywania.   Możesz włączyć wyjaśnienie modelu dla wszystkich modeli lub wyjaśnić tylko model najlepiej pasujący.

Możesz przejrzeć wynik znaczenie funkcji dla modelu ogólny, jak również na klasę w modelu predykcyjnego. Możesz zobaczyć na funkcję porównanie znaczenie dla każdej klasy lub ogólnej.

![Funkcja wyjaśnienia możliwości](./media/how-to-understand-automated-ml/feature-importance.gif)

Aby uzyskać więcej informacji na temat włączania funkcji interpretacji, zobacz [Konfigurowanie zautomatyzowanych eksperymentów ml w języku Python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Aby zapoznać się z przykładem, który objaśnia najlepszy model, zobacz [najlepsze wyjaśnienie modelu](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Wypróbuj przykładowy Notes [Machine Learning objaśnienie modelu automatycznego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
