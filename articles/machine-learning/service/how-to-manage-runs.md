---
title: Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchamiać, ustawiać stan, oznaczać i organizować eksperymenty uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2019
ms.openlocfilehash: 9b58d6e189c891d0dd2917d7d150f133dc35f917
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019101"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python

[Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) i [interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md) udostępnia różne metody monitorowania, organizowania i zarządzania przebiegami w celu szkolenia i eksperymentowania.

W tym artykule przedstawiono przykłady następujących zadań:

* Monitoruj wydajność uruchamiania.
* Anulowanie lub niepowodzenie uruchomienia.
* Utwórz uruchomienia podrzędne.
* Tagi i Znajdź uruchomienia.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

* Zestaw Azure Machine Learning SDK dla języka Python (wersja 1.0.21 lub nowsza). Aby zainstalować lub zaktualizować najnowszą wersję zestawu SDK, zobacz [Instalowanie lub aktualizowanie zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Aby sprawdzić wersję zestawu SDK Azure Machine Learning, użyj następującego kodu:

    ```python
    print(azureml.core.VERSION)
    ```

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) i [rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Rozpocznij przebieg i proces rejestrowania

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Skonfiguruj eksperyment przez zaimportowanie klas [obszaru roboczego](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [eksperymentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [uruchamiania](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)i [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) z pakietu [Azure. Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) .

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Rozpocznij przebieg i proces rejestrowania przy użyciu [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metody.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby rozpocząć wykonywanie eksperymentu, wykonaj następujące czynności:

1. Z poziomu powłoki lub wiersza polecenia Użyj interfejsu wiersza poleceń platformy Azure do uwierzytelniania w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Dołącz konfigurację obszaru roboczego do folderu, który zawiera skrypt szkoleniowy. Zamień `myworkspace` na obszar roboczy usługi Azure Machine Learning. Zamień `myresourcegroup` na grupę zasobów platformy Azure, która zawiera obszar roboczy:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie tworzy `.azureml` podkatalog zawierający przykładowe pliki środowiska runconfig i Conda. Zawiera `config.json` również plik, który jest używany do komunikowania się z obszarem roboczym Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [AZ ml folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Aby uruchomić przebieg, użyj następującego polecenia. Korzystając z tego polecenia, należy określić nazwę pliku runconfig (tekst przed \*. runconfig, Jeśli przeglądasz system plików) z parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Polecenie utworzyłopodkatalog,któryzawiera`.azureml` dwa przykładowe pliki runconfig.
    >
    > Jeśli masz skrypt języka Python, który programowo tworzy obiekt konfiguracji uruchomieniowej, możesz użyć [runconfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) , aby zapisać go jako plik runconfig.
    >
    > Więcej przykładowych plików runconfig można znaleźć [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)w temacie.

    Aby uzyskać więcej informacji, zobacz [AZ ml Run Submit-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitorowanie stanu przebiegu

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Pobierz stan uruchomienia za pomocą [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metody.

```python
print(notebook_run.get_status())
```

Aby uzyskać identyfikator uruchomienia, czas wykonywania i dodatkowe szczegóły dotyczące przebiegu, użyj [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metody.

```python
print(notebook_run.get_details())
```

Po pomyślnym zakończeniu przebiegu Użyj [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metody, aby oznaczyć ją jako zakończoną.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Jeśli używasz wzorca `with...as` projektowego Python, przebieg zostanie automatycznie oznaczony jako wykonany, gdy przebieg jest poza zakresem. Nie musisz ręcznie oznaczyć przebiegu jako zakończony.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

1. Aby wyświetlić listę przebiegów eksperymentu, użyj następującego polecenia. Zamień `experiment` na nazwę eksperymentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    To polecenie zwraca dokument JSON zawierający informacje o przebiegach dla tego eksperymentu.

    Aby uzyskać więcej informacji, zobacz [AZ ml eksperyment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Aby wyświetlić informacje dotyczące określonego przebiegu, użyj następującego polecenia. Zamień `runid` na identyfikator przebiegu:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    To polecenie zwraca dokument JSON, który zawiera listę informacji o przebiegu.

    Aby uzyskać więcej informacji, zobacz [AZ ml Run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Anulowanie lub niepowodzenie przebiegów

Jeśli zauważysz błąd lub jeśli wykonywanie przebiegu trwa zbyt długo, możesz anulować przebieg.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby anulować przebieg przy użyciu zestawu SDK, użyj [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metody:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Jeśli przebieg zostanie zakończony, ale zawiera błąd (na przykład użyto nieprawidłowego skryptu szkoleniowego), można użyć [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) metody, aby oznaczyć ją jako niepowodzenie.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby anulować uruchomienie przy użyciu interfejsu wiersza polecenia, należy użyć następujące polecenie. Zamień `runid` na identyfikator przebiegu

```azurecli-interactive
az ml run cancel -r runid
```

Aby uzyskać więcej informacji, zobacz [AZ ml Run Cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Tworzenie przebiegów podrzędnych

Utwórz uruchomienia podrzędne, aby grupować powiązane z nimi przebiegi, na przykład dla różnych iteracji dostrajania parametrów.

> [!NOTE]
> Uruchomienia podrzędne można tworzyć tylko za pomocą zestawu SDK.

Ten przykład kodu używa `hello_with_children.py` skryptu do tworzenia partii pięciu przebiegów podrzędnych z poziomu przesłanego przebiegu przy [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) użyciu metody:

```python
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
> Gdy przechodzą poza zakres, uruchomienia podrzędne są automatycznie oznaczane jako ukończone.

Aby wydajnie tworzyć wiele podrzędnych przebiegów, [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) Użyj metody. Ponieważ każdy proces tworzenia jest wywoływany przez połączenie sieciowe, utworzenie partii przebiegów jest bardziej wydajne niż ich tworzenie.

### <a name="submit-child-runs"></a>Prześlij uruchomienia podrzędne

Uruchomienia podrzędne mogą być również przesyłane z przebiegu nadrzędnego. Dzięki temu można tworzyć hierarchie uruchomień nadrzędnych i podrzędnych, z których każda działa w różnych obiektach docelowych obliczeń połączonych przez wspólny identyfikator przebiegu nadrzędnego.

Użyj metody ["submit_child ()"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-count-none--tag-key-none--tag-values-none-) , aby przesłać podrzędny przebieg z przebiegu nadrzędnego. Aby to zrobić w skrypcie uruchomienia nadrzędnego, Pobierz kontekst uruchamiania i prześlij podrzędny przebieg przy użyciu metody "submit_child" "w wystąpieniu kontekstu.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

W ramach uruchomienia podrzędnego można wyświetlić identyfikator uruchomienia obiektu nadrzędnego:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Uruchomienia podrzędne zapytania

Aby zbadać podrzędne uruchomienia określonego elementu nadrzędnego, użyj [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody. Argument "rekursywny = true" "" "jest poszukiwany w zagnieżdżonym drzewie elementów podrzędnych i podrzędne.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tagi i Znajdź przebiegi

W usłudze Azure Machine Learning można użyć właściwości i tagów, aby ułatwić organizowanie i wykonywanie zapytań dotyczących przebiegów w celu uzyskania ważnych informacji.

### <a name="add-properties-and-tags"></a>Dodaj właściwości i Tagi

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby dodać metadane z możliwością wyszukiwania do przebiegów, [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) Użyj metody. Na przykład poniższy kod dodaje `"author"` właściwość do przebiegu:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Właściwości są niezmienne, więc tworzą stałe rekordy do celów inspekcji. Poniższy przykład kodu powoduje błąd, ponieważ został już dodany `"azureml-user"` `"author"` jako wartość właściwości w poprzednim kodzie:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

W przeciwieństwie do właściwości, Tagi są modyfikowalne. Aby dodać wyszukiwanie i istotne informacje dla klientów eksperymentu, użyj [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metody.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Możesz również dodać proste Tagi ciągu. Gdy Tagi są wyświetlane w słowniku tagów jako klucze, mają one wartość `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

> [!NOTE]
> Za pomocą interfejsu wiersza polecenia można dodawać i aktualizować tylko Tagi.

Aby dodać lub zaktualizować tag, użyj następującego polecenia:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Aby uzyskać więcej informacji, zobacz [AZ ml Run Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Właściwości zapytania i Tagi

Możesz wykonywać zapytania w ramach eksperymentu, aby zwrócić listę przebiegów zgodnych z określonymi właściwościami i tagami.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure obsługuje zapytania [JMESPath](http://jmespath.org) , które mogą służyć do filtrowania przebiegów w oparciu o właściwości i Tagi. Aby użyć zapytania JMESPath z interfejsem wiersza polecenia platformy Azure, określ go `--query` za pomocą parametru. W poniższych przykładach przedstawiono podstawowe zapytania przy użyciu właściwości i tagów:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących wyników interfejsu wiersza polecenia platformy Azure, zobacz temat [zapytanie dotyczące danych wyjściowych poleceń platformy Azure](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Przykład notesów

W następujących notesach przedstawiono Koncepcje opisane w tym artykule:

* Aby dowiedzieć się więcej na temat interfejsów API rejestrowania, zobacz artykuł [Rejestrowanie interfejsu API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Aby uzyskać więcej informacji na temat zarządzania przebiegami z zestawem SDK Azure Machine Learning, zobacz [Notes zarządzanie przebiegiem](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rejestrować metryki dla eksperymentów, zobacz [Dziennik metryk podczas przebiegów szkoleniowych](how-to-track-experiments.md).
