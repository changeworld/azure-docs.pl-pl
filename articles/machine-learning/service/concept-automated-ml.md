---
title: Co to jest zautomatyzowany ML/automl
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób usługa Azure Machine Learning może automatycznie wybierać algorytm i generować z niego model, aby zaoszczędzić czas, korzystając z podanych przez Ciebie parametrów i kryteriów, aby wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: 3fb4d953f575dcf6582f9f1c8c2a725971134159
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856257"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczna usługi machine learning?

Zautomatyzowana Uczenie maszynowe, nazywana również autoML, jest procesem automatyzacji czasochłonnych, iteracyjnych zadań tworzenia modelu uczenia maszynowego. Umożliwia ona analitykom danych, specjalistom i deweloperom tworzenie modeli ML o wysokiej skalowalności, wydajności i produktywności, a jednocześnie zapewnia wysoką jakość modelu.

Tradycyjny rozwój modelu uczenia maszynowego jest czasochłonny i wymaga znacznej wiedzy o domenie oraz czasu na wyprodukowanie i porównanie dziesiątek modeli. Zastosuj automatyczne ML, gdy chcesz, aby Azure Machine Learning szkolić i dostrajania model przy użyciu określonej metryki docelowej. Następnie usługa iteruje przez algorytmy ML sparowane z opcjami wyboru funkcji, gdzie każda iteracja tworzy model z wynikiem uczenia. Im wyższy wynik, tym lepszy model jest traktowany jako "dopasowane" do danych.

Dzięki automatycznemu uczeniu maszynowego skracasz czas potrzebny do uzyskania gotowych do produkcji modeli ML z doskonałą prostotą i wydajnością.

## <a name="when-to-use-automated-ml"></a>Kiedy używać zautomatyzowanej ML

Zautomatyzowana tablica demokratyzuje proces tworzenia modelu uczenia maszynowego i umożliwia jego użytkownikom, bez względu na swoją wiedzę o nauce danych, identyfikację kompleksowego potoku uczenia maszynowego dla każdego problemu.

Analitycy danych, analitykowie i deweloperzy w różnych branżach mogą używać zautomatyzowanych ML do:

+ Implementowanie rozwiązań uczenia maszynowego bez obszernej wiedzy programistycznej
+ Oszczędność czasu i zasobów
+ Korzystanie z najlepszych rozwiązań dotyczących analizy danych
+ Zapewnianie problemu Agile — Rozwiązywanie problemów

## <a name="how-automated-ml-works"></a>Jak działa zautomatyzowany ML

Korzystając z **usługi Azure Machine Learning**, można projektować i uruchamiać zautomatyzowane eksperymenty szkoleniowe z użyciem następujących kroków:

1. **Określ problem związany z ml** , który ma zostać rozwiązany: Klasyfikacja, prognozowanie lub regresja

1. **Określ źródło i format etykiet danych szkoleniowych**: Numpy Arrays lub Pandas Dataframe

1. **Skonfiguruj cel obliczeń dla szkolenia modelu**, takiego jak [komputer lokalny, Azure Machine Learning obliczeń, zdalnych maszyn wirtualnych lub Azure Databricks](how-to-set-up-training-targets.md).  Dowiedz się więcej o zautomatyzowanym szkoleniu [dla zasobu zdalnego](how-to-auto-train-remote.md).

1. **Skonfiguruj zautomatyzowane parametry uczenia maszynowego** , które określają, ile iteracji przekroczy różne modele, ustawienia parametrów, zaawansowane przetwarzanie wstępne/cechowania i jakie metryki mają być sprawdzane podczas określania najlepszego modelu.  Można skonfigurować ustawienia automatycznego eksperymentu szkoleniowego [w Azure Portal](how-to-create-portal-experiments.md) lub [z zestawem SDK](how-to-configure-auto-train.md).

1. **Prześlij przebieg szkoleniowy.**

  ![Automatyczne Uczenie maszynowe](./media/how-to-automated-ml/automl-concept-diagram2.png)

Podczas szkolenia usługa Azure Machine Learning tworzy wiele równoległych potoków, które wypróbuje różne algorytmy i parametry. Zostanie ona zatrzymana po trafieniu kryteriów zakończenia zdefiniowanych w eksperymentie.

Możesz również sprawdzić zarejestrowane informacje o uruchomieniu, które [zawierają metryki](how-to-understand-automated-ml.md) zebrane podczas uruchamiania. Uruchomienie szkoleniowe powoduje utworzenie serializowanego obiektu języka`.pkl` Python (plik) zawierającego model i przetwarzanie wstępne przetwarzania danych.

Chociaż Kompilowanie modelu jest zautomatyzowane, można również [dowiedzieć się, jak ważne lub istotne funkcje są](how-to-configure-auto-train.md#explain) wygenerowane modele.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Przetwarzania wstępnego

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są wstępnie przetwarzane przy użyciu metod domyślnych i opcjonalnie w ramach zaawansowanego przetwarzania wstępnego.

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="automatic-preprocessing-standard"></a>Automatyczne przetwarzanie wstępne (standard)

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są automatycznie skalowane lub znormalizowane w celu zapewnienia prawidłowego wykonywania algorytmów.  Podczas uczenia modelu jedna z następujących technik skalowania lub normalizacji zostanie zastosowana do każdego modelu.

|Normalizacja skalowania&nbsp;&&nbsp;| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ujednolicenie funkcji przez usunięcie średniej i skalowania do wariancji jednostek  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca funkcje, przeskalowane każdą funkcję według minimalnej i maksymalnej wartości tej kolumny  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skaluj każdą funkcję przez maksymalną wartość bezwzględną |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ta funkcja skalowania według ich zakresu quantile |
| [Z](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Zmniejszenie liczby liniowej przy użyciu jednoznacznej dekompozycji danych w celu ich zaprojektowania do mniejszej przestrzeni wymiarowej |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ta metoda przekształcania dokonuje redukcji liniowej, dzięki obcięciu pojedynczej dekompozycji wartości (SVD). W przeciwieństwie do UPW, ten szacowania nie Wyśrodkowuje danych przed przetwarzaniem niepojedynczej dekompozycji wartości. Oznacza to, że może wydajnie współpracować z scipy. rozrzedzonych macierzy |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każdy przykład (to oznacza, że każdy wiersz macierzy danych) z co najmniej jednym składnikiem niezerowym jest ponownie skalowany niezależnie od innych próbek, dzięki czemu jego norma (L1 lub L2) jest równa 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>Zaawansowane przetwarzanie wstępne: opcjonalne cechowania

Dostępne są również dodatkowe zaawansowane procesy przetwarzania wstępnego i cechowania, takie jak brakujące wartości, które nie są przypisywaniem, kodowaniem i transformacjem. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-create-portal-experiments.md#preprocess). Włącz to ustawienie przy użyciu:

+ Azure Portal: Zaznaczając pole wyboru preprocesora w **ustawieniach zaawansowanych** , [wykonując następujące kroki](how-to-create-portal-experiments.md).

+ Zestaw SDK języka Python: Określanie `"preprocess": True` [dla klasy`AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Tworzenie prognoz jest integralną częścią dowolnej firmy, bez względu na to, czy chodzi o dochody, spisy, sprzedaż czy zapotrzebowanie na klienta. Możesz użyć zautomatyzowanej ML do łączenia technik i podejścia i uzyskania zalecanej wysokiej jakości prognozy szeregów czasowych.

Zautomatyzowany eksperyment szeregów czasowych jest traktowany jako problem z regresją wieloczynnikowa. Poprzednie wartości serii czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi. Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnie dołączania wielu zmiennych kontekstowych i ich relacji do siebie podczas uczenia się. Zautomatyzowana ML zdobywa pojedynczy, ale często wewnętrznie rozgałęzienie modelu dla wszystkich elementów w zestawie danych i prognozowanie Horizons. W tym celu można uzyskać więcej danych w celu oszacowania parametrów modelu i generalizacji do niewidocznej serii.

Dowiedz się więcej i zobacz przykład [automatycznej uczenia maszynowego na potrzeby prognozowania szeregów czasowych](how-to-auto-train-forecast.md).

## <a name="ensemble"></a>Modele kompletów

Automatyczne Uczenie maszynowe obsługuje modele kompletów, które są domyślnie włączone. Program dblearning podnosi wyniki uczenia maszynowego i wydajność predykcyjną przez połączenie wielu modeli w przeciwieństwie do korzystania z pojedynczych modeli. Iteracje kompletka są wyświetlane jako ostateczne iteracje przebiegu. Automatyczne Uczenie maszynowe używa metody "głosowania" i "stosu" do łączenia modeli:

* **Głosowanie**: przewidywania w oparciu o średnią ważoną przewidywanych prawdopodobieństw zajęć (dla zadań klasyfikacji) lub przewidywane cele regresji (dla zadań regresji).
* **Stosowanie**: stosy łączy heterogenicznych modele i pociąga za siebie model na podstawie danych wyjściowych z poszczególnych modeli. Bieżące domyślne meta models to LogisticRegression dla zadań klasyfikacji i ElasticNet dla zadań regresji/prognozowania.

[Algorytm wyboru Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) DBZ posortowaną inicjalizacją kompletną służy do decydowania, które modele mają być używane w ramach tego modułu. Na wysokim poziomie ten algorytm inicjuje zespół z maksymalnie 5 modelami z najlepszymi wynikami i sprawdza, czy te modele znajdują się w przedziale od 5% do największej wartości, aby uniknąć słabego początkowego elementu. Następnie dla każdej iteracji, nowy model jest dodawany do istniejącej, a wynik jest obliczany. Jeśli nowy model poprawi istniejący wynik, jego kompletność zostanie zaktualizowana w celu uwzględnienia nowego modelu.

Zapoznaj się z [tematem jak](how-to-configure-auto-train.md#ensemble) zmienić domyślne ustawienia zestawu w usłudze automat Machine Learning.

## <a name="use-with-onnx-in-c-apps"></a>Używanie z ONNX w C# aplikacjach

Za pomocą Azure Machine Learning można użyć zautomatyzowanej ML do skompilowania modelu języka Python i przekonwertowania go na format ONNX. Środowisko uruchomieniowe ONNX C#obsługuje, dzięki czemu można użyć modelu skompilowanego automatycznie w C# aplikacjach bez konieczności ponownego kodowania lub dowolnych opóźnień sieci, które wprowadzają punkty końcowe Rest. Wypróbuj przykład tego przepływu [w tym notesie Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatyczna ML w firmie Microsoft

Zautomatyzowana ML jest również dostępna w innych rozwiązaniach firmy Microsoft, takich jak:

|Integracje|Opis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatyczny wybór modelu i szkolenia w aplikacjach .NET przy użyciu programu Visual Studio i Visual Studio Code ze ML.NET zautomatyzowanej ML (wersja zapoznawcza).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Równolegle Skaluj swoje zautomatyzowane zadania szkoleniowe dotyczące platformy Spark w klastrach usługi HDInsight.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Wywołaj modele uczenia maszynowego bezpośrednio w Power BI (wersja zapoznawcza).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Twórz nowe modele uczenia maszynowego za pośrednictwem danych w klastrach danych Big Data w SQL Server 2019.|

## <a name="next-steps"></a>Kolejne kroki

Zobacz przykłady i Dowiedz się, jak tworzyć modele przy użyciu automatycznego uczenia maszynowego:

+ Postępuj [zgodnie z samouczkiem: Automatyczne uczenie modelu regresji przy użyciu automatycznego Machine Learning platformy Azure](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia dla eksperymentu automatycznego szkolenia:
  + W Azure Portal interfejsie [wykonaj te kroki](how-to-create-portal-experiments.md).
  + Za pomocą zestawu SDK języka Python [wykonaj te kroki](how-to-configure-auto-train.md).

+ Dowiedz się, jak korzystać z funkcji autouczenia przy użyciu danych szeregów czasowych, [wykonując następujące kroki](how-to-auto-train-forecast.md).

+ Wypróbuj [Jupyter Notebook przykłady w celu automatycznego uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
