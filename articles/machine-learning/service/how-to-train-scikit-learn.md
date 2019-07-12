---
title: Szkolenie i zarejestruj scikit-Dowiedz się więcej modeli
titleSuffix: Azure Machine Learning service
description: W tym artykule pokazano, jak szkolenie i zarejestruj scikit-Dowiedz się, model przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840016"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Szkolenie i zarejestruj Scikit-learn modeli na skalę przy użyciu usługi Azure Machine Learning

W tym artykule pokazano, jak szkolenie i zarejestrować model Scikit-learn przy użyciu usługi Azure Machine Learning. Używa ona popularnej [zestawu danych Iris](https://archive.ics.uci.edu/ml/datasets/iris) do klasyfikowania irysów Kwiatek obrazów za pomocą niestandardowej [scikit-Dowiedz się,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klasy.

Scikit-learn to platforma obliczeniowa typu open-source często używane do uczenia maszynowego. Za pomocą usługi Azure Machine Learning można szybko skalować w poziomie zadania szkoleń o całkowicie otwartym kodzie źródłowym, przy użyciu zasobów obliczeniowych w elastycznej chmurze. Można także śledzić swoje przebiegów szkoleniowych, modele wersji wdrażanie modeli i wiele więcej.

Czy tworzysz od podstaw modelu Scikit-learn korzystamy istniejącego modelu w chmurze, usługi Azure Machine Learning mogą pomóc tworzyć modele gotowe do produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z tych środowisk:
 - Notesu platformy Azure Machine Learning maszyny Wirtualnej — bez plików do pobrania i instalacji konieczne

    - Wykonaj [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md) utworzyć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium.
    - W folderze samples na serwerze Notes, należy znaleźć notesu ukończonych oraz rozszerzone, przechodząc do tego katalogu: **jak-to-użyj-usługi Azure ml > szkolenia > train-hyperparameter-tune-deploy-with-sklearn** folderu.

 - Serwer notesu programu Jupyter

    - [Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python](setup-create-workspace.md#sdk)
    - [Utwórz plik konfiguracji obszaru roboczego](setup-create-workspace.md#write-a-configuration-file)
    - [Pobierz przykładowy plik skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Możesz również znaleźć ukończone [wersji notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tego przewodnika stronę przykładów usługi GitHub. Notes zawiera rozwinięte rozdział z omówieniem hiperparametrycznego inteligentnego dostosowywania i pobierania najlepszy model przez podstawowe metryki.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperymentu szkolenia ładowania pakietów wymaganych języka python, inicjowanie obszar roboczy, tworzenia eksperymentu i przekazywanie danych szkoleniowych i skryptów szkolenia.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszaru roboczego usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Udostępnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które można utworzyć. W zestawu SDK języka Python, uzyskujesz dostęp artefaktów obszaru roboczego, tworząc [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Tworzenie eksperymentów i folder do przechowywania skryptów szkolenia. W tym przykładzie należy utworzyć eksperyment, o nazwie "skryptu sklearn irys".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Przekaż zestaw danych i skryptów

[Datastore](how-to-access-data.md) jest miejscem, gdzie danych może być przechowywane i używane przez zainstalowanie lub skopiowanie danych do obliczeniowego elementu docelowego. Każdy obszar roboczy zawiera domyślny magazyn danych. Przekazywanie danych i skryptów szkolenia z magazynem danych, dzięki czemu można łatwo do nich podczas szkolenia.

1. Utwórz katalog dla swoich danych.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Przekaż zestawu danych iris do domyślnego magazynu danych.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Skrypt szkoleniowy przekazywania Scikit-learn, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Utworzyć lub uzyskać obliczeniowego elementu docelowego

Utworzyć obliczeniowego elementu docelowego dla zadania do uruchomienia na Scikit-learn. Dowiedz się, Scikit tylko obsługuje jeden węzeł, przetwarzania procesora CPU.

Poniższy kod tworzy obliczeniowych usługi Azure Machine Learning w przypadku zarządzanych (AmlCompute) dla zasobu obliczeniowego zdalnego szkolenia. Tworzenie AmlCompute trwa około 5 minut. Jeśli AmlCompute o tej nazwie jest już w obszarze roboczym, ten kod pozwoli na pominięcie procesu tworzenia.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obliczeniowych elementów docelowych, zobacz [co to jest cel obliczenia](concept-compute-target.md) artykułu.

## <a name="create-a-scikit-learn-estimator"></a>Utwórz narzędzie do szacowania Scikit-learn

[Scikit-learn narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prostą metodę uruchamiania, Scikit-learn zadania szkolenia na obliczeniowego elementu docelowego. Jest wdrożona za pomocą [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klasy, która może służyć do szkoleń dotyczących procesora CPU jednowęzłowej pomocy technicznej.

Jeśli dodatkowe narzędzia pip lub conda pakietów, aby uruchomić skrypt szkolenia, może mieć zainstalowanego na Wynikowy obraz platformy docker, przekazując ich nazwiska za pomocą pakietu `pip_packages` i `conda_packages` argumentów.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Prześlij przebiegu

[Przebiegu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) zapewnia interfejs do historii uruchamiania podczas uruchamiania zadania, a po jego ukończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Uruchomienie jest wykonywane, przechodzi on przez następujące etapy:

- **Trwa przygotowywanie**: Obraz platformy docker są tworzone zgodnie ze skonfigurowanym TensorFlow narzędzie do szacowania. Obraz, który jest przekazany do obszaru roboczego container registry i nowszych przebiegów w pamięci podręcznej. Dzienniki również są przesyłane strumieniowo do historii uruchamiania i mogą być wyświetlane w celu monitorowania postępu.

- **Skalowanie**: Klaster polegające na skalowanie w górę, jeśli klaster usługi Batch AI wymaga więcej węzłów do wykonania przebiegu nie są obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptu są przekazywane do obliczeniowego elementu docelowego, magazyny danych są zainstalowane lub kopiowane i entry_script jest wykonywany. Dane wyjściowe ze strumienia wyjściowego stdout i. / folderu dzienników są przesyłane strumieniowo do historii uruchamiania i może służyć do monitorowania przebiegu.

- **Przetwarzanie końcowe**: . / Wyjścia, folder przebiegu jest kopiowana do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestrować model

Gdy już uczony model, można zapisać i zarejestruj go do obszaru roboczego. Rejestracja modelu pozwala magazynu i wersji modeli w Twoim obszarze roboczym, aby uprościć [zarządzania i wdrażania modelu](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkolenia, train_iris.py, aby zapisać modelu. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model do swojego obszaru roboczego z następującym kodem.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przeszkolonych i zarejestrowany Scikit-learn modelu w usłudze Azure Machine Learning.

* Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego [modelu wdrażania](how-to-deploy-and-where.md) artykułu.

* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)