---
title: Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchomić, ustawić stan tagu i organizowanie eksperymenty z uczeniem maszynowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: a93492b8ea97500fe3c761f3ac0c49f8c1342d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074955"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python

[Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) i [interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md) zapewniają różne metody monitorowania i organizować oraz zarządzać nimi przebiegów szkoleniowych i eksperymentowanie w usłudze.

W tym artykule przedstawiono przykłady następujących zadań:

* Monitor wydajności uruchamiania.
* Anulowanie lub niepowodzenie uruchomienia.
* Tworzenie obiektu podrzędnego przebiegów.
* Tag i Znajdź przebiegów.

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszaru roboczego usługi Azure Machine Learning](setup-create-workspace.md).

* Zestaw Azure Machine Learning SDK for Python (wersja 1.0.21 lub nowszej). Aby zainstalować lub aktualizacji do najnowszej wersji zestawu SDK, zobacz [Zainstaluj lub zaktualizuj zestaw SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Aby sprawdzić swoją wersję zestawu SDK usługi Azure Machine Learning, użyj następującego kodu:

    ```Python
    print(azureml.core.VERSION)
    ```

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) i [rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Rozpocznij przebieg i jego proces rejestrowania

### <a name="using-the-sdk"></a>Używanie zestawu SDK

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

### <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia

Można uruchomić przebiegu eksperymentu, wykonaj następujące kroki:

1. Z powłoki lub wiersza polecenia należy użyć wiersza polecenia platformy Azure do uwierzytelniania w Twojej subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Dołącz Konfiguracja obszaru roboczego do folderu, który zawiera skrypt szkolenia. Zastąp `myworkspace` z obszarem roboczym usługi Azure Machine Learning. Zastąp `myresourcegroup` z grupą zasobów platformy Azure, która zawiera obszar roboczy:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie umożliwia utworzenie `.azureml` podkatalogu zawierającego pliki przykładowe runconfig i conda środowiska. Zawiera ona także `config.json` pliku, który jest używany do komunikowania się z obszarem roboczym usługi Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [dołączyć az ml folderu](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Aby rozpocząć przebieg, użyj następującego polecenia. Korzystając z tego polecenia, należy określić nazwę pliku runconfig (tekst przed \*.runconfig, jeśli patrzy systemu plików) dla parametru - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Polecenia utworzone `.azureml` podkatalogu, która zawiera dwa pliki runconfig przykład. 
    >
    > Jeśli masz skrypt w języku Python, która tworzy obiekt konfiguracji uruchamiania programowo, możesz użyć [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) go zapisać jako plik runconfig.
    >
    > Aby uzyskać więcej plików runconfig przykładu, zobacz [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Aby uzyskać więcej informacji, zobacz [az ml Uruchom Prześlij skrypt](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitorowanie stanu przebiegu

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Pobierz stan przebiegu o [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metody.

```Python
print(notebook_run.get_status())
```

Aby uzyskać dodatkowe szczegóły dotyczące uruchomienia, użyj [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metody.

```Python
notebook_run.get_details()
```

Po uruchomieniu zakończą się pomyślnie, należy użyć [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodę, aby oznaczyć je jako ukończone.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Jeśli używasz języka Python `with...as` wzorzec, Uruchom automatycznie oznaczy się jako ukończone, gdy działanie jest poza zakresem. Nie potrzebujesz ręcznie oznaczyć działanie jako ukończone.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

### <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia

1. Aby wyświetlić listę przebiegów eksperymentu, użyj następującego polecenia. Zastąp `experiment` nazwą swojego eksperymentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    To polecenie zwraca dokument JSON, który wyświetla informacje na temat przebiegów w tym eksperymencie.

    Aby uzyskać więcej informacji, zobacz [az ml eksperymentować listy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Aby wyświetlić informacje w określonym przebiegu, użyj następującego polecenia. Zastąp `runid` o identyfikatorze przebiegu:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    To polecenie zwraca dokument JSON, który wyświetla informacje o przebiegu.

    Aby uzyskać więcej informacji, zobacz [az ml pokazu](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Anulowanie lub niepowodzenie uruchomienia

Jeśli zauważysz błąd lub przebieg trwa zbyt długo, można anulować przebiegu.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby anulować przebiegu przy użyciu zestawu SDK, należy użyć [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metody:

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Jeśli zakończy się przebieg, ale zawiera błąd (na przykład skrypt szkoleniowy niepoprawne użyto), możesz użyć [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metodę, aby oznaczyć go jako nie powiodła się.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia

Aby anulować przebiegu przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `runid` o identyfikatorze przebiegu

```azurecli-interactive
az ml run cancel -r runid
```

Aby uzyskać więcej informacji, zobacz [az ml Uruchom Anuluj](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Utwórz podrzędny przebiegi

Tworzenie obiektu podrzędnego przebiegi do grupowania powiązanych przebiegów, takich jak dla różne iteracje strojenia hiperparametrycznego.

> [!NOTE]
> Podrzędne uruchomienia można tworzyć tylko za pomocą zestawu SDK.

W tym przykładzie kodu użyto `hello_with_children.py` skrypt, aby utworzyć partię pięć przebiegów podrzędnych z w ramach wykonywania przesłane za pomocą [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metody:

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
> Przechodzące poza zakresem, przebiegów podrzędne są automatycznie oznaczane jako ukończone.

Można również uruchomić podrzędne uruchamia jedno po drugim, ale ponieważ każda tworzenia powoduje wysłanie wywołania sieci, jest mniej wydajne niż przesyłania partii przebiegów.

Aby wysłać zapytanie działa podrzędny określonego elementu nadrzędnego, użyj [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tag i znaleźć przebiegi

W usłudze Azure Machine Learning można użyć właściwości i tagów, aby ułatwić organizowanie i wykonywania zapytań względem przebiegów, aby uzyskać ważne informacje.

### <a name="add-properties-and-tags"></a>Dodaj właściwości i tagów

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby dodać wyszukiwalnych metadanych do przebiegów, użyj [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metody. Na przykład, poniższy kod dodaje `"author"` właściwość do uruchomienia:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Właściwości są niezmienne, dzięki czemu mogą utworzyć rekord stałe na potrzeby inspekcji. Poniższy kod przykładowy będzie skutkowało błędem, ponieważ już dodana `"azureml-user"` jako `"author"` wartości właściwości w poprzednim kodzie:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

W przeciwieństwie do właściwości tagi są zmieniane. Aby dodać informacje można wyszukiwać i zrozumiała dla konsumentów eksperymentu, użyj [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metody.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Można również dodać tagi prostego ciągu. Gdy tagi te pojawią się w słowniku tag, mają wartość `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia

> [!NOTE]
> Przy użyciu interfejsu wiersza polecenia, można tylko dodać lub zaktualizować tagów.

Aby dodać lub zaktualizować tagu, użyj następującego polecenia:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Aby uzyskać więcej informacji, zobacz [az ml aktualizacją](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Właściwości zapytania i tagów

Można tworzyć zapytania działa w ramach eksperymentu, aby powrócić do listy uruchomień, które pasują do określonych właściwości i tagów.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure obsługuje [JMESPath](http://jmespath.org) zapytania, które mogą być używane do filtrowania działa na podstawie właściwości i tagów. Zapytania JMESPath za pomocą wiersza polecenia platformy Azure, określ ją za pomocą `--query` parametru. W poniższych przykładach pokazano podstawowe zapytania przy użyciu właściwości i tagów:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Aby uzyskać więcej informacji o zapytaniach dotyczących wyników wiersza polecenia platformy Azure, zobacz [dane wyjściowe polecenia wiersza polecenia platformy Azure w kwerendzie](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Przykład notesów

Następujące notesów zademonstrowania koncepcji w tym artykule:

* Aby dowiedzieć się więcej na temat rejestrowania interfejsów API, zobacz [notesu rejestrowanie interfejsu API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Aby uzyskać więcej informacji o zarządzaniu działa przy użyciu zestawu SDK usługi Azure Machine Learning, zobacz [Zarządzanie uruchomienia notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak rejestrować metryki dla eksperymentów, zobacz [dziennika metryki podczas wysyłanie przebiegów szkoleniowych](how-to-track-experiments.md).
