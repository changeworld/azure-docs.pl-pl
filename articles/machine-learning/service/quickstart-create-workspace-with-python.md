---
title: 'Szybki start: Wprowadzenie do języka Python'
titleSuffix: Azure Machine Learning service
description: Rozpocznij pracę z usługą Azure Machine Learning w języku Python. Użyj zestawu SDK języka Python, aby utworzyć obszar roboczy, który stanowi podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich trenowanie oraz wdrażanie.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 8d45ca0f55b373970bfc0b1d146d5b3e2d6d66fa
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823406"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu zestawu SDK języka Python

W tym artykule użyjesz zestawu SDK Azure Machine Learning dla języka Python, aby utworzyć [obszar roboczy](concept-azure-machine-learning-architecture.md) usługi Azure Machine Learning i go używać. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich trenowanie oraz wdrażanie za pomocą usługi Machine Learning. 

Rozpoczynasz pracę od skonfigurowania własnego środowiska języka Python i serwera Jupyter Notebook. Aby uruchomić je bez instalacji, zobacz [Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning w witrynie Azure Portal](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

W tym artykule opisano następujące zagadnienia:
* Instalacja zestawu SDK dla języka Python.
* Tworzenie obszaru roboczego w subskrypcji platformy Azure.
* Tworzenie pliku konfiguracji dla obszaru roboczego do późniejszego użycia w innych notesach i skryptach.
* Pisanie kodu, który rejestruje wartości w obszarze roboczym.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

Tworzysz obszar roboczy i plik konfiguracji, których będzie można użyć jako elementów wymaganych wstępnie w innych samouczkach usługi Machine Learning i artykułach zawierających instrukcje. Podobnie jak w przypadku innych usług platformy Azure korzystanie z usługi Machine Learning jest ograniczone pewnymi limitami i przydziałami. [Learn about quotas and how to request more (Więcej informacji o limitach przydziału i zwiększaniu limitów)](how-to-manage-quotas.md).

Do obszaru roboczego zostaną automatycznie dodane następujące zasoby platformy Azure, gdy będą dostępne w regionie:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](http://aka.ms/AMLFree) już dziś.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

> [!IMPORTANT]
> Pomiń tę sekcję, jeśli używasz maszyny wirtualnej do analizy danych utworzonej po 27 września 2018 r.
> Maszyny wirtualne do analizy danych utworzone po tej dacie mają preinstalowany zestaw SDK języka Python.

Kod w tym artykule wymaga zestawu Azure Machine Learning SDK w wersji 1.0.2 lub nowszej.

Przed zainstalowaniem zestawu SDK zalecamy utworzenie izolowanego środowiska języka Python. W tym artykule używane jest narzędzie [Miniconda](https://docs.conda.io/en/latest/miniconda.html), ale możesz również użyć pełnego zainstalowanego narzędzia [Anaconda](https://www.anaconda.com/) lub [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalowanie narzędzia Miniconda

[Pobierz i zainstaluj narzędzie Miniconda](https://conda.io/miniconda.html). Wybierz środowisko Python 3.7 lub nowsze. Nie wybieraj wersji 2.x języka Python.

### <a name="create-an-isolated-python-environment"></a>Tworzenia izolowanego środowiska języka Python 

1. Otwórz okno wiersza polecenia i utwórz nowe środowisko conda o nazwie *myenv* w języku Python 3.6.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Aktywuj środowisko.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W aktywowanym środowisku conda zainstaluj zestaw SDK. Ten kod instaluje podstawowe składniki zestawu Machine Learning SDK. Ponadto instaluje serwer aplikacji Jupyter Notebook w środowisku conda. Instalacja trwa kilka minut; czas jej trwania zależy od konfiguracji maszyny.

```shell
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

Możesz użyć dodatkowych słów kluczowych, aby zainstalować inne składniki zestawu SDK:

```shell
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

W środowisku usługi Azure Databricks wykonaj w zamian [kroki instalacji usługi Databricks](how-to-configure-environment.md#azure-databricks
).


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

1. Aby uruchomić aplikację Jupyter Notebook, wpisz to polecenie:

    ```shell
    jupyter notebook
    ```

1. W oknie przeglądarki utwórz nowy notes przy użyciu domyślnego jądra `Python 3`. 

1. Aby wyświetlić wersję zestawu SDK, wprowadź poniższy kod języka Python w komórce notesu, a następnie wykonaj go:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Znajdź wartość parametru `<azure-subscription-id>` na [liście subskrypcji w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Użyj dowolnej subskrypcji, w której masz rolę właściciela lub współautora.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   Podczas wykonywania kodu może zostać wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu token uwierzytelniania będzie buforowany lokalnie.

1. Aby wyświetlić szczegóły obszaru roboczego, takie jak skojarzony magazyn, rejestr kontenerów oraz magazyn kluczy, wprowadź poniższy kod:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Zapisywanie pliku konfiguracji

Zapisz szczegóły obszaru roboczego w pliku konfiguracji do bieżącego katalogu. Ten plik ma nazwę *aml_config\config.json*.  

Wywołanie interfejsu API `write_config()` tworzy plik konfiguracyjny w bieżącym katalogu. Plik *config.json* zawiera następujący kod:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

Plik konfiguracji obszaru roboczego ułatwia późniejsze załadowanie tego samego obszaru roboczego. Możesz załadować go z innymi notesami i skryptami w tym samym katalogu lub podkatalogu. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]



## <a name="use-the-workspace"></a>Korzystanie z obszaru roboczego

Napisz kod korzystający z podstawowych interfejsów API zestawu SDK w celu śledzenia przebiegu eksperymentu.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Wyświetlanie zarejestrowanych wyników
Po zakończeniu przebiegu możesz wyświetlić przebieg eksperymentu w witrynie Azure Portal. Aby wyświetlić adres URL prowadzący do wyników dla ostatniego przebiegu, użyj następującego kodu:

```python
print(run.get_portal_url())
```

Użyj linku, aby wyświetlić zarejestrowane wartości w witrynie Azure Portal w przeglądarce.

![Zarejestrowane wartości w witrynie Azure Portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
>[!IMPORTANT]
>Utworzonych tutaj zasobów możesz użyć jako wstępnie wymaganych składników w innych samouczkach usługi Machine Learning i artykułach z instrukcjami.

Jeśli nie planujesz korzystać z zasobów, które zostały utworzone w tym artykule, usuń je, aby uniknąć ponoszenia opłat.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono zasoby niezbędne do eksperymentowania i wdrażania modeli. Uruchomiono kod w notesie i zbadano historię uruchamiania tego kodu w obszarze roboczym w chmurze.

Aby użyć tego kodu w samouczkach usługi Machine Learning, potrzebujesz w swoim środowisku jeszcze kilku pakietów.

1. W przeglądarce zamknij notes.
1. W oknie wiersza polecenia użyj skrótu klawiaturowego Ctrl+C, aby zatrzymać serwer Jupyter Notebook.
1. Zainstaluj dodatkowe pakiety.  Jeśli powyżej nie zainstalowano zestawu `azureml-sdk[automl]`, upewnij się, że zrobisz to teraz.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Po zainstalowaniu tych pakietów postępuj zgodnie z instrukcjami w samouczkach, aby przetrenować i wdrożyć model. 

> [!div class="nextstepaction"]
> [Samouczek: trenowanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)

Możesz też zapoznać się z [bardziej zaawansowanymi przykładami w witrynie GitHub](https://aka.ms/aml-notebooks).
