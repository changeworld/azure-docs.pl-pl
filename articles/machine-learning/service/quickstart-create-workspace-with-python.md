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
ms.date: 11/09/2018
ms.openlocfilehash: fff08131af277b20034ad23c354b70e73ae32f2e
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578284"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Szybki start: rozpoczęcie pracy z usługą Azure Machine Learning w języku Python

W tym przewodniku Szybki start użyjesz zestawu Azure Machine Learning SDK dla języka Python, aby utworzyć [obszar roboczy](concept-azure-machine-learning-architecture.md) usługi Machine Learning i go używać. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich uczenie oraz wdrażanie za pomocą usługi Machine Learning. W tym przewodniku Szybki start rozpoczynasz pracę od skonfigurowania własnego środowiska języka Python i serwera notesów Jupyter. Aby uruchomić go bez żadnej instalacji, zobacz [Szybki start: rozpoczynanie pracy z usługą Azure Machine Learning w witrynie Azure Portal](quickstart-get-started.md).


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

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

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu Azure Machine Learning SDK w wersji 0.1.74 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="install-the-sdk"></a>Instalacja zestawu SDK

*Pomiń tę sekcję, jeśli używasz maszyny wirtualnej nauki o danych utworzonej po 27 września 2018 r.* Te maszyny wirtualne nauki o danych są preinstalowane z zestawem SDK języka Python.

Przed zainstalowaniem zestawu SDK zalecamy utworzenie izolowanego środowiska języka Python. W tym przewodniku Szybki start jest używane narzędzie [Miniconda](https://conda.io/docs/user-guide/install/index.html), ale możesz również użyć pełnego zainstalowanego narzędzia [Anaconda](https://www.anaconda.com/) lub [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalowanie narzędzia Miniconda


[Pobierz](https://conda.io/miniconda.html) i zainstaluj narzędzie Miniconda. Wybierz wersję języka Python 3.7 lub nowszą. Nie wybieraj wersji 2.x języka Python.

### <a name="create-an-isolated-python-environment"></a>Tworzenia izolowanego środowiska języka Python 

Otwórz okno wiersza polecenia. Utwórz nowe środowisko conda o nazwie `myenv` w języku Python 3.6.

```shell
conda create -n myenv -y Python=3.6
```

Aktywuj środowisko.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W aktywowanym środowisku conda zainstaluj zestaw SDK. Poniższe polecenie służy do instalowania podstawowych składników zestawu Machine Learning SDK. Ponadto instaluje serwer aplikacji Jupyter Notebook w środowisku conda `myenv`. Instalacja trwa kilka minut; jest to zależne od konfiguracji maszyny.

```shell
# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```



## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby uruchomić aplikację Jupyter Notebook, wprowadź to polecenie.
```shell
jupyter notebook
```

W oknie przeglądarki utwórz nowy notes przy użyciu domyślnego jądra `Python 3`. 

Aby wyświetlić wersję zestawu SDK, wprowadź poniższy kod języka Python w komórce notesu, a następnie wykonaj go.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Zapisywanie pliku konfiguracji

Zapisz szczegóły obszaru roboczego w pliku konfiguracji do bieżącego katalogu. Ten plik ma nazwę „aml_config\config.json”.  

Ten plik konfiguracji obszaru roboczego ułatwia późniejsze załadowanie tego samego obszaru roboczego. Możesz załadować go z innymi notesami i skryptami w tym samym katalogu lub podkatalogu. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>Następne kroki

Utworzono zasoby niezbędne do eksperymentowania i wdrażania modeli. Uruchomiono też kod w notesie. Zbadano historię przebiegów dotyczącą tego kodu w obszarze roboczym w chmurze.

Potrzebujesz jeszcze kilku pakietów w swoim środowisku, które będą używane w samouczkach usługi Machine Learning.

1. W przeglądarce zamknij notes.
1. W oknie wiersza polecenia użyj polecenia `Ctrl`+`C`, aby zatrzymać serwer notesów.
1. Zainstaluj dodatkowe pakiety.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]

    # install run history widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable run history widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

    Możesz również użyć innych „dodatkowych” słów kluczowych w celu zainstalowania dodatkowych składników zestawu SDK.

    ```shell
    # install the base SDK and auto ml components
    pip install azureml-sdk[automl]

    # install the base SDK and model explainability component
    pip install azureml-sdk[explain]

    # install the base SDK and experimental components
    pip install azureml-sdk[contrib]

    # install the base SDK and automl components in Azure Databricks environment
    # read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
    pip install azureml-sdk[databricks]
    ```


Po zainstalowaniu tych pakietów postępuj zgodnie z instrukcjami w samouczkach, aby nauczyć i wdrożyć model. 

> [!div class="nextstepaction"]
> [Samouczek: uczenie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)

Możesz też zapoznać się z [bardziej zaawansowanymi przykładami w witrynie GitHub](https://aka.ms/aml-notebooks).
