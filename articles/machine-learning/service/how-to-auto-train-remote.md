---
title: Automatyczne ML zdalnego obliczeniowych elementów docelowych
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanych machine learning na wirtualne do analizy danych cel obliczenia zdalnego machine (DSVM) przy użyciu usługi Azure Machine Learning
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
ms.openlocfilehash: 6f2d71abeacee531b21a8276f621367dd39a39d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820394"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Szkolenie modeli za pomocą automatycznych machine learning w chmurze

W usłudze Azure Machine Learning podstawie uczyć modele na różnego rodzaju zasobów obliczeniowych, którymi zarządzasz. Obliczeniowego elementu docelowego może być komputerze lokalnym lub w chmurze.

Możesz łatwo skalować w górę lub skalowania w poziomie eksperymentu usługi machine learning, dodając dodatkowe obliczeniowych elementów docelowych. Target — opcje obliczeń obejmują oparta na systemie Ubuntu Data Science Virtual Machine (dsvm dystrybucji) lub obliczeniowego usługi Azure Machine Learning. Maszyny DSVM jest dostosowany obraz maszyny Wirtualnej w chmurze Microsoft Azure, przeznaczony do nauki o danych. Ma wiele popularnych danych do analizy i inne narzędzia, wstępnie zainstalowanych i skonfigurowanych.  

W tym artykule dowiesz się, jak tworzyć modele korzystające z automatycznych uczenia Maszynowego na maszyny DSVM.

## <a name="how-does-remote-differ-from-local"></a>Czym różni się zdalne z lokalnego?

Samouczek "[Wytrenuj model klasyfikacji przy użyciu uczenia maszynowego automatycznych](tutorial-auto-train-models.md)" dowiesz się, jak używać komputera lokalnego do uczenia modelu z automatycznych ML.  Przepływ pracy podczas szkolenia lokalnie ma zastosowanie również do celów zdalnego, a także. Jednak za pomocą zdalnego mocy obliczeniowej, zautomatyzowane iteracjami eksperymentów uczenia Maszynowego są wykonywane asynchronicznie. Ta funkcja umożliwia anulowanie konkretnej iteracji, obejrzyj stan wykonania lub kontynuować pracę na innych komórek w notesie Jupyter. To w opracowywaniu zdalnie, należy najpierw utworzyć zdalnego obliczeniowego elementu docelowego, takich jak Azure, maszyny wirtualnej DSVM.  Następnie skonfiguruj zasób zdalny i przesyłać tam kod.

Ten artykuł przedstawia dodatkowe kroki wymagane do uruchamiania automatycznych eksperymentu uczenia Maszynowego na zdalnego maszyny wirtualnej DSVM.  Obiekt workspace `ws`, w tym samouczku jest używana w całym kodzie, w tym miejscu.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Utwórz zasób

Tworzenie maszyny wirtualnej DSVM w obszarze roboczym (`ws`), jeśli jeszcze nie istnieje. Jeśli wcześniej utworzono maszyny DSVM, ten kod pominie proces tworzenia i ładuje szczegóły dotyczące istniejących zasobów do `dsvm_compute` obiektu.  

**Szacowany czas**: Tworzenie maszyny wirtualnej zajmuje około 5 minut.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
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

Ten kod nie tworzy nazwy użytkownika lub hasła dla maszyny wirtualnej DSVM, dla którego zainicjowano. Jeśli chcesz nawiązać bezpośrednie połączenie maszyny Wirtualnej, przejdź do strony [witryny Azure portal](https://portal.azure.com) Aby utworzyć poświadczenia.  

### <a name="attach-existing-linux-dsvm"></a>Dołącz istniejące DSVM systemu Linux

Można również dołączyć istniejącej maszyny wirtualnej DSVM systemu Linux jako obliczeniowego elementu docelowego. W tym przykładzie korzysta z istniejącej maszyny wirtualnej DSVM, ale nie tworzy nowego zasobu.

> [!NOTE]
>
> Poniższy kod używa [RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) docelowej klasy, aby dołączyć istniejącej maszyny Wirtualnej jako obliczeniowego elementu docelowego.
> [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) klasy staną się przestarzałe w przyszłych wersjach na rzecz tym wzorcu projektowym.

Uruchom poniższy kod, aby utworzyć obliczeniowego elementu docelowego na podstawie istniejących DSVM systemu Linux.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached-vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

Teraz możesz używać `compute_target` obiektu jako zdalnego obliczeniowego elementu docelowego.

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
                             compute_target = dsvm_compute,
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
                             compute_target = dsvm_compute,
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

* shap_values: Informacje o wyjaśnienie, generowane przez lib kształtu
* expected_values: Oczekiwana wartość zastosowany do zestawu danych X_train model.
* overall_summary: Wartości ważności funkcja poziomu modelu posortowane w kolejności malejącej
* Overall: W nazwach funkcji sortowane w takiej samej kolejności jak overall_summary
* per_class_summary: Klasa wartości ważności funkcja poziomu posortowane w kolejności malejącej. Dostępne tylko w przypadku klasyfikacji
* per_class_imp: Sortowane w takiej samej kolejności jak per_class_summary nazwy funkcji. Dostępne tylko w przypadku klasyfikacji

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

[How-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) koncepcji w tym artykule pokazano, notesu. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, [jak skonfigurować ustawienia automatycznego szkolenia](how-to-configure-auto-train.md).
