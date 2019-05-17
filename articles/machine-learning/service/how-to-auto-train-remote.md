---
title: Automatyczne ML zdalnego obliczeniowych elementów docelowych
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanych machine learning na usługi Azure Machine Learning zdalnego obliczeniowego elementu docelowego przy użyciu usługi Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6a18bdf3a2a1ccd60ff20d21ebd99f4f6e15e38f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551332"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Szkolenie modeli za pomocą automatycznych machine learning w chmurze

W usłudze Azure Machine Learning podstawie uczyć modele na różnego rodzaju zasobów obliczeniowych, którymi zarządzasz. Obliczeniowego elementu docelowego może być komputerze lokalnym lub w chmurze.

Możesz łatwo skalować w górę lub skalowania w poziomie eksperymentu usługi machine learning, dodając dodatkowe obliczeniowych elementów docelowych, takich jak Azure Machine Learning Compute (AmlCompute). AmlCompute jest infrastrukturę obliczeniową zarządzana, która pozwala na łatwe tworzenie jednego lub wielu węzłach obliczeniowych.

W tym artykule dowiesz się, jak tworzyć modele korzystające z automatycznych uczenia Maszynowego przy użyciu AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Czym różni się zdalne z lokalnego?

Samouczek "[Wytrenuj model klasyfikacji przy użyciu uczenia maszynowego automatycznych](tutorial-auto-train-models.md)" dowiesz się, jak używać komputera lokalnego do uczenia modelu z automatycznych ML.  Przepływ pracy podczas szkolenia lokalnie ma zastosowanie również do celów zdalnego, a także. Jednak za pomocą zdalnego mocy obliczeniowej, zautomatyzowane iteracjami eksperymentów uczenia Maszynowego są wykonywane asynchronicznie. Ta funkcja umożliwia anulowanie konkretnej iteracji, obejrzyj stan wykonania lub kontynuować pracę na innych komórek w notesie Jupyter. To w opracowywaniu zdalnie, należy najpierw utworzyć zdalnego obliczeniowego elementu docelowego, takie jak AmlCompute. Następnie skonfiguruj zasób zdalny i przesyłać tam kod.

Ten artykuł przedstawia dodatkowe kroki wymagane do uruchamiania automatycznych eksperymentu uczenia Maszynowego w celu AmlCompute zdalnego. Obiekt workspace `ws`, w tym samouczku jest używana w całym kodzie, w tym miejscu.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Utwórz zasób

Utwórz docelową AmlCompute w obszarze roboczym (`ws`), jeśli jeszcze nie istnieje.  

**Szacowany czas**: Tworzenie elementu docelowego AmlCompute zajmuje około 5 minut.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl" #Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", 
                                                            # for GPU, use "STANDARD_NC6"
                                                            #vm_priority = 'lowpriority', # optional
                                                            max_nodes = 6)

compute_target = ComputeTarget.create(ws, amlcompute_cluster_name, provisioning_config)
    
# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(show_output = True, min_node_count = None, timeout_in_minutes = 20)
```

Teraz możesz używać `compute_target` obiektu jako zdalnego obliczeniowego elementu docelowego.

Ograniczenia dotyczące nazwy klastra, obejmują:
+ Musi zawierać mniej niż 64 znaki.  
+ Nie może zawierać żadnego z następujących znaków: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

## <a name="access-data-using-getdata-file"></a>Dostęp do danych przy użyciu pliku get_data

Podaj zasób zdalny dostęp do danych szkoleniowych. Eksperymenty uczenia maszynowego automatycznych systemem zdalnym obliczeń, aby uzyskać dane mają zostać pobrane, za pomocą `get_data()` funkcji.  

Aby zapewnić dostęp, musisz mieć:
+ Tworzenie pliku get_data.py zawierającego `get_data()` — funkcja 
+ Umieść dostępne jako ścieżkę bezwzględną pliku w katalogu 

Należy hermetyzować kod, aby odczytać dane z magazynu obiektów blob lub dysk lokalny, w pliku get_data.py. W poniższym przykładzie kodu dane pochodzą z pakietu skryptu sklearn.

>[!Warning]
>Jeśli używasz zdalnego obliczeniowych, a następnie należy użyć `get_data()` gdzie przekształceń danych są wykonywane. Jeśli musisz zainstalować dodatkowe biblioteki do przekształcenia danych jako część get_data(), istnieją dodatkowe kroki, które należy stosować. Zapoznaj się [notesu auto-ml — przygotowania danych przykładowych](https://aka.ms/aml-auto-ml-data-prep ) Aby uzyskać szczegółowe informacje.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

Określ ustawienia `AutoMLConfig`.  (Zobacz [pełną listę parametrów](how-to-configure-auto-train.md#configure-experiment) i ich możliwe wartości.)

W ustawieniach `run_configuration` ustawiono `run_config` obiekt, który zawiera ustawienia i konfiguracja maszyny DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Włącz objaśnienia dotyczące modelu

Ustawianie opcjonalne `model_explainability` parametru w `AutoMLConfig` konstruktora. Ponadto obiektu dataframe weryfikacji muszą być przekazywane jako parametr `X_valid` korzystania z funkcji explainability modelu.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Przesyłanie eksperymentu szkolenia

Teraz przesłać konfiguracji do automatycznego wybierania algorytmów, parametry hyper i trenowanie modelu.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
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

Można użyć tego samego elementu widget Jupyter niż w [samouczek szkolenia](tutorial-auto-train-models.md#explore-the-results) wykres i tabelę wyników.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Oto obraz statyczny widżetu.  W notesie możesz kliknąć każdego wiersza w tabeli, aby wyświetlić właściwości wykonywania i danych wyjściowych dzienników, uruchamiania.   Można również Użyj listy rozwijanej powyżej wykresu, aby wyświetlić wykres wszystkie dostępne metryki dla każdej iteracji.

![tabela widżetu](./media/how-to-auto-train-remote/table.png)
![wykres widżetu](./media/how-to-auto-train-remote/plot.png)

Widżet Wyświetla adres URL można użyć, aby zobaczyć i zapoznaj się z osobą szczegóły przebiegu.
 
### <a name="view-logs"></a>Wyświetlanie dzienników

Znajdowanie dzienników na nauki, w obszarze `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Najlepsze wyjaśnienie modelu

Pobieranie danych wyjaśnienie modelu pozwala wyświetlić szczegółowe informacje na temat modeli w celu zwiększenia przejrzystości w uruchomionej w zapleczu. W tym przykładzie możesz uruchomić wyjaśnienia modelu tylko najlepszy model dopasowania. Po uruchomieniu wszystkich modeli w potoku, będzie skutkować znaczną ilość czasu wykonywania. Zawiera informacje o wyjaśnienie modelu:

* shap_values: Informacje o wyjaśnienie, generowane przez lib kształtu.
* expected_values: Oczekiwana wartość zastosowany do zestawu danych X_train model.
* overall_summary: Funkcja poziomu ważności wartości modelu posortowane w kolejności malejącej.
* Overall: Sortowane w takiej samej kolejności jak overall_summary nazwy funkcji.
* per_class_summary: Klasa wartości ważności funkcja poziomu posortowane w kolejności malejącej. Jest to dostępne tylko w przypadku klasyfikacji.
* per_class_imp: Sortowane w takiej samej kolejności jak per_class_summary nazwy funkcji. Jest to dostępne tylko w przypadku klasyfikacji.

Użyj poniższego kodu, które można wybierać najlepsze potoku swoje iteracje. `get_output` Metoda zwraca najlepszy przebieg i dopasowanego modelu dla ostatniego dopasowania wywołania.

```python
best_run, fitted_model = remote_run.get_output()
```

Importuj `retrieve_model_explanation` funkcji i uruchamianie na najlepszy model.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Drukowanie wyników dla `best_run` zmienne wyjaśnienie, którą chcesz wyświetlić.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Drukowanie `best_run` wyjaśnienie zmienne podsumowania wyników w następujących danych wyjściowych.

![Dane wyjściowe konsoli explainability modelu](./media/how-to-auto-train-remote/expl-print.png)

Można również wizualizować znaczenie funkcji za pomocą widżetu interfejsu użytkownika, a także interfejs użytkownika sieci web w witrynie Azure portal w obszarze roboczym.

![Model explainability interfejsu użytkownika](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Przykład

[How-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) koncepcji w tym artykule pokazano, notesu. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, [jak skonfigurować ustawienia automatycznego szkolenia](how-to-configure-auto-train.md).
