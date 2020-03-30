---
title: Modelowanie metod szkoleniowych
titleSuffix: Azure Machine Learning
description: Poznaj różne metody, których można użyć do uczenia modelu za pomocą usługi Azure Machine Learning. Estymatory zapewniają łatwy sposób pracy z popularnych platform, takich jak Scikit-learn, TensorFlow, Keras, PyTorch i Chainer. Potoki uczenia maszynowego ułatwiają planowanie nienadzorowanych przebiegów, używanie heterogenicznych środowisk obliczeniowych i ponowne wykorzystywanie części przepływu pracy. I uruchomić konfiguracje zapewniają szczegółową kontrolę nad obiekty docelowe obliczeń, na których działa proces szkolenia.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129769"
---
# <a name="train-models-with-azure-machine-learning"></a>Trenuj modele za pomocą usługi Azure Machine Learning

Usługa Azure Machine Learning oferuje kilka sposobów uczenia modeli, od rozwiązań kodu przy użyciu zestawu SDK do rozwiązań o niskim poziomie kodu, takich jak zautomatyzowane uczenie maszynowe i projektant wizualny. Użyj poniższej listy, aby określić, która metoda treningu jest dla Ciebie odpowiednia:

+ [Zestaw SDK usługi Azure Machine Learning dla języka Python:](#python-sdk)zestaw SDK języka Python udostępnia kilka sposobów uczenia modeli, z których każdy ma różne możliwości.

    | Metoda treningowa | Opis |
    | ----- | ----- |
    | [Uruchom konfigurację](#run-configuration) | **Ogólny sposób szkolenia modeli** jest użycie skryptu szkoleniowego i uruchomić konfigurację. Konfiguracja uruchamiania zawiera informacje potrzebne do skonfigurowania środowiska szkoleniowego używanego do trenowania modelu. Można wziąć konfigurację uruchamiania, skrypt szkolenia i cel obliczeniowy (środowisko szkoleniowe) i uruchomić zadanie szkoleniowe. |
    | [Zautomatyzowane uczenie maszynowe](#automated-machine-learning) | Zautomatyzowane uczenie maszynowe pozwala **szkolić modele bez rozbudowanej wiedzy na temat nauki o danych lub programowania.** Dla osób z analityką danych i środowiskiem programowania zapewnia sposób na zaoszczędzenie czasu i zasobów poprzez automatyzację wyboru algorytmów i dostrajania hiperparametryczne. Nie musisz się martwić o definiowanie konfiguracji uruchamiania podczas korzystania z automatycznego uczenia maszynowego. |
    | [Estymatorów](#estimators) | Klasy estymatora **ułatwiają szkolenie modeli w oparciu o popularne struktury uczenia maszynowego.** Istnieją klasy estymatora dla **Scikit-learn**, **PyTorch**, **TensorFlow**i **Chainer**. Istnieje również ogólny estymator, który może być używany z frameworkami, które nie mają jeszcze dedykowanej klasy estymatora. Nie musisz się martwić o definiowanie konfiguracji uruchamiania podczas korzystania z estymatorów. |
    | [Potok uczenia maszynowego](#machine-learning-pipeline) | Potoki nie są inną metodą szkoleniową, ale **sposobem definiowania przepływu pracy przy użyciu modułowych kroków wielokrotnego użycia,** które mogą obejmować szkolenie w ramach przepływu pracy. Potoki uczenia maszynowego obsługują automatyczne uczenie maszynowe, estymatory i uruchamianie konfiguracji do uczenia modeli. Ponieważ potoki nie koncentrują się w szczególności na szkoleniu, przyczyny korzystania z potoku są bardziej zróżnicowane niż inne metody szkolenia. Ogólnie rzecz biorąc można użyć potoku, gdy:<br>* Chcesz **zaplanować procesy bez nadzoru,** takie jak długotrwałe zadania szkoleniowe lub przygotowanie danych.<br>* Użyj **wielu kroków,** które są koordynowane w różnych heterogenicznych zasobów obliczeniowych i lokalizacji pamięci masowej.<br>* Użyj potoku jako **szablonu wielokrotnego użytku** dla określonych scenariuszy, takich jak przekwalifikowanie lub ocenianie partii.<br>* **Śledzenie i wersja źródeł danych, danych wejściowych i wyjściowych** dla przepływu pracy.<br>* Przepływ pracy jest **realizowany przez różne zespoły, które pracują na określonych krokach niezależnie.** Kroki można następnie połączyć w potoku, aby zaimplementować przepływ pracy. |

+ [Zestaw SDK usługi Azure Machine Learning dla języka Python:](#r-sdk)zestaw SDK używa pakietu reticulate do powiązania z zestawem SDK języka Python usługi Azure Machine Learning. Dzięki temu można uzyskać dostęp do podstawowych obiektów i metod zaimplementowanych w module SDK języka Python z dowolnego środowiska języka R.

+ **Projektant:** Projektant usługi Azure Machine Learning (wersja zapoznawcza) zapewnia łatwy punkt wejścia do uczenia maszynowego w celu tworzenia weryfikacji koncepcji lub dla użytkowników z niewielkim doświadczeniem w kodowaniu. Umożliwia trenowanie modeli przy użyciu interfejsu użytkownika opartego na przeciąganiu i upuszczaniu w sieci Web. Można użyć kodu Języka Python jako część projektu lub pociągów modeli bez pisania kodu.

+ **CLI:** Narzędzie polecenia interfejsu wiersza uczenia maszynowego udostępnia polecenia dla typowych zadań za pomocą usługi Azure Machine Learning i jest często używane do **skryptów i automatyzacji zadań.** Na przykład po utworzeniu skryptu szkoleniowego lub potoku można użyć interfejsu wiersza polecenia, aby rozpocząć szkolenie zgodnie z harmonogramem lub po zaktualizowaniu plików danych używanych do szkolenia. W przypadku modeli szkoleniowych udostępnia polecenia, które przesyłają zadania szkoleniowe. Może przesyłać zadania przy użyciu konfiguracji uruchamiania lub potoków.

Każda z tych metod szkolenia można użyć różnych typów zasobów obliczeniowych do szkolenia. Łącznie zasoby te są określane jako [__cele obliczeniowe__](concept-azure-machine-learning-architecture.md#compute-targets). Celem obliczeniowym może być maszyna lokalna lub zasób w chmurze, taki jak usługa Azure Machine Learning Compute, usługa Azure HDInsight lub zdalna maszyna wirtualna.

## <a name="python-sdk"></a>Zestaw SDK dla języka Python

Zestaw SDK usługi Azure Machine Learning dla języka Python umożliwia tworzenie i uruchamianie przepływów pracy uczenia maszynowego za pomocą usługi Azure Machine Learning. Można wchodzić w interakcje z usługą z interaktywnej sesji języka Python, Notesy Jupyter, Visual Studio Code lub innych IDE.

* [Co to jest zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalowanie/aktualizowanie zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurowanie środowiska programistycznego dla usługi Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Uruchom konfigurację

Ogólne zadanie szkoleniowe za pomocą usługi Azure Machine Learning można zdefiniować za pomocą [funkcji RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Następnie używana jest konfiguracja uruchamiania wraz ze skryptami szkoleniowymi do szkolenia modelu na podstawie celu obliczeniowego.

Można rozpocząć od konfiguracji uruchamiania dla komputera lokalnego, a następnie przełączyć się do jednego dla obiektu docelowego obliczeń opartych na chmurze w razie potrzeby. Podczas zmiany obiektu docelowego obliczeń można tylko zmienić używaną konfigurację uruchamiania. Uruchom rejestruje również informacje o zadaniu szkoleniowym, takie jak dane wejściowe, dane wyjściowe i dzienniki.

* [Co to jest konfiguracja uruchamiania?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Samouczek: Trenuj swój pierwszy model ML](tutorial-1st-experiment-sdk-train.md)
* [Przykłady: Przykłady modeli szkoleniowych z notesu Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Jak: Konfigurowanie i używanie celów obliczeniowych do szkolenia modelu](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Zdefiniuj iteracje, ustawienia hiperparametryczne, featurization i inne ustawienia. Podczas szkolenia usługa Azure Machine Learning próbuje równolegle różnych algorytmów i parametrów. Szkolenie zatrzymuje się, gdy osiągnie zdefiniowane kryteria wyjścia. Nie musisz się martwić o definiowanie konfiguracji uruchamiania podczas korzystania z estymatorów.

> [!TIP]
> Oprócz zestawu SDK języka Python można również korzystać z automatycznego uczenia maszynowego za pośrednictwem [usługi Azure Machine Learning studio.](https://ml.azure.com)

* [Co to jest zautomatyzowane uczenie maszynowe?](concept-automated-ml.md)
* [Samouczek: Tworzenie pierwszego modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md)
* [Samouczek: Użyj automatycznego uczenia maszynowego, aby przewidzieć ceny taksówek](tutorial-auto-train-models.md)
* [Przykłady: przykłady notesów jupyter do automatycznego uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Jak: Konfigurowanie zautomatyzowanych eksperymentów uczenia maszynowego w języku Python](how-to-configure-auto-train.md)
* [Jak: Automatyczne szkolenie modelu prognozy szeregów czasowych](how-to-auto-train-forecast.md)
* [Jak: Tworzenie, eksplorowanie i wdrażanie zautomatyzowanych eksperymentów uczenia maszynowego za pomocą usługi Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Estymatorów

Estymatory ułatwiają szkolenie modeli przy użyciu popularnych struktur uczenia maszynowego. Jeśli używasz **Scikit-learn**, **PyTorch**, **TensorFlow**lub **Chainer**, należy rozważyć użycie estymatora do szkolenia. Istnieje również ogólny estymator, który może być używany z frameworkami, które nie mają jeszcze dedykowanej klasy estymatora. Nie musisz się martwić o definiowanie konfiguracji uruchamiania podczas korzystania z estymatorów.

* [Co to są estymatory?](concept-azure-machine-learning-architecture.md#estimators)
* [Samouczek: Trenuj modele klasyfikacji obrazów za pomocą danych MNIST i scikit-learn przy użyciu usługi Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Przykłady: Przykłady notesu Jupyter przy użyciu estymatorów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Jak: Tworzenie estymatorów w szkoleniu](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Potok uczenia maszynowego

Potoki uczenia maszynowego można użyć wcześniej wymienionych metod szkoleniowych (uruchom konfigurację, estymatory i automatyczne uczenie maszynowe). Potoki są bardziej o tworzeniu przepływu pracy, więc obejmują one więcej niż tylko szkolenia modeli. W potoku można szkolić model przy użyciu automatycznego uczenia maszynowego, estymatorów lub uruchamiania konfiguracji.

* [Co to są potoki ml w usłudze Azure Machine Learning?](concept-ml-pipelines.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK usługi Azure Machine Learning](how-to-create-your-first-pipeline.md)
* [Samouczek: Używanie potoków usługi Azure Machine Learning do oceniania wsadowego](tutorial-pipeline-batch-scoring-classification.md)
* [Przykłady: przykłady notesu jupyter dla potoków uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Przykłady: Potok z automatycznym uczeniem maszynowym](https://aka.ms/pl-automl)
* [Przykłady: Rurociąg z estymatorami](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>Zestaw SDK języka R

The R SDK enables you to use the R language with Azure Machine Learning. Zestaw SDK używa pakietu reticulate do powiązania z zestawem SDK języka Python usługi Azure Machine Learning. Dzięki temu można uzyskać dostęp do podstawowych obiektów i metod zaimplementowanych w module SDK języka Python z dowolnego środowiska języka R.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Samouczek: Tworzenie modelu regresji logistycznej](tutorial-1st-r-experiment.md)
* [Zestaw SDK usługi Azure Machine Learning dla języka R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

Projektant umożliwia szkolenie modeli za pomocą interfejsu przeciągania i upuszczania w przeglądarce internetowej.

+ [Co to jest projektant?](concept-designer.md)
+ [Samouczek : Przewidywanie ceny samochodu](tutorial-designer-automobile-price-train-score.md)
+ [Regresja: Przewidywanie ceny](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klasyfikacja: Przewidywanie dochodu](how-to-designer-sample-classification-predict-income.md)
+ [Klasyfikacja: Przewidywanie zmian, apecji i up-selling](how-to-designer-sample-classification-churn.md)
+ [Klasyfikacja za pomocą niestandardowego skryptu R: Przewidywanie opóźnień lotu](how-to-designer-sample-classification-flight-delay.md)
+ [Klasyfikacja tekstu: Zestaw danych Wikipedii SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>Interfejs wiersza polecenia

Narzędzie polecenia uczenia maszynowego jest rozszerzeniem interfejsu wiersza polecenia platformy Azure. Zapewnia wieloplatformowe polecenia interfejsu wiersza polecenia do pracy z usługą Azure Machine Learning. Zazwyczaj używasz interfejsu wiersza polecenia do automatyzacji zadań, takich jak szkolenie modelu uczenia maszynowego.

* [Korzystanie z rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MlOps na platformie Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować środowiska szkoleniowe](how-to-set-up-training-targets.md).
