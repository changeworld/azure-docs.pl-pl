---
title: Metody szkoleń modelu
titleSuffix: Azure Machine Learning
description: Poznaj różne metody, których można użyć do uczenia modelu z Azure Machine Learning. Szacowania zapewnia łatwy sposób pracy z popularnymi strukturami, takimi jak Scikit — uczenie się, TensorFlow, Keras, PyTorch i łańcucha. Potoki Machine Learning ułatwiają planowanie nienadzorowanych uruchomień, używanie środowisk obliczeniowych heterogenicznych i ponowne używanie części przepływu pracy. I uruchamiania konfiguracje zapewniają szczegółową kontrolę nad celami obliczeniowymi, na których działa proces szkoleniowy.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350606"
---
# <a name="train-models-with-azure-machine-learning"></a>Szkolenie modeli za pomocą usługi Azure Machine Learning

Azure Machine Learning oferuje kilka sposobów uczenia modeli — od pierwszego rozwiązania kodu przy użyciu zestawu SDK do małych rozwiązań kodu, takich jak automatyczne Uczenie maszynowe i interfejs graficzny. Skorzystaj z poniższej listy, aby określić, która metoda szkoleniowa jest dla Ciebie odpowiednia:

+ [Zestaw Azure Machine Learning SDK dla języka Python](#python-sdk): Zestaw SDK języka Python oferuje kilka sposobów uczenia modeli, z których każdy może mieć różne możliwości.

    | Metoda uczenia | Opis |
    | ----- | ----- |
    | [Uruchom konfigurację](#run-configuration) | **Ogólny sposób uczenia modeli** polega na użyciu skryptu szkoleniowego i uruchomienia konfiguracji. Konfiguracja uruchamiania zawiera informacje konieczne do skonfigurowania środowiska szkoleniowego używanego do uczenia modelu. Możesz utworzyć konfigurację uruchamiania, skrypt szkoleniowy i cel obliczeń (środowisko szkoleniowe) i uruchomić zadanie szkoleniowe. |
    | [Automatyczne Uczenie maszynowe](#automated-machine-learning) | Automatyczne Uczenie maszynowe umożliwia **uczenie modeli bez obszernej wiedzy o nauce lub programowaniu**. W przypadku osób z tłem i programowaniem danych zapewnia ona sposób oszczędzania czasu i zasobów dzięki automatyzowaniu wyboru algorytmu i dostrajania parametrów. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z funkcji automatycznego uczenia maszynowego. |
    | [Szacowania](#estimators) | Klasy szacowania ułatwiają **uczenie modeli opartych na popularnych strukturach uczenia maszynowego**. Istnieją klasy szacowania dla **Scikit-uczenia**, **PyTorch**, **TensorFlow**i **łańcucha**. Istnieje również ogólny szacowania, który może być używany z platformami, które nie mają jeszcze dedykowanej klasy szacowania. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania. |
    | [Potok uczenia maszynowego](#machine-learning-pipeline) | Potoki nie są różnymi metodami szkoleniowymi, ale **sposób definiowania przepływu pracy przy użyciu modularnych, wielokrotnych kroków**, które mogą obejmować szkolenie w ramach przepływu pracy. Potoki usługi Machine Learning obsługują korzystanie z funkcji automatycznego uczenia maszynowego, szacowania i uruchamiania konfiguracji w celu uczenia modeli. Ponieważ potoki nie są ukierunkowane na szkolenie, przyczyny użycia potoku są bardziej zróżnicowane niż inne metody uczenia się. Ogólnie rzecz biorąc, można użyć potoku, gdy:<br>* Chcesz **zaplanować procesy nienadzorowane** , takie jak długotrwałe zadania szkoleniowe lub Przygotowywanie danych.<br>* Należy użyć **wielu kroków** , które są skoordynowane w niejednorodnych zasobach obliczeniowych i lokalizacjach magazynu.<br>* Użyj potoku jako **szablonu wielokrotnego użytku** dla konkretnych scenariuszy, takich jak przeszkolenie lub wsadowe ocenianie.<br>* **ścieżki i źródła danych, dane wejściowe i wyjściowe** dla przepływu pracy.<br>* Przepływ pracy jest **implementowany przez różne zespoły, które pracują niezależnie od konkretnych kroków**. Kroki można następnie połączyć w potoku w celu zaimplementowania przepływu pracy. |

+ **Interfejs wizualizacji**: __Interfejs wizualizacji__ Azure Machine Learning zapewnia łatwą funkcję uczenia maszynowego na potrzeby tworzenia weryfikacji koncepcji lub dla użytkowników z niewielkim doświadczeniem związanym z kodowaniem. Pozwala to na uczenie modeli przy użyciu funkcji przeciągania i upuszczania interfejsu użytkownika opartego na sieci Web. Możesz użyć kodu w języku Python jako części projektu lub pouczenia modeli bez pisania kodu.

+ **INTERFEJS WIERSZA POLECENIA**: Interfejs wiersza polecenia uczenia maszynowego dostarcza poleceń dla typowych zadań z Azure Machine Learning i jest często używany do **tworzenia skryptów i automatyzowania zadań**. Na przykład po utworzeniu skryptu szkoleniowego lub potoku można użyć interfejsu wiersza polecenia, aby rozpocząć szkolenie zgodnie z harmonogramem lub w przypadku aktualizowania plików danych używanych do szkoleń. W przypadku modeli szkoleniowych dostępne są polecenia, które przesyłają zadania szkoleniowe. Może przesyłać zadania przy użyciu konfiguracji uruchamiania lub potoków.

Każda z tych metod szkoleniowych może używać różnych typów zasobów obliczeniowych do szkoleń. Zbiorowo te zasoby są określane jako [__cele obliczeniowe__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.

## <a name="python-sdk"></a>Zestaw SDK dla języka Python

Zestaw Azure Machine Learning SDK dla języka Python umożliwia tworzenie i uruchamianie przepływów pracy uczenia maszynowego przy użyciu Azure Machine Learning. Można korzystać z usługi z interaktywnej sesji języka Python, Jupyter notesów, Visual Studio Code lub innego środowiska IDE.

* [Co to jest zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalowanie/Aktualizowanie zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Skonfiguruj środowisko programistyczne dla Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Uruchom konfigurację

Ogólne zadanie szkoleniowe z Azure Machine Learning można zdefiniować za pomocą [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Następnie zostanie użyta Konfiguracja przebiegu wraz ze skryptami szkoleniowymi do uczenia modelu w obiekcie docelowym obliczeń.

Możesz rozpocząć od konfiguracji uruchamiania komputera lokalnego, a następnie w razie potrzeby przełączyć się na jeden dla docelowej wartości obliczeniowej opartej na chmurze. Podczas zmieniania elementu docelowego obliczeń można zmienić tylko używaną konfigurację uruchamiania. Uruchomienie rejestruje również informacje o zadaniu szkoleniowym, takie jak dane wejściowe, wyjściowe i dzienniki.

* [Co to jest Konfiguracja przebiegu?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Samouczek: Uczenie swojego pierwszego modelu ML @ no__t-0
* [Examples: Jupyter Notebook przykłady modeli szkoleniowych @ no__t-0
* [Instrukcje: Skonfiguruj cele obliczeń i używaj ich do szkolenia modelowego @ no__t-0

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Zdefiniuj iteracje, ustawienia parametrów, cechowania i inne ustawienia. Podczas uczenia Azure Machine Learning próbuje jednocześnie różne algorytmy i parametry. Szkolenie zostaje zatrzymane po trafieniu zdefiniowanych kryteriów zakończenia. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania.

> [!TIP]
> W funkcji do zestawu SDK języka Python można także użyć zautomatyzowanej ml za pomocą [strony docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

* [Co to jest automatyczne Uczenie maszynowe?](concept-automated-ml.md)
* [Samouczek: Tworzenie pierwszego modelu klasyfikacji przy użyciu zautomatyzowanej uczenia maszynowego @ no__t-0
* [Samouczek: Korzystanie z automatycznej uczenia maszynowego do przewidywania opłat za taksówkę @ no__t-0
* [Examples: Przykłady Jupyter Notebook na potrzeby automatycznego uczenia maszynowego @ no__t-0
* [Instrukcje: Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python @ no__t-0
* [Instrukcje: Autouczenie modelu prognozy szeregów czasowych @ no__t-0
* [Instrukcje: Twórz, eksploruj i wdrażaj automatyczne eksperymenty uczenia maszynowego przy użyciu strony docelowej obszaru roboczego Azure Machine Learning (wersja zapoznawcza) ](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Szacowania

Szacowania ułatwiają uczenie modeli przy użyciu popularnych platform ML. Jeśli używasz **Scikit-uczenia**się, **PyTorch**, **TensorFlow**lub **łańcucha**, należy rozważyć użycie szacowania do szkolenia. Istnieje również ogólny szacowania, który może być używany z platformami, które nie mają jeszcze dedykowanej klasy szacowania. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z szacowania.

* [Co to są szacowania?](concept-azure-machine-learning-architecture.md#estimators)
* [Samouczek: Uczenie modeli klasyfikacji obrazów przy użyciu MNIST ręcznie danych i scikit — uczenie się przy użyciu Azure Machine Learning @ no__t-0
* [Examples: Przykłady użycia szacowania @ no__t-0 Jupyter Notebook
* [Instrukcje: Tworzenie szacowania w szkole @ no__t-0

### <a name="machine-learning-pipeline"></a>Potok uczenia maszynowego

Potoki uczenia maszynowego mogą korzystać z wymienionych wcześniej metod szkoleniowych (uruchamiania konfiguracji, szacowania i automatycznej uczenia maszynowego). Potoki zawierają więcej informacji na temat tworzenia przepływu pracy, co obejmuje więcej niż tylko szkolenia modeli. W potoku można nauczyć model przy użyciu zautomatyzowanej uczenia maszynowego, szacowania lub uruchamiania konfiguracji.

* [Co to są potoki ML w Azure Machine Learning?](concept-ml-pipelines.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)
* [Samouczek: Korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii @ no__t-0
* [Examples: Jupyter Notebook przykłady dla potoków uczenia maszynowego @ no__t-0
* [Examples: Potok z automatycznym uczeniem maszynowym @ no__t-0
* [Examples: Potok z szacowania @ no__t-0

## <a name="visual-interface"></a>Interfejs wizualny

Interfejs wizualny (wersja zapoznawcza) umożliwia uczenie modeli przy użyciu interfejsu przeciągania i upuszczania w przeglądarce internetowej.

+ [Co to jest interfejs wizualny?](ui-concept-visual-interface.md)
+ [Tutorial: Przewidywanie ceny za samochód no__t-0
+ [Regression: Przewidywana cena](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ [Classification: Przewidywanie ryzyka kredytowego](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ [Classification: Przewidywanie zmian, pragnienie i kupowanie](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>Interfejs wiersza polecenia

Interfejs wiersza polecenia uczenia maszynowego to rozszerzenie interfejsu wiersza polecenia platformy Azure. Udostępnia on Międzyplatformowe polecenia interfejsu CLI do pracy z Azure Machine Learning. Zazwyczaj używasz interfejsu wiersza polecenia do automatyzowania zadań, takich jak uczenie modelu uczenia maszynowego.

* [Użyj rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps na platformie Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować środowiska szkoleniowe](how-to-set-up-training-targets.md).
