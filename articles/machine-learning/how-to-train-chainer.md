---
title: Trenuj modele uczenia głębokiego Chainer
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe pyTorch w skali przedsiębiorstwa przy użyciu klasy estymatora usługi Azure Machine Learning Chainer.  Przykładowy skrypt klasyfikuje odręczne obrazy cyfr do tworzenia sieci neuronowej uczenia głębokiego przy użyciu biblioteki Chainer Python uruchomionej na numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536618"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Szkolenie i rejestrowanie modeli chainerów na dużą skalę dzięki usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchomić skrypty szkoleniowe [programu Chainer](https://chainer.org/) w skali przedsiębiorstwa przy użyciu klasy [estymatora usługi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Azure Machine Learning Chainer. Przykładowy skrypt szkoleniowy w tym artykule używa popularnego [zestawu danych MNIST](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr odręcznych przy użyciu głębokiej sieci neuronowej (DNN) utworzonej przy użyciu biblioteki Chainer Python uruchomionej na [numpy](https://www.numpy.org/).

Niezależnie od tego, czy szkolisz model uczenia głębokiego Chainer od podstaw, czy wprowadzasz istniejący model do chmury, możesz użyć usługi Azure Machine Learning do skalowania w poziomie zadań szkoleniowych typu open source przy użyciu elastycznych zasobów obliczeniowych w chmurze. Za pomocą usługi Azure Machine Learning można tworzyć, wdrażać, wersje i monitorować modele klasy produkcyjnej. 

Dowiedz się więcej o [uczeniu głębokim i uczeniu maszynowym.](concept-deep-learning-vs-machine-learning.md)

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

- Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.
    - W przykładowym folderze uczenia głębokiego na serwerze notesu znajdź ukończony notes i pliki w **folderze how-to-use-azureml > ml-frameworks > chainer > deployment > train-hyperparameter-tune-deploy-with-chainer** folder.  Notes zawiera rozszerzone sekcje obejmujące inteligentne dostrajanie hiperparametryczne, wdrażanie modelu i widżety notesu.

- Twój własny serwer notebooka Jupyter

    - [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Pobierz przykładowy plik skryptu [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Ukończoną wersję tego przewodnika można również znaleźć ukończoną wersję tego przewodnika [w usłudze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) GitHub. Notes zawiera rozszerzone sekcje obejmujące inteligentne dostrajanie hiperparametryczne, wdrażanie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperyment szkoleniowy, ładując wymagane pakiety języka Python, inicjując obszar roboczy, tworząc eksperyment i przekazując dane szkoleniowe i skrypty szkoleniowe.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj bibliotekę języka Azureml.core Python i wyświetl numer wersji.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestaw SDK języka Python można uzyskać dostęp do [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefaktów obszaru roboczego, tworząc obiekt.

Utwórz obiekt obszaru roboczego, odczytając `config.json` plik utworzony w sekcji wymagania [wstępne:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Tworzenie katalogu projektów
Utwórz katalog, który będzie zawierał cały niezbędny kod z komputera lokalnego, do którego będziesz potrzebować dostępu do zasobu zdalnego. Obejmuje to skrypt szkolenia i wszelkie dodatkowe pliki, od którego zależy skrypt szkolenia.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Przygotowanie skryptu szkoleniowego

W tym samouczku skrypt szkoleniowy **chainer_mnist.py** jest już dla Ciebie. W praktyce powinieneś być w stanie podjąć dowolny niestandardowy skrypt szkoleniowy, jak jest i uruchomić go za pomocą usługi Azure ML bez konieczności modyfikowania kodu.

Aby korzystać z funkcji śledzenia i metryk usługi Azure ML, dodaj niewielką ilość kodu usługi Azure ML w skrypcie szkoleniowym.  Skrypt szkoleniowy **chainer_mnist.py** pokazuje, jak rejestrować niektóre metryki do `Run` uruchomienia usługi Azure ML przy użyciu obiektu w skrypcie.

Dostarczony skrypt szkoleniowy używa przykładowych danych `datasets.mnist.get_mnist` z funkcji chainer.  W przypadku własnych danych może być konieczne użycie kroków, takich jak [przekazywanie zestawu danych i skryptów,](how-to-train-keras.md#data-upload) aby udostępnić dane podczas szkolenia.

Skopiuj skrypt szkoleniowy **chainer_mnist.py** do katalogu projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Tworzenie eksperymentu głębokiego uczenia się

Tworzenie eksperymentu. W tym przykładzie utwórz eksperyment o nazwie "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Tworzenie lub uzyskanie celu obliczeniowego

Potrzebujesz [celu obliczeniowego](concept-compute-target.md) do szkolenia modelu. W tym przykładzie używasz narzędzia Azure ML zarządzanych obliczeń (AmlCompute) dla zdalnego szkolenia zasobów obliczeniowych.

**Tworzenie AmlCompute trwa około 5 minut**. Jeśli AmlCompute o tej nazwie jest już w obszarze roboczym, ten kod pomija proces tworzenia.  

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

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz, co jest artykuł [docelowy obliczeń.](concept-compute-target.md)

## <a name="create-a-chainer-estimator"></a>Tworzenie estymatora Chainer

[Estymator Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadań szkoleniowych Chainer na celu obliczeniowym.

Program Chainer estymator jest implementowany za pośrednictwem klasy ogólnej, [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji na temat modeli szkoleniowych przy użyciu ogólnego estymatora, zobacz [modele pociągów z usługą Azure Machine Learning przy użyciu estymatora](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Prześlij bieg

[Run Obiekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomiony i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

W miarę wykonywania przebiegu przechodzi przez następujące etapy:

- **Przygotowanie:** Obraz docker jest tworzony zgodnie z szacunków Chainer. Obraz jest przekazyany do rejestru kontenerów obszaru roboczego i buforowany dla późniejszych uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je przeglądać w celu monitorowania postępu.

- **Skalowanie:** Klaster próbuje skalować w górę, jeśli klaster AI partii wymaga więcej węzłów do wykonania uruchomienia niż są obecnie dostępne.

- **Uruchomione**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są montowane lub kopiowane, a entry_script jest wykonywana. Wyjścia z folderu stdout i ./logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Post-Processing:** Folder ./outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisywanie i rejestrowanie modelu

Po przeszkoleniu modelu można go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md)


Po zakończeniu szkolenia modelu, zarejestruj model do obszaru roboczego z następującym kodem.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Model, który właśnie zarejestrowałeś, jest wdrażany dokładnie w taki sam sposób, jak każdy inny zarejestrowany model w usłudze Azure Machine Learning, niezależnie od tego, który estymator został użyty do szkolenia. Instrukcje wdrażania zawiera sekcję dotyczącą rejestrowania modeli, ale można pominąć bezpośrednio do [tworzenia miejsca docelowego obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ masz już zarejestrowany model.

Można również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych pracy sprawdzania poprawności modelu lokalnie. W skrypcie szkolenia `chainer_mnist.py`obiekt wygaszacza utrzymuje model do folderu lokalnego (lokalnego do obiektu docelowego obliczeń). Można użyć Uruchom obiektu, aby pobrać kopię z magazynu danych.

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

W tym artykule przeszkolono i zarejestrowałeś głęboką sieć neuronową uczenia głębokiego przy użyciu narzędzia Chainer w usłudze Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu [wdrażania modelu.](how-to-deploy-and-where.md)

* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)

* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)

* [Zobacz naszą architekturę referencyjną dla rozproszonych szkoleń głębokiego uczenia na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
