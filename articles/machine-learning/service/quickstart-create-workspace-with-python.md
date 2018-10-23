---
title: 'Szybki start: tworzenie obszaru roboczego usługi Machine Learning za pomocą zestawu SDK języka Python — Azure Machine Learning'
description: Rozpocznij pracę z usługą Azure Machine Learning. Zainstaluj zestaw SDK języka Python i użyj go do utworzenia obszaru roboczego. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich uczenie oraz wdrażanie za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: cc348ca50b942b6b8b1474ed4dac4067d107a4af
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378003"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Szybki start: rozpoczęcie pracy z usługą Azure Machine Learning w języku Python

W tym przewodniku Szybki start użyjesz zestawu Azure Machine Learning SDK dla języka Python, aby utworzyć [obszar roboczy](concept-azure-machine-learning-architecture.md) usługi Machine Learning i go używać. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich uczenie oraz wdrażanie za pomocą usługi Machine Learning.

W tym samouczku zainstalujesz zestaw SDK języka Python oraz wykonasz następujące czynności:

* Tworzenie obszaru roboczego w subskrypcji platformy Azure.
* Tworzenie pliku konfiguracji dla obszaru roboczego do późniejszego użycia w innych notesach i skryptach.
* Pisanie kodu, który rejestruje wartości w obszarze roboczym.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

W tym przewodniku Szybki start utworzysz obszar roboczy i plik konfiguracji. Możesz ich użyć jako wstępnie wymaganych składników na potrzeby innych samouczków usługi Machine Learning i artykułów z instrukcjami. Podobnie jak w przypadku innych usług platformy Azure korzystanie z usługi Machine Learning jest ograniczone określonymi limitami i przydziałami. [Learn about quotas and how to request more (Więcej informacji o limitach przydziału i zwiększaniu limitów)](how-to-manage-quotas.md).

Do obszaru roboczego zostaną automatycznie dodane następujące zasoby platformy Azure, gdy będą dostępne w regionie:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="install-the-sdk"></a>Instalacja zestawu SDK

*Pomiń tę sekcję, jeśli używasz maszyny wirtualnej nauki o danych utworzonej po 27 września 2018 r.* Te maszyny wirtualne nauki o danych są preinstalowane z zestawem SDK języka Python.

Przed zainstalowaniem zestawu SDK zalecamy utworzenie izolowanego środowiska języka Python. W tym przewodniku Szybki start jest używane narzędzie [Miniconda](https://conda.io/docs/user-guide/install/index.html), ale możesz również użyć pełnego zainstalowanego narzędzia [Anaconda](https://www.anaconda.com/) lub [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalowanie narzędzia Miniconda


[Pobierz](https://conda.io/miniconda.html) i zainstaluj narzędzie Miniconda. Wybierz wersję języka Python 3.7 lub nowszą. Nie wybieraj wersji 2.x języka Python.

### <a name="create-an-isolated-python-environment"></a>Tworzenia izolowanego środowiska języka Python 

Otwórz okno wiersza polecenia. Utwórz nowe środowisko conda o nazwie `myenv` w języku Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Aktywuj środowisko.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W aktywowanym środowisku conda zainstaluj zestaw SDK. Ten kod instaluje podstawowe składniki zestawu Machine Learning SDK. Ponadto instaluje serwer aplikacji Jupyter Notebook w środowisku conda `myenv`. Instalacja trwa **około 4 minut**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby uruchomić aplikację Jupyter Notebook, wprowadź to polecenie.
```sh
jupyter notebook
```

W oknie przeglądarki utwórz nowy notes przy użyciu domyślnego jądra `Python 3`. 

Aby wyświetlić wersję zestawu SDK, wprowadź poniższy kod języka Python w komórce notesu, a następnie wykonaj go.

```python
import azureml.core
print(azureml.core.VERSION)
```

Utwórz nową grupę zasobów platformy Azure i nowy obszar roboczy.

Znajdź wartość parametru `<azure-subscription-id>` na [liście subskrypcji w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Użyj dowolnej subskrypcji, w której masz rolę właściciela lub współautora.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

Wykonanie powyższego kodu może spowodować wyzwolenie nowego okna przeglądarki umożliwiającego zalogowanie się na koncie platformy Azure. Po zalogowaniu token uwierzytelniania będzie buforowany lokalnie.

Aby wyświetlić szczegóły obszaru roboczego, takie jak skojarzony magazyn, rejestr kontenerów oraz magazyn kluczy, wprowadź poniższy kod.

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Zapisywanie pliku konfiguracji

Zapisz szczegóły obszaru roboczego w pliku konfiguracji do bieżącego katalogu. Ten plik ma nazwę „aml_config\config.json”.  

Ten plik konfiguracji obszaru roboczego ułatwia późniejsze załadowanie tego samego obszaru roboczego. Możesz załadować go z innymi notesami i skryptami w tym samym katalogu lub podkatalogu. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

Wywołanie interfejsu API `write_config()` tworzy plik konfiguracyjny w bieżącym katalogu. Plik `config.json` zawiera poniższy skrypt.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Korzystanie z obszaru roboczego

Napisz kod korzystający z podstawowych interfejsów API zestawu SDK w celu śledzenia przebiegu eksperymentu.

```python
from azureml.core import Experiment

# create a new experiment
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>Wyświetlanie zarejestrowanych wyników
Po zakończeniu przebiegu możesz wyświetlić przebieg eksperymentu w witrynie Azure Portal. Użyj następującego kodu, aby wyświetlić adres URL w wynikach ostatniego przebiegu.

```python
print(run.get_portal_url())
```

Użyj linku, aby wyświetlić zarejestrowane wartości w witrynie Azure Portal w przeglądarce.

![Zarejestrowane wartości w portalu](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
>[!IMPORTANT]
>Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Machine Learning i artykułach z instrukcjami.

Jeśli nie planujesz używać utworzonych tutaj zasobów, usuń je, aby uniknąć naliczania opłat.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Następne kroki

Utworzono zasoby niezbędne do eksperymentowania i wdrażania modeli. Uruchomiono też kod w notesie. Zbadano historię przebiegów dotyczącą tego kodu w obszarze roboczym w chmurze.

Potrzebujesz jeszcze kilku pakietów w swoim środowisku, które będą używane w samouczkach usługi Machine Learning.

1. W przeglądarce zamknij notes.
1. W oknie wiersza polecenia użyj polecenia `Ctrl`+`C`, aby zatrzymać serwer notesów.
1. Zainstaluj dodatkowe pakiety.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Po zainstalowaniu tych pakietów postępuj zgodnie z instrukcjami w samouczkach, aby nauczyć i wdrożyć model. 

> [!div class="nextstepaction"]
> [Samouczek: uczenie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)

Możesz też zapoznać się z [bardziej zaawansowanymi przykładami w witrynie GitHub](https://aka.ms/aml-notebooks).
