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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9736cc3ab20d43cc3731bc237bed9eb9b5370cb4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800784"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczna usługi machine learning?

Uczenie maszynowe automatycznego, nazywana także AutoML, umożliwia analitykom danych, analitycy i deweloperom tworzyć modele uczenia Maszynowego, o dużej skali, wydajności i produktywności, jednocześnie zatwierdzeniu jakość modelu. 

ML zautomatyzowane kompilacje zestaw modeli uczenia Maszynowego automatycznie, inteligentnie wybranie modeli na potrzeby szkolenia, a następnie zaleca najlepszy dla siebie. Tradycyjne usługi machine learning model programowania jest mocno obciążających wymagających wiedzy specjalistycznej znaczące i czas tworzenia i porównywania wielu modeli. Automatyczne ml będzie Przyspiesz czas potrzebny do pobrania modeli uczenia Maszynowego gotowe do produkcji i doskonałą wydajność.

W tle danych szkoleniowych jest wykonywane przy użyciu funkcji zdefiniowanego celu i inteligentnie postanowiliśmy po za pomocą kombinacji algorytmów uczenia Maszynowego i zestawu wybranych funkcji. Następnie oparte na wyniki szkolenia, najlepiej dopasowanego modelu jest zidentyfikowany i zalecane dla Ciebie. 

Nadal masz kontroli nad eksperymentu i przejrzyste co się dzieje. Można zdefiniować ograniczenia i eksperymentów na podstawie czasu, dokładności lub liczba iteracji, na przykład cele. Zobaczysz każdy model, który został wygenerowany swojego eksperymentu szkolenia przepływ dla każdej iteracji i najbardziej wpływowi funkcji w danym modelu.

## <a name="how-automated-ml-works"></a>Jak automatyczne działa ML

Za pomocą **usługi Azure Machine Learning**, można projektować i uruchamiać zautomatyzowane eksperymentów uczenia Maszynowego szkolenia z następujące kroki:

1. **Zidentyfikować ML problem** które należy rozwiązać: klasyfikacji, funkcja prognozowania lub regresji
   
1. **Określ źródło i format danych szkoleniowych etykietami**: Tablice Numpy lub Pandas dataframe

1. **Skonfiguruj cel obliczeń do trenowania modelu**, takich jak usługi [komputera lokalnego, Azure Machine Learning oblicza, zdalnych maszynach wirtualnych lub usługi Azure Databricks](how-to-set-up-training-targets.md).  Dowiedz się więcej o automatycznych szkolenia [do zdalnego zasobu](how-to-auto-train-remote.md).

1. **Skonfiguruj automatyczne maszynę uczenia parametry** określające, jak dużo iteracji przez różne modele, ustawienia hiperparametrycznego zaawansowane przetwarzanie wstępne cechowania i jakie metryki mają Przyjrzyj się podczas określania najlepszy model.  Można skonfigurować ustawienia dla eksperymentu szkolenia automatyczne [w witrynie Azure portal](how-to-create-portal-experiments.md) lub [z zestawem SDK](how-to-configure-auto-train.md).

1. **Prześlij szkolenia uruchomienia.** 


[![Automatyczne usługi Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Podczas szkolenia, usługi Azure Machine Learning, powstaje coraz potoki, którzy spróbują różnych algorytmów i parametrów. Zatrzyma się po trafienia kryteriów zakończenia zdefiniowanych w eksperymencie. 

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
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    Możesz zmieniać skalę każdej funkcji, maksymalna wartość bezwzględna |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   Tej funkcji programu Scaler przy ich zakres kwantyl |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | Liniowy wymiarowości przy użyciu pojedynczej rozkładu wartości danych do projektu go na niższych wymiarowej | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    Transformer to wykonuje liniowej wymiarowości przy użyciu rozkładu obciętą wartość pojedynczej (SVD). Sprzecznie UPW to narzędzie do szacowania nie centrum danych przed obliczeń dekompozycji pojedynczej wartości. Oznacza to, że może współpracować z macierzy scipy.sparse efektywnie | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każda próbka (czyli każdy wiersz macierzy danych) z co najmniej jeden składnik różna od zera jest przeskalowywany w ten sposób niezależnie od innych przykładów, aby równa jego norm (P1 lub P2) | 

### <a name="advanced-preprocessing-optional-featurization"></a>Zaawansowane przetwarzanie wstępne: cechowania opcjonalne

Przetwarzanie wstępne zaawansowanej dodatkowe i cechowania są również dostępne, takich jak brakujące wartości, przypisywania, kodowanie i przekształcenia. [Dowiedz się więcej o jakie cechowania jest dołączony](how-to-create-portal-experiments.md#preprocess). Włącz to ustawienie za pomocą:
+ Witryna Azure Portal: Wybieranie **wstępnie Przetwórz** pola wyboru w **Zaawansowane ustawienia** [za pomocą tych kroków](how-to-create-portal-experiments.md). 
+ Python SDK: Określanie `"preprocess": True` dla [ `AutoMLConfig` klasy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modele zespołu

Możesz uczyć modele zespołu przy użyciu usługi uczenie maszynowe automatycznych przy użyciu [algorytm wybór zespołu Caruana przy użyciu posortowanych inicjowania zespołu](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Learning zespołu zwiększa machine learning wyniki i wydajność predykcyjne, łącząc wiele modeli, w przeciwieństwie do używania jednego modeli. Iteracji zespół jest wyświetlana jako ostatniej iteracji przebieg.

## <a name="use-with-onnx-in-c-apps"></a>Korzystanie z ONNX w C# aplikacji

Za pomocą usługi Azure Machine Learning zautomatyzowane ML służy do tworzenia to model języka Python i go przekonwertować do formatu ONNX. Środowisko uruchomieniowe ONNX obsługuje C#, aby można było używać model tworzony automatycznie w swojej C# aplikacji bez konieczności ponownego kodowania lub żadnego opóźnienia sieciowe, które punkty końcowe REST. Spróbuj na przykład ten przepływ [tego notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatyczne uczenia Maszynowego firmy Microsoft

Automatyczne ML jest również dostępna w innych rozwiązaniach firmy Microsoft w takich jak:
+ W aplikacjach .NET przy użyciu programu Visual Studio i Visual Studio Code za pomocą [strukturze ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)
+ [Na HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), gdzie możesz skalować w poziomie ML szkolenia zadaniami automatycznymi na platformie Spark w klastrach HDInsight równolegle. 
+ [W usłudze Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładami i Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego Machine Learning:

+ Postępuj zgodnie z [samouczka: Automatycznie wytrenuj model klasyfikacji z usługą Azure automatyczne Machine Learning](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia dla eksperymentu szkolenia automatyczne: 
   + W interfejsie portalu Azure [wykonaj następujące kroki](how-to-create-portal-experiments.md).
   + Za pomocą zestawu SDK języka Python [wykonaj następujące kroki](how-to-configure-auto-train.md).
  
 + Dowiedz się, jak można automatycznie szkolenie przy użyciu danych szeregów czasowych, [wykonaj następujące kroki](how-to-auto-train-forecast.md).

+ Wypróbuj [przykłady notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
