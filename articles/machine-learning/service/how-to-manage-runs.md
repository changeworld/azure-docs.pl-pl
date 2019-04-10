---
title: Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchomić, stan, tagu i Organizuj eksperymentów uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 4/5/2019
ms.openlocfilehash: 726273024a2da0cea5207c86140f3c31263a208f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426747"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python

[Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) zapewnia różne metody monitorowania i organizować oraz zarządzać nimi przebiegów szkoleniowych i eksperymentowanie w usłudze.

Niniejszy instruktaż zawiera przykłady następujących zadań:

* [Monitor wydajności uruchamiania](#monitor)
* [Anulowanie lub niepowodzenie uruchomienia](#cancel)
* [Utwórz podrzędny przebiegi](#children)
* [Tag i znaleźć przebiegi](#tag)

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne następujące czynności:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md).

* Azure Machine Learning SDK dla język Python jest zainstalowany (wersja 1.0.21 lub nowszej). Aby zainstalować lub aktualizacji do najnowszej wersji zestawu SDK, przejdź do [instalacji/aktualizacji zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) strony.

    Aby sprawdzić swoją wersję zestawu SDK usługi Azure Machine Learning, użyj następującego kodu.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-and-status-a-run"></a>Początkowy i stan przebiegu

Konfigurowanie eksperymentu, importując [obszaru roboczego](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [eksperymentować](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Uruchom](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), i [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) klas z [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) pakietu.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Rozpocząć przebiegu i jego proces rejestrowania za pomocą [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metody.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

Pobierz stan przebiegu o [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Aby uzyskać więcej informacji dotyczących używania wykonywania [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Po uruchomieniu zakończą się pomyślnie, należy użyć [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodę, aby oznaczyć je jako ukończone.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Możesz również użyć języka Python `with...as` wzorca. W tym kontekście Uruchom automatycznie oznaczy się jako ukończony podczas przebiegu jest poza zakresem. Dzięki temu nie trzeba ręcznie oznaczyć działanie jako ukończone.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Anulowanie lub niepowodzenie uruchomienia

 Jeśli zauważysz błąd lub przebieg wydaje się być długiego czasu zakończenia, należy użyć [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metodę, aby zatrzymać działanie przed zakończeniem i oznacz go jako anulowany.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Jeśli przebieg zakończy, ale zawiera błąd, taki jak, użyto skrypt szkoleniowy niepoprawne, możesz użyć [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metodę, aby oznaczyć go jako nie powiodła się.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Utwórz podrzędny przebiegi

Tworzenie obiektu podrzędnego przebiegi do grupowania powiązanych przebiegów, np. dla różnych hiperparametrycznego dostrajania iteracji.

Ten przykład kodu używa tego skryptu hello_with_children.py, aby utworzyć partię pięciu przebiegów podrzędnych z w obrębie przesłane uruchomienia za pomocą [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metody.

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Podrzędne uruchamia automatycznie pełną przechodzące poza zakres tej asysty.

Można również uruchomić podrzędne uruchamia jedno po drugim, ale ponieważ każda tworzenia powoduje wysłanie wywołania sieci, jest mniej wydajne niż przesyłania partii przebiegów.

Użyj [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody zapytania podrzędne uruchamia określonego elementu nadrzędnego.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Tag i znaleźć przebiegi

W usłudze Azure Machine Learning można użyć właściwości i tagów, aby ułatwić organizowanie i wykonywania zapytań względem przebiegów, aby uzyskać ważne informacje.

### <a name="add-properties-and-tags"></a>Dodaj właściwości i tagów

Użyj [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) dodać wyszukiwalnych metadanych do przebiegów. Na przykład poniższy kod dodaje właściwości "Autor" do uruchomienia.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Właściwości są niezmienne, co jest przydatne jako trwałe rekord na potrzeby inspekcji. Poniższy przykład kodu spowoduje błąd, ponieważ już dodana "usługi Azure ml użytkownika" jako właściwość "Autor" w poprzednim kodzie.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Tagi, jednak są zmieniane. Użyj [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) dodawanie można wyszukiwać i istotnych informacji dla konsumentów eksperymentu.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Można również dodać tag prostego ciągu. Pojawia się w słowniku tag o wartości `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Właściwości zapytania i tagów

Można tworzyć zapytania działa w ramach eksperymentu, które pasują do określonych właściwości i tagów.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Przykład notesów

Następujące notesów zademonstrowania koncepcji w tym artykule:

* Aby dowiedzieć się więcej na temat rejestrowania interfejsów API, zobacz [notesu rejestrowanie interfejsu API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Aby uzyskać dodatkowe informacje na temat zarządzania systemem za pomocą zestawu SDK usługi Azure Machine Learning, zobacz [Zarządzanie uruchomienia notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak rejestrować metryki dla eksperymentów, zobacz [dziennika metryki podczas wysyłanie przebiegów szkoleniowych](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) artykułu.