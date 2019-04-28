---
title: Tworzenie obszaru roboczego
titleSuffix: Azure Machine Learning service
description: Umożliwia utworzenie obszaru roboczego usługi Azure Machine Learning w witrynie Azure portal, zestawu SDK, szablonu lub interfejsu wiersza polecenia. Ten obszar roboczy zawiera scentralizowanym miejscem do pracy z wszystkich artefaktów, które możesz utworzyć, korzystając z usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: ca43a6cff6a32a30d93e42f6a6624439b2fe83a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766809"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Utwórz obszar roboczy usługi Azure Machine Learning

Aby korzystać z usługi Azure Machine Learning, musisz mieć [ **obszaru roboczego usługi Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Ten obszar roboczy jest zasobem najwyższego poziomu dla usługi i scentralizowane miejsce do pracy z wszystkich artefaktów, które można utworzyć. 

W tym artykule dowiesz się, jak utworzyć obszar roboczy przy użyciu dowolnej z następujących metod: 
* [Witryny Azure portal](#portal) interfejsu
* [Usługi Azure Machine Learning zestawu SDK dla języka Python](#sdk)
* Szablon usługi Azure Resource Manager
* [Usługi Azure Machine Learning interfejs wiersza polecenia](#cli)

Obszar roboczy, którego możesz utworzyć przy użyciu czynności opisane w tym miejscu może służyć jako warunek wstępny do innych samouczki i artykuły z instrukcjami.

Jeśli chcesz użyć skryptu, aby skonfigurować automatyczne machine learning w lokalnym środowisku Python można znaleźć [GitHub Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) instrukcje.  

Po utworzeniu obszaru roboczego następujących zasobów platformy Azure są dodawane automatycznie (jeśli są one regionalnie dostępne):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Podobnie jak w przypadku innych usług platformy Azure korzystanie z usługi Machine Learning jest ograniczone pewnymi limitami i przydziałami. [Learn about quotas and how to request more (Więcej informacji o limitach przydziału i zwiększaniu limitów)](how-to-manage-quotas.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="portal"></a> Witryna Azure portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Niezależnie od tego, jak został utworzony, można wyświetlić w obszarze roboczym [witryny Azure portal](https://portal.azure.com/).  Zobacz [wyświetlić obszar roboczy](how-to-manage-workspace.md#view) Aby uzyskać szczegółowe informacje.

## <a name="sdk"></a> Python SDK

Tworzenie obszaru roboczego przy użyciu zestawu SDK języka Python. Najpierw należy zainstalować zestaw SDK.

> [!IMPORTANT]
> Instalacja zestawu SDK należy pominąć, jeśli używasz maszyny wirtualnej do nauki o danych platformy Azure lub usługi Azure Databricks.
> * Maszyny wirtualne do analizy danych na platformie Azure utworzone po 27 września 2018 r. mają preinstalowany zestaw SDK języka Python. Pomiń instalację i rozpoczynać [utworzyć obszar roboczy z zestawem SDK](#sdk-create).
> * W środowisku usługi Azure Databricks wykonaj w zamian [kroki instalacji usługi Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Użyj tych instrukcji, aby zainstalować i używać zestawu SDK z komputera lokalnego. Służące Jupyter na zdalnej maszynie wirtualnej, należy skonfigurować zdalnej, pulpitu lub X sesji terminalowej.

Przed zainstalowaniem zestawu SDK zalecamy utworzenie izolowanego środowiska języka Python. W tym artykule używane jest narzędzie [Miniconda](https://docs.conda.io/en/latest/miniconda.html), ale możesz również użyć pełnego zainstalowanego narzędzia [Anaconda](https://www.anaconda.com/) lub [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Instrukcje w tym artykule zainstaluje wszystkie pakiety, musisz uruchomić notesów Szybki Start i samouczków.  Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników.  Aby uzyskać więcej informacji dotyczących tych składników, zobacz [Instalowanie zestawu SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Instalowanie narzędzia Miniconda

[Pobierz i zainstaluj narzędzie Miniconda](https://docs.conda.io/en/latest/miniconda.html). Wybierz do zainstalowania wersję języka Python 3.7. Nie wybieraj wersji 2.x języka Python.  

### <a name="create-an-isolated-python-environment"></a>Tworzenia izolowanego środowiska języka Python

1. Otwórz Anaconda monitu, a następnie utwórz nowe środowiska conda, o nazwie *myenv* i zainstaluj język Python 3.6.5. Zestaw SDK usługi Azure Machine Learning będzie działał z językiem Python w wersji 3.5.2 lub nowszym, ale zautomatyzowane składniki uczenia maszynowego nie są w pełni funkcjonalne w wersji języka Python 3.7.  Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut. 

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
> W niektórych narzędzi wiersza polecenia może być konieczne dodania znaków cudzysłowu w następujący sposób:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Utwórz obszar roboczy z zestawem SDK

Utwórz obszar roboczy w aplikacji Jupyter Notebook przy użyciu zestawu SDK języka Python.

1. Utwórz lub przeprowadź ciągłe wdrażanie do katalogu, którego chcesz używać do celów przewodników Szybki start oraz samouczków.

1. Aby uruchomić aplikację Jupyter Notebook, wpisz to polecenie:

    ```shell
    jupyter notebook
    ```

1. W oknie przeglądarki utwórz nowy notes przy użyciu domyślnego jądra `Python 3`. 

1. Aby wyświetlić wersję zestawu SDK, wprowadź poniższy kod języka Python w komórce notesu, a następnie wykonaj go:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Znajdź wartość parametru `<azure-subscription-id>` na [liście subskrypcji w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Użyj dowolnej subskrypcji, w której masz rolę właściciela lub współautora. Aby uzyskać więcej informacji o rolach, zobacz [zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md) artykułu.

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

Zapisz szczegóły obszaru roboczego w pliku konfiguracji do bieżącego katalogu. Ten plik jest nazywany *.azureml/config.json*.  

Plik konfiguracji obszaru roboczego ułatwia późniejsze załadowanie tego samego obszaru roboczego. Można go załadować z innymi notesów i skryptów, w tym samym katalogu lub podkatalog przy użyciu kodu `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

To wywołanie interfejsu API `write_config()` utworzy plik konfiguracyjny w bieżącym katalogu. Plik *config.json* zawiera następujący kod:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Do używania Twojego obszaru roboczego w skryptach języka Python lub notesów programu Jupyter, znajduje się w innych katalogach, skopiuj ten plik do tego katalogu. Może to być plik w tym samym katalogu, w podkatalogu nazwanym *.azureml*, lub w katalogu nadrzędnym.

## <a name="resource-manager-template"></a>Szablonu usługi resource manager

Aby utworzyć obszar roboczy za pomocą szablonu, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning za pomocą szablonu](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interfejs wiersza polecenia

Aby utworzyć obszar roboczy za pomocą interfejsu wiersza polecenia, zobacz [Użyj rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

* Niezależnie od tego, jak został utworzony, można wyświetlić w obszarze roboczym [witryny Azure portal](https://portal.azure.com/).  Zobacz [wyświetlić obszar roboczy](how-to-manage-workspace.md#view) Aby uzyskać szczegółowe informacje.

* Wypróbuj obszaru roboczego z tych przewodników Szybki Start i samouczków.

    * Szybki start: [Uruchamianie notesu Jupyter w chmurze](quickstart-run-cloud-notebook.md).
    * Szybki start: [Uruchamianie notesu programu Jupyter na własnym serwerze](quickstart-run-local-notebook.md).
    * Samouczek z dwóch części: [Szkolenie](tutorial-train-models-with-aml.md) i [wdrażanie](tutorial-deploy-models-with-aml.md) trybu Klasyfikacja obrazu.
    * Samouczek z dwóch części: [Przygotowywanie danych](tutorial-data-prep.md) i [korzystanie z uczenia maszynowego automatycznych](tutorial-auto-train-models.md) do zbudowania modelu regresji.

* Dowiedz się więcej na temat [skonfigurować środowisko programowania](how-to-configure-environment.md).

* Dowiedz się więcej o [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk).
