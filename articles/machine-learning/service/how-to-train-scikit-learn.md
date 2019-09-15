---
title: Uczenie modeli uczenia maszynowego za pomocą scikit — uczenie się
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać scikite skrypty szkoleniowe na skalę przedsiębiorstwa przy użyciu klasy skryptu sklearn szacowania firmy Azure Machine Learning. Przykładowe skrypty klasyfikują obrazy kwiatów z przesłoną w celu utworzenia modelu uczenia maszynowego na podstawie zestawu danych Iris scikit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 2b05ba7e4d38b596bdf76655fad0736425f8ce89
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002534"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Kompiluj scikit — Poznaj modele na dużą skalę dzięki Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać scikite skrypty szkoleniowe na skalę przedsiębiorstwa przy użyciu klasy [skryptu sklearn szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) firmy Azure Machine Learning. 

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów kwiatów w formacie Iris w celu utworzenia modelu uczenia maszynowego na podstawie [zestawu danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Bez względu na to, czy przeprowadzasz szkolenia z modelu uczenia maszynowego scikit — uczenie się od podstaw lub przenosisz istniejący model do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać i monitorować modele klasy produkcyjnej przy użyciu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:
 - Maszyna wirtualna w Azure Machine Learning Notes — nie jest wymagane pobieranie ani instalacja

    - Wykonaj kroki z artykułu [Samouczek: Zainstaluj środowisko i obszar](tutorial-1st-experiment-sdk-setup.md) roboczy, aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze szkolenia przykładów na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > training > uczenie-parametr-dostrajania-Deploy-with-skryptu sklearn** .

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Pobierz zestaw danych i przykładowy plik skryptu 
        - [zestaw danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes zawiera rozszerzoną sekcję obejmującą dostrajanie inteligentnego parametru i pobierającą najlepszy model przy użyciu metryk głównych.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

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

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) utworzenie obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Tworzenie eksperymentu uczenia maszynowego

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie Utwórz eksperyment o nazwie "skryptu sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Przekazywanie zestawu danych i skryptów

[Magazyn](how-to-access-data.md) danych to miejsce, w którym dane mogą być przechowywane i dostępne przez zainstalowanie lub skopiowanie danych do elementu docelowego obliczeń. Każdy obszar roboczy zawiera domyślny magazyn danych. Przekaż skrypty danych i szkoleniowe do magazynu datastore, aby można było łatwo uzyskać do nich dostęp podczas szkoleń.

1. Utwórz katalog dla swoich danych.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Przekaż zestaw danych Iris do domyślnego magazynu plików.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Przekaż skrypt `train_iris.py`szkoleniowy scikit — uczenie się.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Utwórz lub Pobierz obiekt docelowy obliczeń

Utwórz obiekt docelowy obliczeń dla zadania scikit-Dowiedz się, aby uruchomić. Scikit — uczenie obsługuje tylko jeden węzeł, przetwarzanie procesora CPU.

Poniższy kod tworzy Azure Machine Learning Managed COMPUTE (AmlCompute) dla zasobu obliczeń zdalnego szkolenia. Tworzenie AmlCompute trwa około 5 minut. Jeśli AmlCompute o tej nazwie znajduje się już w obszarze roboczym, ten kod pominie proces tworzenia.

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

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Tworzenie scikit — uczenie się szacowania

[Scikit — uczenie szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) zapewnia prostą metodę uruchamiania zadania szkoleniowego scikit-uczenia na obiekcie docelowym obliczeń. Jest implementowana za pomocą [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klasy, która może być używana do obsługi szkolenia procesora jednowęzłowego.

Jeśli do uruchomienia skryptu szkoleniowego wymagane są dodatkowe pakiety PIP lub Conda, można je zainstalować na powstającym obrazie Docker, przekazując ich nazwy za pomocą `pip_packages` argumentów i. `conda_packages`

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

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: Obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: Klaster próbuje skalować w górę, Jeśli klaster Batch AI wymaga więcej węzłów do uruchomienia przebiegu niż jest to obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: Folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestruj model

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkoleniowego, train_iris. PR, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym przy użyciu następującego kodu.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Następne kroki


W tym artykule został przeszkolony i zarejestrowany model Keras na Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrożenia modelu.

> [!div class="nextstepaction"]
> [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
