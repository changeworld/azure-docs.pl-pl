---
title: Trenuj modele Kerasa do głębokiego uczenia się
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić i rejestrować model klasyfikacji sieci neuronowych Keras uruchomiony na tensorflow przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269967"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Szkolenie i rejestrowanie modelu klasyfikacji kerasu za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak szkolić i rejestrować model klasyfikacji Keras zbudowany na TensorFlow przy użyciu usługi Azure Machine Learning. Używa popularnego [zestawu danych MNIST](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr odręcznych przy użyciu głębokiej sieci neuronowej (DNN) zbudowanej przy użyciu [biblioteki Keras Python](https://keras.io) działającej na szczycie [TensorFlow](https://www.tensorflow.org/overview).

Keras to interfejs API sieci neuronowych wysokiego poziomu, który może działać na szczycie innych popularnych struktur DNN w celu uproszczenia rozwoju. Dzięki usłudze Azure Machine Learning można szybko skalować zadania szkoleniowe w poziomie przy użyciu elastycznych zasobów obliczeniowych w chmurze. Można również śledzić przebiegi treningowe, modele wersji, wdrażać modele i wiele więcej.

Niezależnie od tego, czy tworzysz model Keras od podstaw, czy wprowadzasz istniejący model do chmury, usługa Azure Machine Learning może pomóc w tworzeniu modeli gotowych do produkcji.

Zobacz [artykuł koncepcyjny,](concept-deep-learning-vs-machine-learning.md) aby uzyskać informacje na temat różnic między uczeniem maszynowym a uczeniem głębokim.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

- Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.
    - W folderze przykłady na serwerze notesu znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-keras** folder.

 - Twój własny serwer notebooka Jupyter

    - [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz przykładowe pliki](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` skryptów i`utils.py`

    Ukończoną wersję tego przewodnika można również znaleźć ukończoną wersję tego przewodnika [w usłudze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) GitHub. Notes zawiera rozszerzone sekcje obejmujące inteligentne dostrajanie hiperparametryczne, wdrażanie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperyment szkoleniowy, ładując wymagane pakiety języka Python, inicjując obszar roboczy, tworząc eksperyment i przekazując dane szkoleniowe i skrypty szkoleniowe.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki Języka Python.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestaw SDK języka Python można uzyskać dostęp do [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefaktów obszaru roboczego, tworząc obiekt.

Utwórz obiekt obszaru `config.json` roboczego z pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment o nazwie "keras-mnist" w obszarze roboczym.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Tworzenie zestawu danych plików

Obiekt `FileDataset` odwołuje się do jednego lub wielu plików w magazynie danych obszaru roboczego lub publicznych adresach URL. Pliki mogą być w dowolnym formacie, a klasa zapewnia możliwość pobierania lub instalowania plików do obliczeń. Tworząc program `FileDataset`, można utworzyć odwołanie do lokalizacji źródła danych. Jeśli zastosowano żadnych przekształceń do zestawu danych, będą one przechowywane w zestawie danych, jak również. Dane pozostają w istniejącej lokalizacji, więc nie ponosi żadnych dodatkowych kosztów magazynowania. Aby uzyskać więcej informacji, `Dataset` zapoznaj się z [instrukcjami](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) na opakowaniu.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Użyj `register()` tej metody, aby zarejestrować zestaw danych w obszarze roboczym, aby mogły być udostępniane innym, ponownie używane w różnych eksperymentach i określane według nazwy w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Tworzenie celu obliczeniowego

Utwórz cel obliczeniowy dla zadania TensorFlow do uruchomienia. W tym przykładzie utwórz klaster obliczeniowy usługi Azure Machine Learning z obsługą procesora GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz, co jest artykuł [docelowy obliczeń.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Tworzenie estymatora TensorFlow i importowanie keras

[Estymator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadań szkoleniowych TensorFlow na celu obliczeniowym. Ponieważ Keras działa na szczycie TensorFlow, można użyć oszacowanie TensorFlow i zaimportować bibliotekę Keras przy użyciu argumentu. `pip_packages`

Najpierw pobierz dane z magazynu danych `Dataset` obszaru roboczego przy użyciu klasy.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

Estymator TensorFlow jest implementowany [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) za pośrednictwem klasy ogólnej, która może służyć do obsługi dowolnej struktury. Ponadto należy utworzyć słownik `script_params` zawierający ustawienia hiperparametru DNN. Aby uzyskać więcej informacji na temat modeli szkoleniowych przy użyciu ogólnego estymatora, zobacz [modele pociągów z usługą Azure Machine Learning przy użyciu estymatora](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij bieg

[Run Obiekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomiony i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

W miarę wykonywania przebiegu przechodzi przez następujące etapy:

- **Przygotowanie:** Obraz docker jest tworzony zgodnie z oszacowaniem TensorFlow. Obraz jest przekazyany do rejestru kontenerów obszaru roboczego i buforowany dla późniejszych uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je przeglądać w celu monitorowania postępu.

- **Skalowanie:** Klaster próbuje skalować w górę, jeśli klaster AI partii wymaga więcej węzłów do wykonania uruchomienia niż są obecnie dostępne.

- **Uruchomione**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są montowane lub kopiowane, a entry_script jest wykonywana. Wyjścia z folderu stdout i ./logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Post-Processing:** Folder ./outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-the-model"></a>Rejestrowanie modelu

Po przeszkoleniu modelu DNN można go zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Model, który właśnie zarejestrowałeś, jest wdrażany dokładnie w taki sam sposób, jak każdy inny zarejestrowany model w usłudze Azure Machine Learning, niezależnie od tego, który estymator został użyty do szkolenia. Instrukcje wdrażania zawiera sekcję dotyczącą rejestrowania modeli, ale można pominąć bezpośrednio do [tworzenia miejsca docelowego obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ masz już zarejestrowany model.

Można również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych pracy sprawdzania poprawności modelu lokalnie. W skrypcie szkolenia `mnist-keras.py`obiekt wygaszacz TensorFlow utrzymuje model do folderu lokalnego (lokalnego do obiektu docelowego obliczeń). Można użyć Uruchom obiektu, aby pobrać kopię z magazynu danych.

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

W tym artykule przeszkolono i zarejestrowałeś model Keras w usłudze Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrażania modelu.

> [!div class="nextstepaction"]
> [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md)
* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna dla rozproszonych szkoleń głębokiego uczenia na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
