---
title: Konfigurowanie zdalnego celów obliczeń dla zautomatyzowanych machine learning — usługi Azure Machine Learning
description: W tym artykule wyjaśniono, jak budować modele przy użyciu zautomatyzowanych machine learning na wirtualne do analizy danych cel obliczenia zdalnego machine (DSVM) przy użyciu usługi Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 80a227b57c8df157890337f0e207519c71ae5bd6
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034624"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Szkolenie modeli za pomocą automatycznych machine learning w chmurze

W usłudze Azure Machine Learning możesz uczyć modelu na różnego rodzaju zasobów obliczeniowych, którymi zarządzasz. Obliczeniowego elementu docelowego może być komputerze lokalnym lub w chmurze. 

Możesz łatwo skalować w górę lub skalowania w poziomie eksperymentu usługi machine learning, dodając dodatkowe obliczeniowych elementów docelowych, takich jak oparta na systemie Ubuntu Data Science Virtual Machine (dsvm dystrybucji) lub usługi Azure Batch AI. Maszyny DSVM jest dostosowany obraz maszyny Wirtualnej w chmurze Microsoft Azure, przeznaczony do nauki o danych. Ma wiele popularnych danych do analizy i inne narzędzia, wstępnie zainstalowanych i skonfigurowanych.  

W tym artykule dowiesz się, jak tworzyć modele korzystające z uczenia Maszynowego z automatycznego na maszyny DSVM.  

## <a name="how-does-remote-differ-from-local"></a>Czym różni się zdalne z lokalnego?

Samouczek "[Wytrenuj model klasyfikacji z automatycznego ML](tutorial-auto-train-models.md)" dowiesz się, jak używać komputera lokalnego do nauczenia modelu za pomocą zautomatyzowanego uczenia Maszynowego.  Przepływ pracy podczas szkolenia lokalnie ma zastosowanie również do celów zdalnego, a także. Za pomocą zdalnego mocy obliczeniowej iteracjami eksperymentów uczenia Maszynowego z automatycznego są wykonywane asynchronicznie. Dzięki temu, że anulujesz konkretnej iteracji, wyrażeń kontrolnych stanu wykonywania i kontynuować pracę na innych komórek w notesie Jupyter. To w opracowywaniu zdalnie, należy najpierw utworzyć cel obliczenia zdalnego takich jak Azure, maszyny wirtualnej DSVM, jest skonfigurowana i przesyłać tam kod.

Ten artykuł przedstawia dodatkowe kroki potrzebne do uruchomienia zautomatyzowane ML Wypróbuj zdalnego maszyny wirtualnej DSVM.  Obiekt workspace `ws`, w tym samouczku jest używana w całym kodzie, w tym miejscu.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Utwórz zasób

Tworzenie maszyny wirtualnej DSVM w obszarze roboczym (`ws`), jeśli jeszcze nie istnieje. Jeśli wcześniej utworzono maszyny DSVM, ten kod pominie proces tworzenia i ładuje szczegóły dotyczące istniejących zasobów do `dsvm_compute` obiektu.  

**Szacowany czas**: tworzenie maszyny wirtualnej trwa około 5 minut.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Teraz możesz używać `dsvm_compute` obiektu jako zdalnego obliczeniowego elementu docelowego.

Ograniczenia dotyczące nazwy maszyny wirtualnej DSVM obejmują:
+ Musi zawierać mniej niż 64 znaki.  
+ Nie może zawierać żadnego z następujących znaków: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

>[!Warning]
>Jeśli tworzenie zakończy się niepowodzeniem z komunikatem o kwalifikowaniu zakupu portalu Marketplace:
>    1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
>    1. Rozpocznij tworzenie maszyny wirtualnej DSVM 
>    1. Wybierz "chcesz utworzyć programowo" umożliwia programowe tworzenie
>    1. Zakończ bez faktycznie tworzenia maszyny Wirtualnej
>    1. Uruchom ponownie kod tworzenia


## <a name="access-data-using-get-data-file"></a>Dostęp do danych przy użyciu pliku pobieranie danych

Podaj zasób zdalny dostęp do danych szkoleniowych. Dla automatycznego ML eksperymentów, systemem zdalnym obliczeń dane mają zostać pobrane, za pomocą `get_data()` funkcji.  

Aby zapewnić dostęp, musisz mieć:
+ Tworzenie pliku get_data.py zawierającego `get_data()` — funkcja 
* Umieść plik w katalogu głównym folderu zawierającego skryptów 

Należy hermetyzować kod, aby odczytać dane z magazynu obiektów blob lub dysk lokalny, w pliku get_data.py. W poniższym przykładzie kodu dane pochodzą z pakietu skryptu sklearn.

>[!Warning]
>Jeśli używasz zdalnego obliczeniowych, a następnie należy użyć `get_data()` do wykonania przekształceń danych.


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

## <a name="configure-automated-machine-learning-experiment"></a>Konfigurowanie automatycznych eksperymentu uczenia maszynowego

Określ ustawienia `AutoMLConfig`.  (Zobacz [pełną listę parametrów]() i ich możliwe wartości.)

W ustawieniach `run_configuration` ustawiono `run_config` obiekt, który zawiera ustawienia i konfiguracja maszyny DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-automated-machine-learning-training-experiment"></a>Przesyłanie automatycznych eksperymentu uczenia maszynowego, szkolenia

Teraz przesłać konfiguracji do automatycznego wybierania algorytmów, parametry hyper i trenowanie modelu. (Dowiedz się, [więcej informacji o ustawieniach]() dla `submit` metody.)

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
Zostaną wyświetlone dane wyjściowe podobne do tego:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>Zapoznaj się z wyników

Można użyć tego samego elementu widget Jupyter niż w [samouczek szkolenia](tutorial-auto-train-models.md#explore-the-results) wykres i tabelę wyników.

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
Oto obraz statyczny elementu widget.  W notesie możesz kliknąć każdego wiersza w tabeli, aby wyświetlić właściwości wykonywania i danych wyjściowych dzienników, uruchamiania.   Można również Użyj listy rozwijanej powyżej wykresu, aby wyświetlić wykres wszystkie dostępne metryki dla każdej iteracji.

![element widget tabeli](./media/how-to-auto-train-remote/table.png)
![widżet wykresu](./media/how-to-auto-train-remote/plot.png)

Widżet Wyświetla adres URL można użyć, aby zobaczyć i zapoznaj się z osobą szczegóły przebiegu.
 
### <a name="view-logs"></a>Wyświetlanie dzienników

Znajdowanie dzienników na DSVM w folderze/tmp/azureml_run / {iterationid} / dzienniki usługi Azure ml.

## <a name="example"></a>Przykład

`automl/03.auto-ml-remote-execution.ipynb` Koncepcji w tym artykule pokazano, notesu.  Pobierz ten notes:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, [jak skonfigurować ustawienia automatycznego szkolenia](how-to-configure-auto-train.md).
