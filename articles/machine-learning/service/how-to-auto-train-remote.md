---
title: Automatyczne cele obliczeń zdalnych ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć modele przy użyciu automatycznego uczenia maszynowego na Azure Machine Learning zdalnym miejscu docelowym obliczeń przy użyciu Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8c50a1ba79fc07f62e319c6dceb75c32a9e8609f
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287140"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Szkolenie modeli za pomocą automatycznych machine learning w chmurze

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W usłudze Azure Machine Learning podstawie uczyć modele na różnego rodzaju zasobów obliczeniowych, którymi zarządzasz. Obiekt docelowy obliczeń może być komputerem lokalnym lub zasobem w chmurze.

Eksperyment uczenia maszynowego można łatwo skalować w górę lub w poziomie, dodając dodatkowe cele obliczeniowe, takie jak Azure Machine Learning COMPUTE (AmlCompute). AmlCompute to zarządzana infrastruktura obliczeniowa, która umożliwia łatwe tworzenie obliczeń jednego lub wielowęzłowego.

W tym artykule dowiesz się, jak skompilować model przy użyciu zautomatyzowanej ML z AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Czym różni się zdalne z lokalnego?

Samouczek "[uczenie modelu klasyfikacji przy użyciu automatycznego uczenia maszynowego](tutorial-auto-train-models.md)" uczy się, w jaki sposób używać komputera lokalnego do uczenia modelu o zautomatyzowanej ml. Przepływ pracy podczas szkolenia lokalnie ma zastosowanie również do celów zdalnego, a także. Jednak za pomocą zdalnego mocy obliczeniowej, zautomatyzowane iteracjami eksperymentów uczenia Maszynowego są wykonywane asynchronicznie. Ta funkcja umożliwia anulowanie konkretnej iteracji, obejrzyj stan wykonania lub kontynuować pracę na innych komórek w notesie Jupyter. Aby przeprowadzić uczenie zdalne, należy najpierw utworzyć zdalne miejsce docelowe obliczeń, takie jak AmlCompute. Następnie skonfiguruj zasób zdalny i przesyłać tam kod.

W tym artykule przedstawiono dodatkowe kroki niezbędne do uruchomienia zautomatyzowanego eksperymentu ML na zdalnym miejscu docelowym AmlCompute. Obiekt obszaru roboczego, `ws`, z samouczka jest używany w całym kodzie tutaj.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Utwórz zasób

Utwórz obiekt docelowy AmlCompute w obszarze roboczym (`ws`), jeśli jeszcze nie istnieje.

**Szacowany czas**: Tworzenie elementu docelowego AmlCompute trwa około 5 minut.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Teraz można użyć obiektu `compute_target` jako elementu docelowego obliczeń zdalnych.

Ograniczenia nazw klastrów obejmują:
+ Musi zawierać mniej niż 64 znaki.
+ Nie może zawierać żadnego z następujących znaków: `\` ~! @ # $% ^ & * () = + _ [] {} \\\\ |; : \' \\", < >/?. `

## <a name="access-data-using-tabulardataset-function"></a>Dostęp do danych za pomocą funkcji TabularDataset

Zdefiniowane training_data jako `TabularDataset`i etykieta, które są przesyłane do zautomatyzowanej ML w AutoMLConfig. `from_delimited_files` domyślnie ustawia `infer_column_types` na wartość true, co spowoduje automatyczne wywnioskowanie typu kolumny. 

Jeśli chcesz ręcznie ustawić typy kolumn, możesz ustawić argument `set_column_types`, aby ręcznie ustawić typ każdej kolumny. W poniższym przykładzie kodu dane pochodzą z pakietu skryptu sklearn.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="create-run-configuration"></a>Utwórz konfigurację uruchamiania

Aby udostępnić zależności dla skryptu get_data. PR, zdefiniuj obiekt `RunConfiguration` ze zdefiniowanym `CondaDependencies`. Użyj tego obiektu dla parametru `run_configuration` w `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy==1.16.2"])
run_config.environment.python.conda_dependencies = dependencies
```

Zapoznaj się z tym [przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) , aby uzyskać dodatkowy przykład tego wzorca projektowego.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu
Określ ustawienia dla `AutoMLConfig`.  (Zobacz [pełną listę parametrów](how-to-configure-auto-train.md#configure-experiment) i ich wartości).

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Przesyłanie eksperymentu szkolenia

Teraz przesłać konfiguracji do automatycznego wybierania algorytmów, parametry hyper i trenowanie modelu.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Zostaną wyświetlone dane wyjściowe podobne do poniższego przykładu:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Zapoznaj się z wyników

Możesz użyć tego samego [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , jak pokazano w [samouczku szkolenia](tutorial-auto-train-models.md#explore-the-results) , aby wyświetlić wykres i tabelę wyników.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Oto obraz statyczny widżetu.  W notesie możesz kliknąć każdego wiersza w tabeli, aby wyświetlić właściwości wykonywania i danych wyjściowych dzienników, uruchamiania.   Można również Użyj listy rozwijanej powyżej wykresu, aby wyświetlić wykres wszystkie dostępne metryki dla każdej iteracji.

![tabela widżetu](./media/how-to-auto-train-remote/table.png)
![wykres widżetu](./media/how-to-auto-train-remote/plot.png)

Widżet Wyświetla adres URL można użyć, aby zobaczyć i zapoznaj się z osobą szczegóły przebiegu.  

Jeśli nie jesteś w notesie Jupyter, możesz wyświetlić adres URL od samego uruchomienia:

```
remote_run.get_portal_url()
```

Te same informacje są dostępne w obszarze roboczym.  Aby dowiedzieć się więcej na temat tych wyników, zobacz [Opis zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

## <a name="example"></a>Przykład

W poniższym [notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) przedstawiono Koncepcje opisane w tym artykule.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak skonfigurować ustawienia automatycznego szkolenia](how-to-configure-auto-train.md).
* Zapoznaj się z artykułem [jak](how-to-machine-learning-interpretability-automl.md) włączyć funkcję interpretacji modelu w zautomatyzowanych eksperymentach ml.
