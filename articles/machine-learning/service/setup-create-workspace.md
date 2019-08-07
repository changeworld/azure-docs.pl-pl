---
title: Tworzenie obszaru roboczego
titleSuffix: Azure Machine Learning service
description: Użyj Azure Portal, zestawu SDK, szablonu lub interfejsu wiersza polecenia, aby utworzyć obszar roboczy usługi Azure Machine Learning. Ten obszar roboczy zawiera centralne miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 4d689b51a53a27a0e85a52724752d959c4c2506d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775063"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning

Aby można było korzystać z usługi Azure Machine Learning, wymagany jest [**obszar roboczy usługi Azure Machine Learning**](concept-workspace.md).  Ten obszar roboczy jest zasobem najwyższego poziomu dla usługi i oferuje scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. 

W tym artykule dowiesz się, jak utworzyć obszar roboczy przy użyciu dowolnej z następujących metod: 
* Interfejs [Azure Portal](#portal)
* [Zestaw Azure Machine Learning SDK dla języka Python](#sdk)
* Szablon Azure Resource Manager
* [Interfejs wiersza polecenia Azure Machine Learning](#cli)

Obszar roboczy, który tworzysz przy użyciu kroków tego artykułu, może być używany jako warunek wstępny do innych samouczków i artykułów z instrukcjami.

Jeśli chcesz użyć skryptu, aby skonfigurować automatyczne Uczenie maszynowe w lokalnym środowisku języka Python, zapoznaj się z instrukcjami w witrynie [GitHub Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) .  

Po utworzeniu obszaru roboczego następujące zasoby platformy Azure są dodawane automatycznie (jeśli są dostępne w regionie):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Aby zminimalizować koszty, ACR jest **załadowane z opóźnieniem** do momentu utworzenia obrazów wdrożenia.
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Podobnie jak w przypadku innych usług platformy Azure korzystanie z usługi Machine Learning jest ograniczone pewnymi limitami i przydziałami. [Learn about quotas and how to request more (Więcej informacji o limitach przydziału i zwiększaniu limitów)](how-to-manage-quotas.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="portal"></a>Azure Portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Niezależnie od tego, jak został utworzony, możesz wyświetlić obszar roboczy w [Azure Portal](https://portal.azure.com/).  Aby uzyskać szczegółowe informacje [, zobacz Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view) .

## <a name="sdk"></a>Zestaw SDK języka Python

Utwórz obszar roboczy przy użyciu zestawu SDK języka Python. Najpierw należy zainstalować zestaw SDK.

> [!IMPORTANT]
> Pomiń instalację zestawu SDK, jeśli używasz usługi Azure Data Science Virtual Machine lub Azure Databricks.
> * Maszyny wirtualne do analizy danych na platformie Azure utworzone po 27 września 2018 r. mają preinstalowany zestaw SDK języka Python. Pomiń instalację i zacznij od [utworzenia obszaru roboczego za pomocą zestawu SDK](#sdk-create).
> * W środowisku usługi Azure Databricks wykonaj w zamian [kroki instalacji usługi Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Użyj tych instrukcji, aby zainstalować zestaw SDK i korzystać z niego z komputera lokalnego. Aby użyć Jupyter na zdalnej maszynie wirtualnej, Skonfiguruj pulpit zdalny lub sesję terminala X.

Przed zainstalowaniem zestawu SDK zalecamy utworzenie izolowanego środowiska języka Python. W tym artykule używane jest narzędzie [Miniconda](https://docs.conda.io/en/latest/miniconda.html), ale możesz również użyć pełnego zainstalowanego narzędzia [Anaconda](https://www.anaconda.com/) lub [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Instrukcje zawarte w tym artykule spowodują zainstalowanie wszystkich pakietów potrzebnych do uruchamiania notesów Szybki Start i samouczków.  Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników.  Aby uzyskać więcej informacji dotyczących tych składników, zobacz [Instalowanie zestawu SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Instalowanie narzędzia Miniconda

[Pobierz i zainstaluj narzędzie Miniconda](https://docs.conda.io/en/latest/miniconda.html). Wybierz do zainstalowania wersję języka Python 3.7. Nie wybieraj wersji 2.x języka Python.  

### <a name="create-an-isolated-python-environment"></a>Tworzenia izolowanego środowiska języka Python

1. Otwórz wiersz polecenia Anaconda, a następnie utwórz nowe środowisko Conda o nazwie *MyENV* i zainstaluj język Python 3.6.5. Zestaw SDK usługi Azure Machine Learning będzie działał z językiem Python w wersji 3.5.2 lub nowszym, ale zautomatyzowane składniki uczenia maszynowego nie są w pełni funkcjonalne w wersji języka Python 3.7.  Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Aktywuj środowisko.

    ```shell
    conda activate myenv
    ```

1. Włącz jądra ipython specyficzne dla środowiska:

    ```shell
    conda install notebook ipykernel
    ```

    Następnie utwórz jądro:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

1. W aktywowanym środowisku conda zainstaluj podstawowe składniki zestawu SDK uczenia maszynowego z funkcjami aplikacji Jupyter Notebook. Instalacja trwa kilka minut; czas jej trwania zależy od konfiguracji maszyny.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Aby użyć tego środowiska do celów samouczków dotyczących usługi Azure Machine Learning, zainstaluj te pakiety.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Aby użyć tego środowiska do celów samouczków dotyczących usługi Azure Machine Learning, zainstaluj zautomatyzowane składniki uczenia maszynowego.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> W niektórych narzędziach wiersza polecenia może być konieczne dodanie cudzysłowów w następujący sposób:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a>Tworzenie obszaru roboczego za pomocą zestawu SDK

Utwórz obszar roboczy w aplikacji Jupyter Notebook przy użyciu zestawu SDK języka Python.

1. Utwórz lub przeprowadź ciągłe wdrażanie do katalogu, którego chcesz używać do celów przewodników Szybki start oraz samouczków.

1. Aby uruchomić aplikację Jupyter Notebook, wpisz to polecenie:

    ```shell
    jupyter notebook
    ```

1. W oknie przeglądarki utwórz nowy notes przy użyciu domyślnego jądra `Python 3`. 

1. Aby wyświetlić wersję zestawu SDK, wprowadź poniższy kod języka Python w komórce notesu, a następnie wykonaj go:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Znajdź wartość parametru `<azure-subscription-id>` na [liście subskrypcji w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Użyj dowolnej subskrypcji, w której masz rolę właściciela lub współautora. Aby uzyskać więcej informacji na temat ról, zobacz artykuł [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md) .

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Podczas wykonywania kodu może zostać wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu token uwierzytelniania będzie buforowany lokalnie.

1. Aby wyświetlić szczegóły obszaru roboczego, takie jak skojarzony magazyn, rejestr kontenerów oraz magazyn kluczy, wprowadź poniższy kod:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Zapisywanie pliku konfiguracji

Zapisz szczegóły obszaru roboczego w pliku konfiguracji do bieżącego katalogu. Ten plik ma nazwę *. Azure/config. JSON*.  

Plik konfiguracji obszaru roboczego ułatwia późniejsze załadowanie tego samego obszaru roboczego. Możesz załadować ją z innymi notesami i skryptami w tym samym katalogu lub podkatalogu przy użyciu kodu `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

To wywołanie interfejsu API `write_config()` utworzy plik konfiguracyjny w bieżącym katalogu. Plik. Pouczenie */config. JSON* zawiera następujące elementy:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Aby użyć obszaru roboczego w skryptach języka Python lub notesach Jupyter znajdujących się w innych katalogach, Skopiuj ten plik do tego katalogu. Plik może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym.

## <a name="resource-manager-template"></a>Szablon Menedżera zasobów

Aby utworzyć obszar roboczy z szablonem, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning przy użyciu szablonu](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interfejs wiersza polecenia

Aby utworzyć obszar roboczy za pomocą interfejsu wiersza polecenia, zobacz [Korzystanie z rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

* Niezależnie od tego, jak został utworzony, możesz wyświetlić obszar roboczy w [Azure Portal](https://portal.azure.com/).  Aby uzyskać szczegółowe informacje [, zobacz Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view) .

* Wypróbuj swój obszar roboczy, korzystając z tych samouczków.

    * Dwuczęściowy samouczek: [Skonfiguruj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) i [Przeszkol swój pierwszy model](tutorial-1st-experiment-sdk-train.md).
    * Dwuczęściowy samouczek: [Uczenie](tutorial-train-models-with-aml.md) i [wdrażanie](tutorial-deploy-models-with-aml.md) trybu klasyfikacji obrazu.
    * Dwuczęściowy samouczek: [Przygotuj dane](tutorial-data-prep.md) i [korzystaj](tutorial-auto-train-models.md) z zautomatyzowanej uczenia maszynowego, aby skompilować model regresji.

* Dowiedz się więcej na temat [konfigurowania środowiska deweloperskiego](how-to-configure-environment.md).

* Dowiedz się więcej na temat [zestawu SDK Azure Machine Learning dla języka Python](https://aka.ms/aml-sdk).
