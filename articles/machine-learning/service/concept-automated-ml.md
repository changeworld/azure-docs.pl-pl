---
title: Automatyczne wybieranie algorytmu uczenia Maszynowego i dostosowywania
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak usługa Azure Machine Learning automatycznie wybrać algorytm dla Ciebie i wygenerować model z niego, aby zaoszczędzić czas przy użyciu parametrów i kryteria można wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515564"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczna usługi machine learning?

Zautomatyzowane uczenia maszynowego, nazywana także automatyczne ML to proces automatyzacji zadań dużo czasu, iteracyjne projektowania modelu uczenia maszynowego. Umożliwia analitykom danych, analitycy i deweloperom tworzyć modele uczenia Maszynowego, o dużej skali, wydajności i produktywności, jednocześnie zatwierdzeniu jakość modelu.

Tradycyjne usługi machine learning model programowania jest mocno obciążających, wymagających wiedzy specjalistycznej znaczące i czas tworzenia i porównywania wielu modeli. Zastosuj automatyczne uczenia Maszynowego, gdy chcesz, aby usługa Azure Machine Learning do nauczenia i dostosowywanie modelu przy użyciu metrykę docelowych, które określisz. Usługa następnie wykonuje iterację przez algorytmy uczenia Maszynowego z zestawu wybranych funkcji, w którym każda iteracja tworzy modelu z wynikiem szkolenia. Im większa wynik, tym lepiej model jest uznawany za "fit" dane.

Przy użyciu uczenia maszynowego automatycznych, będzie Przyspiesz czas potrzebny do pobrania modeli uczenia Maszynowego gotowe do produkcji i doskonałą wydajność.

## <a name="when-to-use-automated-ml"></a>Kiedy należy używać automatycznego uczenia Maszynowego

ML automatycznych demokratyzuje procesu tworzenia modeli uczenia maszynowego i umożliwia jego użytkowników, niezależnie od ich wiedzy do nauki o danych do identyfikowania potok nauczania maszyny end-to-end dla wszelkich problemów.

Naukowców i analityków i deweloperom różnych branż, można użyć automatycznego ML do:

+ Implementowanie rozwiązania do uczenia maszynowego bez doskonałej znajomości programowania
+ Oszczędzaj czas i zasoby
+ Korzystaj z najlepszych rozwiązań do nauki o danych
+ Podaj agile rozwiązywania problemów

## <a name="how-automated-ml-works"></a>Jak automatyczne działa ML

Za pomocą **usługi Azure Machine Learning**, można projektować i uruchamiać zautomatyzowane eksperymentów uczenia Maszynowego szkolenia z następujące kroki:

1. **Zidentyfikować ML problem** które należy rozwiązać: klasyfikacji, funkcja prognozowania lub regresji

1. **Określ źródło i format danych szkoleniowych etykietami**: Tablice Numpy lub Pandas dataframe

1. **Skonfiguruj cel obliczeń do trenowania modelu**, takich jak usługi [komputera lokalnego, Azure Machine Learning oblicza, zdalnych maszynach wirtualnych lub usługi Azure Databricks](how-to-set-up-training-targets.md).  Dowiedz się więcej o automatycznych szkolenia [do zdalnego zasobu](how-to-auto-train-remote.md).

1. **Skonfiguruj automatyczne maszynę uczenia parametry** określające, jak dużo iteracji przez różne modele, ustawienia hiperparametrycznego zaawansowane przetwarzanie wstępne cechowania i jakie metryki mają Przyjrzyj się podczas określania najlepszy model.  Można skonfigurować ustawienia dla eksperymentu szkolenia automatyczne [w witrynie Azure portal](how-to-create-portal-experiments.md) lub [z zestawem SDK](how-to-configure-auto-train.md).

1. **Prześlij szkolenia uruchomienia.**

  ![Automatyczne usługi Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Podczas szkolenia, usługi Azure Machine Learning, powstaje coraz w potokach równoległych, którzy spróbują różnych algorytmów i parametrów. Zatrzyma się po trafienia kryteriów zakończenia zdefiniowanych w eksperymencie.

Można również sprawdzić zarejestrowane informacje wykonywania zawiera metryki zebrane podczas uruchamiania. Uruchom szkolenia tworzy obiekt Python serializacji (`.pkl` pliku) zawierający model i wstępnego przetwarzania danych.

Podczas konstruowania modelu jest zautomatyzowane, możesz również [Dowiedz się, jak ważna lub odpowiednie funkcje są](how-to-configure-auto-train.md#explain) wygenerowanego modeli.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Przetwarzanie wstępne

W każdej automatycznych eksperymentu uczenia maszynowego przy użyciu domyślnych metod i opcjonalnie za pomocą zaawansowanego przetwarzania wstępnego jest wstępnie przetworzonych danych.

### <a name="automatic-preprocessing-standard"></a>Automatyczne wstępne przetwarzanie (standardowa)

W każdej automatycznych eksperymentu uczenia maszynowego danych jest automatycznie skalowany lub znormalizowane ułatwiające algorytmy działać dobrze.  Podczas uczenia modelu jednej z następujących technik skalowanie lub normalizacji zostaną zastosowane do każdego modelu.

|Skalowanie&nbsp;&&nbsp;normalizacji| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ujednolicenie funkcji, usuwając średnią i skalowanie do jednostki wariancji  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca funkcji, skalując w każdej funkcji, minimalne i maksymalne tej kolumny  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Możesz zmieniać skalę każdej funkcji, maksymalna wartość bezwzględna |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tej funkcji programu Scaler przy ich zakres kwantyl |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Liniowy wymiarowości przy użyciu pojedynczej rozkładu wartości danych do projektu go na niższych wymiarowej |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Transformer to wykonuje liniowej wymiarowości przy użyciu rozkładu obciętą wartość pojedynczej (SVD). Sprzecznie UPW to narzędzie do szacowania nie centrum danych przed obliczeń dekompozycji pojedynczej wartości. Oznacza to, że może współpracować z macierzy scipy.sparse efektywnie |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każda próbka (czyli każdy wiersz macierzy danych) z co najmniej jeden składnik różna od zera jest ponownie skalowane niezależnie od innych przykładów tak, aby równa jego norm (P1 lub P2) |

### <a name="advanced-preprocessing-optional-featurization"></a>Zaawansowane przetwarzanie wstępne: cechowania opcjonalne

Przetwarzanie wstępne zaawansowanej dodatkowe i cechowania są również dostępne, takich jak brakujące wartości, przypisywania, kodowanie i przekształcenia. [Dowiedz się więcej o jakie cechowania jest dołączony](how-to-create-portal-experiments.md#preprocess). Włącz to ustawienie za pomocą:

+ Witryna Azure Portal: Wybieranie **wstępnie Przetwórz** pola wyboru w **Zaawansowane ustawienia** [za pomocą tych kroków](how-to-create-portal-experiments.md).

+ Python SDK: Określanie `"preprocess": True` dla [ `AutoMLConfig` klasy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modele zespołu

Możesz uczyć modele zespołu przy użyciu usługi uczenie maszynowe automatycznych przy użyciu [algorytm wybór zespołu Caruana przy użyciu posortowanych inicjowania zespołu](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Learning zespołu zwiększa machine learning wyniki i wydajność predykcyjne, łącząc wiele modeli, w przeciwieństwie do używania jednego modeli. Iteracji zespół jest wyświetlana jako ostatniej iteracji przebieg.

## <a name="training-metric-output"></a>Szkolenie metryki danych wyjściowych

Istnieje wiele sposobów, aby wyświetlić metryki dokładność szkolenia dla każdej iteracji wykonywania.

* Za pomocą widżetu Jupyter.
* Użyj `get_metrics()` funkcji na dowolnym `Run` obiektu.
* Wyświetlać metryki w witrynie Azure portal w eksperymencie.

### <a name="classification-metrics"></a>Metryki klasyfikacji

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

### <a name="regression-and-forecasting-metrics"></a>Regresji i przygotowywać trafniejsze prognozy metryki

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


## <a name="use-with-onnx-in-c-apps"></a>Korzystanie z ONNX w C# aplikacji

Za pomocą usługi Azure Machine Learning zautomatyzowane ML służy do tworzenia to model języka Python i go przekonwertować do formatu ONNX. Środowisko uruchomieniowe ONNX obsługuje C#, aby można było używać model tworzony automatycznie w swojej C# aplikacji bez konieczności ponownego kodowania lub żadnego opóźnienia sieciowe, które punkty końcowe REST. Spróbuj na przykład ten przepływ [tego notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatyczne uczenia Maszynowego firmy Microsoft

Automatyczne ML jest również dostępna w innych rozwiązaniach firmy Microsoft w takich jak:

|Integracje|Opis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Wybór modelu automatyczne i szkolenia w aplikacjach .NET przy użyciu programu Visual Studio i Visual Studio Code za pomocą platformy ML.NET zautomatyzowane ML (wersja zapoznawcza).|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Skalowanie w poziomie uczenie Maszynowe szkolenia zadaniami automatycznymi na platformie Spark w klastrach HDInsight równolegle.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Wywoływanie modeli uczenia maszynowego bezpośrednio w usłudze Power BI (wersja zapoznawcza).|

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładami i Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego Machine Learning:

+ Postępuj zgodnie z [samouczka: Automatycznie wytrenuj model klasyfikacji z usługą Azure automatyczne Machine Learning](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia dla eksperymentu szkolenia automatyczne:
  + W interfejsie portalu Azure [wykonaj następujące kroki](how-to-create-portal-experiments.md).
  + Za pomocą zestawu SDK języka Python [wykonaj następujące kroki](how-to-configure-auto-train.md).

+ Dowiedz się, jak można automatycznie szkolenie przy użyciu danych szeregów czasowych, [wykonaj następujące kroki](how-to-auto-train-forecast.md).

+ Wypróbuj [przykłady notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
