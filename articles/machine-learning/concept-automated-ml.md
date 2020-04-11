---
title: Co to jest zautomatyzowany ML / AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usługa Azure Machine Learning może automatycznie wybrać algorytm dla Ciebie i wygenerować model z niego, aby zaoszczędzić czas przy użyciu parametrów i kryteriów, które podasz, aby wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 095561f02fdeff6688b78d69cc1becc4ee0f8901
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115208"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest zautomatyzowane uczenie maszynowe?

Zautomatyzowane uczenie maszynowe, zwane również zautomatyzowanym ml, to proces automatyzacji czasochłonnych, iteracyjnych zadań tworzenia modelu uczenia maszynowego. Umożliwia analitykom danych, analitykom i deweloperom tworzenie modeli uczenia maszynowego o wysokiej skali, wydajności i produktywności przy jednoczesnym utrzymaniu jakości modelu. Automated ML opiera się na przełomie z naszego [działu Microsoft Research.](https://arxiv.org/abs/1705.05355)

Tradycyjne opracowywanie modeli uczenia maszynowego wymaga dużej ilości zasobów, co wymaga znacznej wiedzy i czasu na tworzenie i porównywanie dziesiątek modeli. Dzięki zautomatyzowanemu uczeniu maszynowemu przyspieszysz czas potrzebny na uzyskanie gotowych do produkcji modeli uczenia maszynowego z dużą łatwością i wydajnością.

## <a name="when-to-use-automated-ml"></a>Kiedy używać automatycznego ML

Zastosuj automatyczne ML, jeśli chcesz, aby usługa Azure Machine Learning szkoliła i dostroiła model przy użyciu określonej metryki docelowej. Zautomatyzowane ml demokratyzuje proces tworzenia modelu uczenia maszynowego i umożliwia użytkownikom, bez względu na ich wiedzy na temat nauki o danych, identyfikowanie kompleksowego potoku uczenia maszynowego dla każdego problemu.

Analitycy danych, analitycy i deweloperzy z różnych branż mogą używać zautomatyzowanej liczby danych w celu:

+ Wdrażanie rozwiązań uczenia maszynowego bez dogłębnej wiedzy na temat programowania
+ Oszczędność czasu i zasobów
+ Wykorzystanie najlepszych praktyk w zakresie nauki o danych
+ Zapewnij elastyczne rozwiązywanie problemów

W poniższej tabeli wymieniono typowe przypadki użycia zautomatyzowanych ml. 

Klasyfikacja| Prognozowanie szeregów czasowych | Regresja
---|---|---
[Wykrywanie oszustw](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prognozowanie sprzedaży](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Przewidywanie wydajności procesora](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Przewidywanie marketingu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prognozowanie popytu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Klasyfikacja danych grupy dyskusyjnej](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Prognoza produkcji napojów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>Projektowanie zautomatyzowanych eksperymentów ML

Korzystając z **usługi Azure Machine Learning,** można zaprojektować i uruchomić eksperymenty szkoleniowe zautomatyzowanego uczenia maszynowego za pomocą następujących kroków:

1. **Identyfikowanie problemu ml** do rozwiązania: klasyfikacja, prognozowanie lub regresja

1. **Określ źródło i format oznaczonych danych szkoleniowych:** Tablice numpy lub ramka danych Pandas

1. **Skonfiguruj cel obliczeniowy dla szkolenia modelu,** na przykład [komputer lokalny, obliczenia usługi Azure Machine Learning, zdalne maszyny wirtualne lub usługi Azure Databricks.](how-to-set-up-training-targets.md)  Dowiedz się więcej o zautomatyzowanych [szkoleniach dotyczących zasobu zdalnego.](how-to-auto-train-remote.md)

1. **Skonfiguruj parametry automatycznego uczenia maszynowego,** które określają, ile iteracji w różnych modelach, ustawieniach hiperparametru, zaawansowanym przetwarzaniem wstępnym/featurization i jakie metryki należy zwrócić uwagę podczas określania najlepszego modelu.  Ustawienia automatycznego eksperymentu szkoleniowego można skonfigurować w [usłudze Azure Machine Learning studio](https://ml.azure.com)lub przy pomocy [zestawu SDK.](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Prześlij przebieg szkolenia.**

## <a name="how-automated-ml-works"></a>Jak działa zautomatyzowane ML

Podczas szkolenia usługa Azure Machine Learning tworzy szereg potoków równoległych, które próbują różnych algorytmów i parametrów. Usługa iteruje za pomocą algorytmów ML w połączeniu z wyboru funkcji, gdzie każda iteracja tworzy model z wynikiem szkolenia. Im wyższy wynik, tym lepiej model jest uważany za "dopasowanie" danych.  Zatrzyma się, gdy trafi kryteria wyjścia zdefiniowane w eksperymencie. Na poniższym diagramie przedstawiono ten proces. 

  ![Zautomatyzowane uczenie maszynowe](./media/concept-automated-ml/automl-concept-diagram2.png)


Można również sprawdzić informacje o przebiegu rejestrowane, który [zawiera metryki](how-to-understand-automated-ml.md) zebrane podczas uruchamiania. Przebieg szkoleniowy tworzy python serialized`.pkl` obiektu (plik), który zawiera model i dane przetwarzania wstępnego.

Podczas tworzenia modelu jest zautomatyzowany, można również [dowiedzieć się, jak ważne lub istotne funkcje są](how-to-configure-auto-train.md#explain) do generowanych modeli.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Wstępnego przetwarzania

W każdym zautomatyzowanym eksperymencie uczenia maszynowego dane są wstępnie przetwarzane przy użyciu metod domyślnych i opcjonalnie za pomocą zaawansowanego przetwarzania wstępnego.

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na numeryczne itp.) stają się częścią modelu źródłowego. Podczas korzystania z modelu dla prognoz, te same kroki przetwarzania wstępnego stosowane podczas szkolenia są stosowane do danych wejściowych automatycznie.

### <a name="automatic-preprocessing-standard"></a>Automatyczne przetwarzanie wstępne (standard)

W każdym zautomatyzowanym eksperymencie uczenia maszynowego dane są automatycznie skalowane lub normalizowane, aby pomóc algorytmom działać dobrze.  Podczas szkolenia modelu jeden z następujących skalowania lub normalizacji techniki zostaną zastosowane do każdego modelu.

|Normalizacja&nbsp;&&nbsp;skalowania| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standaryzowanie operacji przez usunięcie średniej i skalowanie do odchylenia jednostki  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca obiekty, skalując każdą operację według minimalnej i maksymalnej kolumny  |
| [MaxAbsScaler (MaxAbsScaler)](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skaluj każdą operację według jej maksymalnej wartości bezwzględnej |
| [Solidnaskalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ten skaler charakteryzuje się zakresem ilościowym |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Liniowa redukcja wymiarowości przy użyciu rozkładu wartości pojedynczej danych w celu rzutowanie ich do dolnej przestrzeni wymiarowej |
| [ObciętySVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ten transformator wykonuje liniową redukcję wymiarowości za pomocą obciętego rozkładu wartości pojedynczej (SVD). W przeciwieństwie do PCA, ten estymator nie wyśrodkowa danych przed obliczeniem rozkładu wartości pojedynczej, co oznacza, że może pracować z matrycami scipy.sparse wydajnie |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każda próbka (czyli każdy wiersz macierzy danych) z co najmniej jednym składnikiem niezerowym jest przeskakowana niezależnie od innych próbek, tak aby jej norma (l1 lub l2) była równa jednemu |

### <a name="advanced-preprocessing-optional-featurization"></a>Zaawansowane przetwarzanie wstępne: opcjonalna featurization

Dostępne są również dodatkowe zaawansowane przetwarzanie wstępne i featurization, takie jak barierki danych, kodowanie i przekształcenia. [Dowiedz się więcej o tym, co featurization jest wliczone](how-to-use-automated-ml-for-ml-models.md#featurization)w cenę . Włącz to ustawienie za pomocą:

+ Studio usługi Azure Machine Learning: Włącz **automatyczne featurization** w sekcji **Wyświetl dodatkową konfigurację** z tymi [krokami](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ SDK języka Python: Określanie `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` [ `AutoMLConfig` dla klasy](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="classification--regression"></a>Klasyfikacja & regresja

Klasyfikacja i regresja są najczęstszymi typami zadań uczenia maszynowego. Oba są rodzajami nadzorowanego uczenia się, w których modele uczą się przy użyciu danych szkoleniowych i stosują te nauki do nowych danych. Usługa Azure Machine Learning oferuje featurizations specjalnie dla tych zadań, takich jak głębokie sieci neuronowe featurizers tekstu do klasyfikacji. Dowiedz się więcej o [opcjach featurization](how-to-use-automated-ml-for-ml-models.md#featurization). 

Głównym celem modeli klasyfikacji jest przewidywanie, do których kategorii wpadną nowe dane na podstawie wniosków z danych szkoleniowych. Typowe przykłady klasyfikacji obejmują wykrywanie oszustw, rozpoznawanie pisma ręcznego i wykrywanie obiektów.  Dowiedz się więcej i zobacz przykład [klasyfikacji z automatycznym uczeniem maszynowym](tutorial-train-models-with-aml.md).

Różni się od klasyfikacji, w której przewidywane wartości wyjściowe są kategoryczne, modele regresji przewidują wartości danych liczbowych na podstawie niezależnych predyktorów. W regresji celem jest pomoc w ustanowieniu relacji między tymi niezależnymi zmiennymi predykcyjnymi, oceniając, jak jedna zmienna wpływa na inne. Na przykład, cena samochodu na podstawie funkcji, takich jak przebieg gazu, ocena bezpieczeństwa, itp. Dowiedz się więcej i zobacz przykład [regresji dzięki zautomatyzowanemu uczeniu maszynowemu.](tutorial-auto-train-models.md)

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Prognozy budynków są integralną częścią każdej firmy, niezależnie od tego, czy chodzi o przychody, zapasy, sprzedaż czy zapotrzebowanie klientów. Za pomocą zautomatyzowanej funkcji uczenia maszynowego można łączyć techniki i podejścia oraz uzyskać zalecaną prognozę serii czasowych wysokiej jakości.

Eksperyment automatycznych szeregów czasowych jest traktowany jako problem regresji wielowymiarowej. Przeszłe wartości szeregów czasowych są "przestawne", aby stać się dodatkowymi wymiarami dla regressora wraz z innymi predyktorami. Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma tę zaletę, że naturalnie uwzględnia wiele zmiennych kontekstowych i ich wzajemne relacje podczas treningu. Automatyczna funkcja uczenia maszynowego uczy się pojedynczego, ale często wewnętrznie rozgałęziony model dla wszystkich elementów w zestawie danych i horyzontów prognozowania. W związku z tym dostępnych jest więcej danych do oszacowania parametrów modelu i uogólnienie do niewidocznych serii staje się możliwe.

Dowiedz się więcej i zobacz przykład [automatycznego uczenia maszynowego do prognozowania szeregów czasowych.](how-to-auto-train-forecast.md) Możesz też zapoznać się z [notesem zapotrzebowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) na energię, aby uzyskać szczegółowe przykłady kodu zaawansowanej konfiguracji prognozowania, w tym:

* wykrywanie i featurization wakacje
* szeregi czasowe i osoby uczące się DNN (Auto-ARIMA, Prorok, ForecastTCN)
* wiele modeli obsługuje poprzez grupowanie
* Walidacja krzyżowa pochodzenia toczenia
* konfigurowalne opóźnienia
* funkcje agregacji okien tocznych

## <a name="ensemble-models"></a><a name="ensemble"></a>Modele zespołowe

Automatyczne uczenie maszynowe obsługuje modele zespołu, które są domyślnie włączone. Uczenie zespołu poprawia wyniki uczenia maszynowego i wydajność predykcyjną, łącząc wiele modeli, w przeciwieństwie do korzystania z pojedynczych modeli. Iteracje zespołu są wyświetlane jako końcowe iteracje przebiegu. Zautomatyzowane uczenie maszynowe wykorzystuje zarówno metody głosowania, jak i układania w stosy do łączenia modeli:

* **Głosowanie:** przewiduje na podstawie średniej ważonej przewidywanych prawdopodobieństw klasowych (dla zadań klasyfikacyjnych) lub przewidywanych celów regresji (dla zadań regresji).
* **Układanie:** układanie łączy w sobie heterogeniczne modele i trenuje meta-model na podstawie danych wyjściowych z poszczególnych modeli. Bieżące domyślne meta-modele to LogisticRegression dla zadań klasyfikacji i ElasticNet dla zadań regresji/prognozowania.

[Algorytm wyboru zespołu Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) z inicjacją posortowanego zespołu służy do decydowania, które modele mają być używane w zespole. Na wysokim poziomie ten algorytm inicjuje zespół z maksymalnie pięciu modeli z najlepszych wyników indywidualnych i sprawdza, czy te modele są w granicach 5% progu najlepszego wyniku, aby uniknąć słabego początkowego zespołu. Następnie dla każdej iteracji zespołu nowy model jest dodawany do istniejącego zespołu i obliczana jest wynik wynikowy. Jeśli nowy model poprawił istniejącą partyturę zespołu, zespół zostanie zaktualizowany w celu uwzględnienia nowego modelu.

Zobacz [instrukcje dotyczące](how-to-configure-auto-train.md#ensemble) zmiany domyślnych ustawień zespołu w zautomatyzowanym uczeniu maszynowym.

## <a name="use-with-onnx"></a>Używanie z ONNX

Za pomocą usługi Azure Machine Learning można użyć zautomatyzowanego uczenia maszynowego do tworzenia modelu języka Python i konwersji na format ONNX. Gdy modele są w formacie ONNX, mogą być uruchamiane na różnych platformach i urządzeniach. Dowiedz się więcej o [przyspieszaniu modeli ml za pomocą ONNX.](concept-onnx.md)

Zobacz, jak przekonwertować format ONNX [w tym przykładzie notebooka Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Dowiedz się, które [algorytmy są obsługiwane w ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Środowisko wykonawcze ONNX obsługuje również C#, dzięki czemu można użyć modelu utworzonego automatycznie w aplikacjach języka C# bez konieczności przekodowywania lub żadnych opóźnień sieci, które wprowadzają punkty końcowe REST. Dowiedz się więcej o [wnioskowanie modeli ONNX za pomocą interfejsu API środowiska wykonawczego ONNX C#.](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md) 

## <a name="automated-ml-in-azure-machine-learning"></a>Automatyczne uczenia maszynowe w usłudze Azure Machine Learning

Usługa Azure Machine Learning oferuje dwa środowiska do pracy z automatycznym ml

* Dla klientów korzystających z kodu, [zestaw SDK języka Python usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Aby uzyskać ograniczoną/braku dostępu do kodu, studio usługi Azure Machine Learning[https://ml.azure.com](https://ml.azure.com/)  

Poniżej podsumowano funkcje automatycznego uczenia maszynowego wysokiego poziomu obsługiwane w każdym doświadczeniu.

<a name="parity"></a>

### <a name="experiment-settings"></a>Ustawienia eksperymentu 

Poniższe ustawienia umożliwiają skonfigurowanie eksperymentu automatycznego uczenia maszynowego. 

| | Zestaw SDK dla języka Python| Studio
----|:----:|:----:
Dzielenie danych na zestawy pociągów/sprawdzania poprawności| ✓|✓
Obsługuje zadania uczenia maszynowego: klasyfikacja, regresja i prognozowanie| ✓| ✓
Optymalizuje na podstawie metryki podstawowej| ✓| ✓
Obsługa obliczeń AML jako celu obliczeniowego | ✓|✓
Konfigurowanie horyzontu prognozy, docelowe opóźnienia & oknie kroczącym|✓|✓
Ustawianie kryteriów wyjścia |✓|✓ 
Ustawianie równoczesnych iteracji| ✓|✓
Kolumny upuszczania| ✓|✓
Algorytmy blokowe|✓|✓
Sprawdzanie poprawności krzyżowej |✓|✓
Obsługa szkoleń dotyczących klastrów usługi Azure Databricks| ✓|
Wyświetlanie zaprojektowanych nazw operacji|✓|
Podsumowanie featurization| ✓|
Wakacyjna realizacja|✓|
Poziom szczegółowości dla plików dziennika| ✓|

### <a name="model-settings"></a>Ustawienia modelu

Te ustawienia mogą być stosowane do najlepszego modelu w wyniku eksperymentu automatycznego uczenia maszynowego.

||Zestaw SDK dla języka Python|Studio
----|:----:|:----:
Najlepsza rejestracja modelu| ✓|✓
Najlepsze wdrożenie modelu| ✓| ✓
Najlepsza wytłumaność modelu| ✓|✓
Włączanie modeli zespołów & stosów w zespole do głosowania| ✓|✓
Pokaż najlepszy model na podstawie danych innych niż podstawowe|✓|
Włączanie/wyłączanie zgodności modelu ONNX|✓|
Testowanie modelu | ✓| |

### <a name="run-control-settings"></a>Uruchamianie ustawień sterowania

Te ustawienia umożliwiają przeglądanie i kontrolowanie przebiegów eksperymentu i jego przebiegów podrzędnych. 

||Zestaw SDK dla języka Python| Studio
----|:----:|:----:
Uruchamianie tabeli podsumowania| ✓|✓
Anuluj uruchomienie| ✓|✓
Anuluj uruchomienie podrzędne| ✓| ✓
Zdobądź barierki ochronne| ✓|✓
Wstrzymaj bieg| ✓| 
Wznów bieg| ✓| 

## <a name="next-steps"></a>Następne kroki

Zobacz przykłady i dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego uczenia maszynowego:

+ Postępuj zgodnie [z samouczkiem: Automatyczne szkolenie modelu regresji za pomocą usługi Azure Machine Learning](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia eksperymentu automatycznego szkolenia:
  + W studiu usługi Azure Machine Learning [wykonaj następujące kroki.](how-to-use-automated-ml-for-ml-models.md)
  + W przypadku modułu SDK języka Python [wykonaj następujące kroki](how-to-configure-auto-train.md).

+ Dowiedz się, jak automatycznie trenować przy użyciu danych szeregów czasowych, [wykonaj następujące kroki](how-to-auto-train-forecast.md).

+ Wypróbuj [próbki notebooka Jupyter do automatycznego uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Automatyczne ml jest również dostępne w innych rozwiązaniach firmy Microsoft, takich jak [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) i SQL [Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
