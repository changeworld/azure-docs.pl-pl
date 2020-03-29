---
title: Trenuj modele uczenia maszynowego scikit-learn
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić skrypty szkoleniowe scikit-learn w skali przedsiębiorstwa przy użyciu klasy estymatora usługi Azure Machine Learning SKlearn. Przykładowe skrypty klasyfikują obrazy kwiatów tęczówki, aby utworzyć model uczenia maszynowego na podstawie zestawu danych tęczówki scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942271"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Tworzenie modeli scikit-learn na dużą skalę dzięki usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchomić skrypty szkoleniowe scikit-learn w skali przedsiębiorstwa przy użyciu klasy [estymatora usługi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) Azure Machine Learning SKlearn. 

Przykładowe skrypty w tym artykule są używane do klasyfikowania obrazów kwiatów tęczówki w celu utworzenia modelu uczenia maszynowego na podstawie [zestawu danych tęczówki](https://archive.ics.uci.edu/ml/datasets/iris)scikit-learn.

Niezależnie od tego, czy szkolisz model scikit-learn uczenia się uczenia maszynowego od podstaw, czy wprowadzasz istniejący model do chmury, możesz użyć usługi Azure Machine Learning do skalowania w poziomie zadań szkoleniowych typu open source przy użyciu elastycznych zasobów obliczeniowych w chmurze. Za pomocą usługi Azure Machine Learning można tworzyć, wdrażać, wersje i monitorować modele klasy produkcyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:
 - Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.
    - W przykładowym folderze szkoleniowym na serwerze notesu znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** folder.

 - Twój własny serwer notebooka Jupyter

    - [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Pobieranie zestawu danych i przykładowego pliku skryptu 
        - [zestaw danych tęczówki](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Ukończoną wersję tego przewodnika można również znaleźć ukończoną wersję tego przewodnika [w usłudze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) GitHub. Notes zawiera rozszerzoną sekcję obejmującą inteligentne dostrajanie hiperparametryczne i pobieranie najlepszego modelu według podstawowych metryk.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperyment szkoleniowy, ładując wymagane pakiety języka Python, inicjując obszar roboczy, tworząc eksperyment i przekazując dane szkoleniowe i skrypty szkoleniowe.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki Języka Python.

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

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestaw SDK języka Python można uzyskać dostęp do [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefaktów obszaru roboczego, tworząc obiekt.

Utwórz obiekt obszaru `config.json` roboczego z pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Tworzenie eksperymentu uczenia maszynowego

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie utwórz eksperyment o nazwie "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Przygotowanie skryptu szkoleniowego

W tym samouczku skrypt szkoleniowy **train_iris.py** jest już dla Ciebie. W praktyce powinieneś być w stanie podjąć dowolny niestandardowy skrypt szkoleniowy, jak jest i uruchomić go za pomocą usługi Azure ML bez konieczności modyfikowania kodu.

Aby korzystać z funkcji śledzenia i metryk usługi Azure ML, dodaj niewielką ilość kodu usługi Azure ML w skrypcie szkoleniowym.  Skrypt szkoleniowy **train_iris.py** pokazuje, jak rejestrować niektóre metryki do `Run` uruchomienia usługi Azure ML przy użyciu obiektu w skrypcie.

Dostarczony skrypt szkoleniowy używa przykładowych `iris = datasets.load_iris()` danych z funkcji.  W przypadku własnych danych może być konieczne użycie kroków, takich jak [przekazywanie zestawu danych i skryptów,](how-to-train-keras.md#data-upload) aby udostępnić dane podczas szkolenia.

Skopiuj skrypt szkoleniowy **train_iris.py** do katalogu projektu.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Tworzenie lub uzyskanie celu obliczeniowego

Utwórz cel obliczeniowy dla zadania scikit-learn do uruchomienia. Scikit-learn obsługuje tylko jeden węzeł, przetwarzanie procesora.

Poniższy kod tworzy zarządzane obliczenia usługi Azure Machine Learning (AmlCompute) dla zdalnego zasobu obliczeniowego szkolenia. Tworzenie AmlCompute trwa około 5 minut. Jeśli AmlCompute o tej nazwie jest już w obszarze roboczym, ten kod pominie proces tworzenia.

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

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz, co jest artykuł [docelowy obliczeń.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Tworzenie estymatora scikit-learn

[Scikit-learn estymator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) zapewnia prosty sposób na uruchomienie scikit-learn szkolenia pracy na cel obliczeniowy. Jest implementowany za [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) pośrednictwem klasy, która może służyć do obsługi szkolenia procesora CPU z jednym węzłem.

Jeśli skrypt szkolenia wymaga dodatkowych pakietów pip lub conda do uruchomienia, można mieć pakiety zainstalowane `pip_packages` `conda_packages` na wynikowy obraz docker, przekazując ich nazwy przez argumenty i.

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


Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Prześlij bieg

[Run Obiekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomiony i po jego zakończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Podczas wykonywania przebiegu przechodzi przez następujące etapy:

- **Przygotowanie:** Obraz docker jest tworzony zgodnie z oszacowaniem TensorFlow. Obraz jest przekazyany do rejestru kontenerów obszaru roboczego i buforowany dla późniejszych uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je przeglądać w celu monitorowania postępu.

- **Skalowanie:** Klaster próbuje skalować w górę, jeśli klaster AI partii wymaga więcej węzłów do wykonania uruchomienia niż są obecnie dostępne.

- **Uruchomione**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są montowane lub kopiowane, a entry_script jest wykonywana. Wyjścia z folderu stdout i ./logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Post-Processing:** Folder ./outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisywanie i rejestrowanie modelu

Po przeszkoleniu modelu można go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md)

Dodaj następujący kod do skryptu szkolenia, train_iris.py, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym za pomocą następującego kodu. Poprzez określenie `model_framework`parametrów `model_framework_version`, `resource_configuration`i , bez kodu wdrożenia modelu staje się dostępna. Dzięki temu można bezpośrednio wdrożyć model jako usługę sieci web [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) z zarejestrowanego modelu, a obiekt definiuje zasób obliczeniowy dla usługi sieci web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>wdrażania

Model, który właśnie zarejestrowałeś, można wdrożyć dokładnie w taki sam sposób, jak każdy inny zarejestrowany model w usłudze Azure Machine Learning, niezależnie od tego, który estymator został użyty do szkolenia. Instrukcje wdrażania zawiera sekcję dotyczącą rejestrowania modeli, ale można pominąć bezpośrednio do [tworzenia miejsca docelowego obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ masz już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>(Wersja zapoznawcza) Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrażania można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla scikit-learn. Wdrożenie modelu bez kodu jest obsługiwane dla wszystkich wbudowanych typów modeli scikit-learn. Rejestrując model, jak pokazano `model_framework`powyżej `model_framework_version`z `resource_configuration` , i parametrów, można po prostu użyć funkcji statycznej [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) do wdrożenia modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

UWAGA: Te zależności są zawarte w wstępnie utworzonym kontenerze wnioskowania scikit-learn.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Pełne [instrukcje dotyczące](how-to-deploy-and-where.md) wdrażania w usłudze Azure Machine Learning w większym stopniu.


## <a name="next-steps"></a>Następne kroki

W tym artykule przeszkolono i zarejestrowałeś model scikit-learn i dowiedziałeś się o opcjach wdrażania. Zobacz te inne artykuły, aby dowiedzieć się więcej o usłudze Azure Machine Learning.

* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna dla rozproszonych szkoleń głębokiego uczenia na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
