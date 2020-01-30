---
title: Rozszerzenie interfejsu wiersza polecenia
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning dla wiersza polecenia platformy Azure. Wiersza polecenia platformy Azure jest narzędziem wiersza polecenia dla wielu platform, która umożliwia pracę z zasobami w chmurze platformy Azure. Rozszerzenie Machine Learning umożliwia korzystanie z Azure Machine Learning. Interfejs wiersza polecenia ML tworzy i zarządza zasobami, takimi jak obszar roboczy, magazyny danych, zbiory obiektów, potoki, modele i wdrożenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: fe9cee44970da94c346caffc0d5fe76ffebf4a0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845165"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Użyj rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Interfejsu wiersza polecenia usługi Azure Machine Learning to rozszerzenie [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), interfejs wiersza polecenia dla wielu platform na platformie Azure. To rozszerzenie udostępnia polecenia do pracy z Azure Machine Learning. Pozwala to zautomatyzować działania uczenia maszynowego. Poniższa lista zawiera przykładowe akcje, które można wykonać przy użyciu rozszerzenia interfejsu wiersza polecenia:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Zarejestruj modele uczenia maszynowego do użycia przez klientów

+ Pakowanie, wdrażanie i śledzenia w cyklu życia modeli usługi machine learning

Interfejs wiersza polecenia nie jest zamiennikiem dla zestawu SDK usługi Azure Machine Learning. Jest to narzędzie uzupełniające, które jest zoptymalizowane pod kątem obsługi wysoce sparametryzowanych zadań, które dobrze pasują do automatyzacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby korzystać z interfejsu wiersza polecenia, musi mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Pełne dokumenty referencyjne

Znajdź [pełne dokumenty referencyjne dla rozszerzenia platformy Azure — interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie interfejsu wiersza polecenia Machine Learning, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Przykładowe pliki, których można użyć z poniższymi poleceniami, można znaleźć [tutaj](https://aka.ms/azml-deploy-cloud).

Po wyświetleniu monitu wybierz `y` można zainstalować rozszerzenia.

Aby sprawdzić, czy rozszerzenie zostało zainstalowane, użyj następującego polecenia, aby wyświetlić listę określonych ML podpoleceń polecenia:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aktualizowanie rozszerzenia

Aby zaktualizować rozszerzenie interfejsu wiersza polecenia Machine Learning, użyj następującego polecenia:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Usuwanie rozszerzenia

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Poniższe polecenia pokazują, jak zarządzać zasoby używane przez usługi Azure Machine Learning przy użyciu interfejsu wiersza polecenia.

+ Jeśli jeszcze tego nie zrobiono, Utwórz grupę zasobów:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Utwórz obszar roboczy Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > To polecenie tworzy obszar roboczy wersja podstawowa. Aby utworzyć obszar roboczy przedsiębiorstwa, użyj przełącznika `--sku enterprise` za pomocą polecenia `az ml workspace create`. Aby uzyskać więcej informacji na temat wersji Azure Machine Learning, zobacz [co to jest Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Aby uzyskać więcej informacji, zobacz [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Dołącz konfigurację obszaru roboczego do folderu, aby włączyć świadomość kontekstową interfejsu wiersza polecenia.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie tworzy podkatalog `.azureml` zawierający przykładowe pliki środowiska runconfig i Conda. Zawiera również plik `config.json` używany do komunikowania się z obszarem roboczym Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [AZ ml folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Dołączanie kontenera obiektów blob platformy Azure jako magazynu danych.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml datastore Attach-BLOB](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Przekaż pliki do magazynu danych.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Dołącz klaster AKS jako obiekt docelowy obliczeń.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Utwórz nowy element docelowy AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Uruchom eksperymenty

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, należy określić nazwę pliku runconfig (tekst przed \*. runconfig, Jeśli przeglądasz system plików) z parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` polecenie tworzy podkatalog `.azureml`, który zawiera dwa przykładowe pliki runconfig. 
    >
    > Jeśli masz skrypt języka Python, który programowo tworzy obiekt konfiguracji uruchomieniowej, możesz użyć [runconfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) , aby zapisać go jako plik runconfig.
    >
    > Pełny schemat runconfig można znaleźć w tym [pliku JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schemat służy do samodokumentowania przez klucz `description` każdego obiektu. Ponadto istnieją wyliczenia dla możliwych wartości i fragmentu szablonu na końcu.

    Aby uzyskać więcej informacji, zobacz [AZ ml Run Submit-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Wyświetl listę eksperymentów:

    ```azurecli-interactive
    az ml experiment list
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml eksperyment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Zarządzanie zestawem danych

Następujące polecenia przedstawiają sposób pracy z zestawami danych w Azure Machine Learning:

+ Rejestrowanie zestawu danych:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Aby uzyskać informacje dotyczące formatu pliku JSON używanego do definiowania zestawu danych, użyj `az ml dataset register --show-template`.

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet dataregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiwizuj aktywny lub przestarzały zestaw danych:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet Archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Zaniechanie zestawu danych:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet — przestarzałe](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Wyświetl listę wszystkich zestawów danych w obszarze roboczym:

    ```azurecli-interactive
    az ml dataset list
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Pobierz szczegóły zestawu danych:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Aktywuj ponownie zarchiwizowany lub przestarzały zestaw danych:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Wyrejestruj zestaw danych:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml DataSet Unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Zarządzanie środowiskiem

Następujące polecenia pokazują, jak tworzyć, rejestrować i wyświetlać listę [środowisk](how-to-configure-environment.md) Azure Machine Learning dla obszaru roboczego:

+ Utwórz pliki szkieletowe dla środowiska:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml szkielet środowiska](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Zarejestruj środowisko:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Wyświetl listę zarejestrowanych środowisk:

    ```azurecli-interactive
    az ml environment list
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml Environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Pobierz zarejestrowane środowisko:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml Environment Download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schemat konfiguracji środowiska

Jeśli użyto polecenia `az ml environment scaffold`, generuje on szablon `azureml_environment.json` pliku, który można modyfikować i służy do tworzenia niestandardowych konfiguracji środowiska przy użyciu interfejsu wiersza polecenia. Obiekt najwyższego poziomu jest luźno mapowany do klasy [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) w zestawie SDK języka Python. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Poniższa tabela zawiera szczegółowe informacje o każdym polu najwyższego poziomu w pliku JSON, jego typie i opisie. Jeśli typ obiektu jest połączony z klasą z zestawu Python SDK, istnieje luźne dopasowanie 1:1 między wszystkimi polami JSON i nazwą zmiennej publicznej w klasie języka Python. W niektórych przypadkach pole może mapować do argumentu konstruktora, a nie zmiennej klasy. Na przykład pole `environmentVariables` mapuje do zmiennej `environment_variables` w klasie [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .

| Pole JSON | Typ | Opis |
|---|---|---|
| `name` | `string` | Nazwa środowiska. Nie rozpoczynaj nazwy od **firmy Microsoft** ani platformy **Azure**. |
| `version` | `string` | Wersja środowiska. |
| `environmentVariables` | `{string: string}` | Mapa skrótu nazw zmiennych środowiskowych i wartości. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Obiekt definiujący środowisko i interpreter języka Python do użycia w docelowym zasobie obliczeniowym. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definiuje ustawienia w celu dostosowania obrazu platformy Docker skompilowanego do specyfikacji środowiska. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | Sekcja konfiguruje ustawienia platformy Spark. Jest on używany tylko wtedy, gdy platforma jest ustawiona na PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfiguruje zależności biblioteki datakosteks. |
| `inferencingStackVersion` | `string` | Określa wersję stosu inferencing dodaną do obrazu. Aby uniknąć dodawania stosu inferencing, pozostaw to pole `null`. Prawidłowa wartość: "Najnowsza". |

## <a name="ml-pipeline-management"></a>Zarządzanie potoku ML

Następujące polecenia pokazują, jak korzystać z potoków uczenia maszynowego:

+ Tworzenie potoku uczenia maszynowego:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml Pipeline Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Aby uzyskać więcej informacji na temat potoku YAML pliku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md).

+ Uruchamianie potoku:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml Run Submit-Pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Aby uzyskać więcej informacji na temat potoku YAML pliku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md).

+ Planowanie potoku:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml potoku Create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Aby uzyskać więcej informacji na temat pliku YAML harmonogramu potoku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Rejestracja modelu, profilowanie, wdrażanie

Poniższe polecenia pokazują, jak rejestrowanie uczonego modelu, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model przy użyciu usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Opcjonalne** Profilowanie modelu pozwala uzyskać optymalne wartości procesora i pamięci na potrzeby wdrożenia.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Wdrażanie modelu w programie AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracji wnioskowania, zobacz [Schemat konfiguracji wnioskowania](#inferenceconfig).
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracji wdrożenia, zobacz [Schemat konfiguracji wdrożenia](#deploymentconfig).

    Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schemat konfiguracji wnioskowania

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia

### <a name="local-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia lokalnego

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia wystąpienia kontenera platformy Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia usługi Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja poleceń dla rozszerzenia interfejsu wiersza polecenia Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Uczenie i wdrażanie modeli uczenia maszynowego przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
