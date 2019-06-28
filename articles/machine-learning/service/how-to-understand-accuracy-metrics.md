---
title: Szkolenie metryki dokładności w automatycznych ML
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat automatycznego usługi machine learning dokładność metryki dla każdego z przebiegów.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 44dfa387b289afe4dc5f030cca0b13325c04e811
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313299"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Oceny dokładności szkolenia w automatycznych uczenia Maszynowego przy użyciu metryk

Istnieje wiele sposobów, aby wyświetlić metryki dokładność szkolenia dla każdej iteracji wykonywania.

* Użyj [widżet Jupyter](how-to-track-experiments.md#view-run-details)
* Użyj [ `get_metrics()` funkcja](how-to-track-experiments.md#query-run-metrics) na dowolnym `Run` obiektu
* Widok [metryki eksperymentu w witrynie Azure portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="classification-metrics"></a>Metryki klasyfikacji

Następujące metryki są zapisywane w każdej iteracji wykonywania zadania klasyfikacji.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
AUC_Macro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "makro"|
AUC_Micro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Micro jest kolumną obliczaną globalnie, łącząc prawdziwie dodatnie i fałszywych alarmów od każdej klasy| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "micro"|
AUC_Weighted  | AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Ważona jest średnią arytmetyczną oceny dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średni = "ważona"
accuracy|Dokładność jest procent przewidywane etykiety, które dokładnie pasują do etykiety wartość true. |[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Makro jest średnią arytmetyczną wyniku średnią precyzję każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "makro"|
average_precision_score_micro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Micro jest kolumną obliczaną globalnie, łącząc prawdziwie dodatnie i fałszywych alarmów przy każdym odcięcia|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "micro"|
average_precision_score_weighted|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Ważona jest średnią arytmetyczną wyniku średnią precyzję dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "ważona"|
balanced_accuracy|Dokładność o zrównoważonym obciążeniu jest średnią arytmetyczną odwołania dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
f1_score_macro|F1 wynik jest harmoniczna dokładności i odwołania. Makro jest średnią arytmetyczną wynik F1 dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "makro"|
f1_score_micro|F1 wynik jest harmoniczna dokładności i odwołania. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie, fałszywych wyników negatywnych i fałszywych alarmów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "micro"|
f1_score_weighted|F1 wynik jest harmoniczna dokładności i odwołania. Średnia ważona przez klasy częstotliwość oceny F1 dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "ważona"|
log_loss|To jest funkcja utraty używanych w regresji logistycznej (wielomian) i rozszerzenia takich jak sieci neuronowych, zdefiniowane jako ujemny dziennika prawdopodobieństwo true etykiet podane przewidywania prawdopodobieństwa klasyfikatora. Dla jednego przykładu z true etykiety yt w {0,1} oraz szacowane prawdopodobieństwa yp tego yt = 1, utraty dziennika jest - dziennika P (yt&#124;yp) =-(yt log(yp) + (1 - yt) dziennika (1 - yp))|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Odwołaj znormalizowane — makro jest makro odwołania znormalizowane, dzięki czemu losowe wydajności ma wynik 0 i doskonałe wydajności ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro - R) /(1-R), gdzie R jest oczekiwana wartość recall_score_macro dla prognoz losowy (czyli R = 0,5 dla klasyfikacji binarnej) i R=(1/C) klasy C klasyfikacji problemów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro" a następnie (recall_score_macro - R) /(1-R), gdzie R jest oczekiwana wartość recall_score_macro dla prognoz losowy (czyli R = 0,5 dla klasyfikacji binarnej) i R=(1/C) klasy C klasyfikacji problemów|
precision_score_macro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Makro jest średnią arytmetyczną dokładności dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "makro"|
precision_score_micro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie i fałszywych alarmów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "micro"|
precision_score_weighted|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Ważona jest średnią arytmetyczną dokładności dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "ważona"|
recall_score_macro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Makro jest średnią arytmetyczną odwołania dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
recall_score_micro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie, fałszywych wyników negatywnych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "micro"|
recall_score_weighted|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Ważona jest średnią arytmetyczną odwołania dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "ważona"|
weighted_accuracy|Ważona dokładności jest dokładności, gdzie wagę każdy przykład jest równe część wystąpień wartość true, w tym przykładzie wartość true, klasa|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równą część tej klasy, dla każdego elementu w elemencie docelowym|

## <a name="regression-and-forecasting-metrics"></a>Regresji i przygotowywać trafniejsze prognozy metryki

Następujące metryki są zapisywane w każdej iteracji Uruchom regresję lub zadanie prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wariancja wyjaśniono to modelu matematycznego kont odmiany danego zestawu danych. Jest to procent spadek wariancji oryginalne dane do wariancję błędy. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R2 to określenie lub procent redukcji kwadratów błędów w porównaniu z modelem linii bazowej, który wyprowadza średniej. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Obliczanie](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja metodą Spearman jest miarą nonparametric monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacji metodą Spearman nie zakłada, że oba zestawy danych zwykle są rozpowszechniane. Podobnie jak inne współczynniki korelacji ta różni się od -1 do + 1 od 0, co oznacza brak korelacji. Korelacji -1 lub + 1 oznacza dokładne monotoniczny relacji. Dodatnia korelacji oznacza, że x rosną, więc nie y. Korelacji ujemna oznacza, że x rosną, y zmniejsza.|[Obliczanie](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Oznacza to, że błąd absolutny jest oczekiwanej wartości bezwzględnej wartości różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany średni bezwzględny błąd to średni bezwzględny błąd podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Podziel według zakresu danych|
median_absolute_error|Mediana bezwzględny błąd jest medianę wszystkich bezwzględnych różnic między obiektu docelowego i prognozowania. To jest odporny elementy odstające.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowana mediany bezwzględny błąd jest mediany błąd absolutny podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Podziel według zakresu danych|
root_mean_squared_error|Główny oznacza to, że błąd kwadratów jest pierwiastek kwadratowy z oczekiwanym kwadratu różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowana głównego oznacza to, że błąd kwadratów błędu głównego średniej kwadratów podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Podziel według zakresu danych|
root_mean_squared_log_error|Główny oznaczać błędów w dzienniku kwadratów pierwiastek kwadratowy z oczekiwanym błędem logarytmicznej kwadrat|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowane błąd średniej kwadratów dziennika głównego jest błąd średniej kwadratów dziennika głównego podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Podziel według zakresu danych|
