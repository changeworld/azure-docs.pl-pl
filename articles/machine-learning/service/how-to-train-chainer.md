---
title: Uczenie modeli łańcucha uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe PyTorch na skalę przedsiębiorstwa Azure Machine Learning przy użyciu klasy szacowania w łańcuchu kluczy.  Przykładowy skrypt klasyfikuje obrazy cyfr pisanych ręcznie, aby utworzyć sieć neuronowych uczenia głębokiego przy użyciu biblioteki języka Python w łańcuchu, która działa w oparciu o numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: a7c19486fe6787c4548a77dcdd93a92a92c97d8c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931108"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Uczenie i rejestrowanie modeli łańcucha na dużą skalę za pomocą Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe [łańcucha](https://chainer.org/) na skalę przedsiębiorstwa przy użyciu klasy [szacowania łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) firmy Azure Machine Learning. Przykładowy skrypt szkoleniowy w tym artykule używa popularnego [zestawu danych mnist ręcznie](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr odpisanych ręcznie przy użyciu sieci głębokiej neuronowych (DNN) utworzonej przy użyciu biblioteki języka Python w usłudze, która działa w oparciu o [numpy](https://www.numpy.org/).

Bez względu na to, czy przeprowadzasz uczenie modelu łańcucha uczenia głębokiego od podstaw lub przenosisz istniejący model do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać, instalować i monitorować modele klasy produkcyjnej za pomocą Azure Machine Learning. 

Dowiedz się więcej na temat uczenia głębokiego i uczenia [maszynowego](concept-deep-learning-vs-machine-learning.md).

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

- Maszyna wirtualna w Azure Machine Learning Notes — nie jest wymagane pobieranie ani instalacja

    - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze przykłady głębokiego uczenia na serwerze notesu Znajdź ukończony Notes i pliki w temacie **How to-use-azure > ml-frameworks > moduł łańcucha > wdrożenia > uczenie-parametr-dostrajania-Deploy-with-the-łańcucha** .  Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

- Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Pobierz przykładowy plik skryptu [chainer_mnist. PR](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - Możesz również znaleźć kompletną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) tego przewodnika na stronie przykładów w serwisie GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj bibliotekę Azure. Core Python i Wyświetl numer wersji.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie obiektu [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Utwórz obiekt obszaru roboczego, odczytując plik `config.json` utworzony w [sekcji wymagania wstępne](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Utwórz katalog projektu
Utwórz katalog, który będzie zawierać wszystkie niezbędne kody z komputera lokalnego, do którego będziesz potrzebować dostępu do zasobu zdalnego. Dotyczy to również skryptu szkoleniowego i wszelkich dodatkowych plików, od których zależy skrypt szkoleniowy.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Przygotuj skrypt szkoleniowy

W tym samouczku skrypt szkoleniowy **chainer_mnist. PR** został już udostępniony. W tym celu należy mieć możliwość wykonania dowolnego niestandardowego skryptu szkoleniowego i uruchomienia go z platformą Azure ML bez konieczności modyfikowania kodu.

Aby korzystać z funkcji śledzenia i metryk platformy Azure ML, Dodaj niewielką ilość kodu platformy Azure ML w skrypcie szkoleniowym.  Skrypt szkoleniowy **chainer_mnist. PR** pokazuje, w jaki sposób rejestrować niektóre metryki w ramach uruchomienia usługi Azure ml przy użyciu obiektu `Run` w skrypcie.

Dostarczony skrypt szkoleniowy używa przykładowych danych z funkcji `datasets.mnist.get_mnist` łańcucha.  W przypadku własnych danych może być konieczne wykonanie kroków takich jak [przekazywanie zestawu danych i skryptów](how-to-train-keras.md#data-upload) w celu udostępnienia danych podczas szkoleń.

Skopiuj skrypt szkoleniowy **chainer_mnist. PR** do katalogu projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Utwórz eksperyment uczenia głębokiego

Utwórz eksperyment. W tym przykładzie Utwórz eksperyment o nazwie "łańcucher-mnist ręcznie".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Utwórz lub Pobierz obiekt docelowy obliczeń

Potrzebujesz [elementu docelowego obliczeń](concept-compute-target.md) do uczenia modelu. W tym przykładzie używany jest program Azure ML Managed COMPUTE (AmlCompute) dla zasobu obliczeń zdalnego szkolenia.

**Tworzenie AmlCompute trwa około 5 minut**. Jeśli AmlCompute o tej nazwie znajduje się już w obszarze roboczym, ten kod pomija proces tworzenia.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Tworzenie łańcucha szacowania

[Szacowania łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadań szkoleniowych łańcucha na obiekcie docelowym obliczeń.

Szacowania łańcucha jest implementowane za pomocą ogólnej klasy [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji o modelach szkoleniowych przy użyciu generycznej szacowania, zobacz [uczenie modeli Azure Machine Learning przy](how-to-train-ml-models.md) użyciu usługi szacowania

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie z szacowania łańcucha. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchamianie**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestruj model

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).


Po zakończeniu szkolenia modelu Zarejestruj model w Twoim obszarze roboczym przy użyciu następującego kodu.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Właśnie zarejestrowany model jest wdrażany w taki sam sposób jak każdy inny zarejestrowany model w Azure Machine Learning, niezależnie od tego, który szacowania używany do szkoleń. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

Możesz również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych czynności związanych z walidacją modelu lokalnie. W skrypcie szkoleniowym, `chainer_mnist.py`, obiekt wygaszacza utrzymuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Za pomocą obiektu Run można pobrać kopię z magazynu danych.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano przeszkolone i zarejestrowano głębokie uczenie neuronowych sieci przy użyciu łańcucha na Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu [wdrożenia modelu](how-to-deploy-and-where.md) .

* [Dostrajanie parametrów](how-to-tune-hyperparameters.md)

* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)

* [Zapoznaj się z naszą architekturą referencyjną na potrzeby rozproszonego szkolenia uczeniowego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
