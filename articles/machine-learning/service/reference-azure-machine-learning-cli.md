---
title: Rozszerzenie interfejsu wiersza polecenia do uczenia maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning dla wiersza polecenia platformy Azure. Wiersza polecenia platformy Azure jest narzędziem wiersza polecenia dla wielu platform, która umożliwia pracę z zasobami w chmurze platformy Azure. Rozszerzenie usługi Machine Learning umożliwia pracę z usługą Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 35e57dfcc7b1fd6f8de265ab75de29dedd8fdfc2
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501661"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Na użytek rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning

Interfejsu wiersza polecenia usługi Azure Machine Learning to rozszerzenie [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), interfejs wiersza polecenia dla wielu platform na platformie Azure. To rozszerzenie zawiera polecenia służące do pracy z usługą Azure Machine Learning. Umożliwia automatyzowanie działań uczenia maszynowego. Poniższa lista zawiera niektóre akcje przykład, które można wykonać za pomocą rozszerzenie interfejsu wiersza polecenia:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Zarejestruj modele uczenia maszynowego do użycia przez klientów

+ Pakowanie, wdrażanie i śledzenia w cyklu życia modeli usługi machine learning

Interfejs wiersza polecenia nie jest zamiennikiem dla zestawu SDK usługi Azure Machine Learning. Jest wzajemnie dopełniającego się narzędzie, które jest zoptymalizowane pod kątem obsługi wysoce sparametryzowane zadania, które odpowiadały się również do automatyzacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby korzystać z interfejsu wiersza polecenia, musi mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Pełna dokumentacja

Znajdź [pełne dokumenty referencyjne dotyczące rozszerzenia interfejsu wiersza polecenia platformy azure-cli-ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Zainstaluj rozszerzenie

Aby zainstalować rozszerzenie interfejsu wiersza polecenia Machine Learning, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Przykładowe pliki za pomocą poniższych poleceń można znaleźć [tutaj](https://aka.ms/azml-deploy-cloud).

Po wyświetleniu monitu wybierz `y` można zainstalować rozszerzenia.

Aby sprawdzić, czy rozszerzenie zostało zainstalowane, użyj następującego polecenia, aby wyświetlić listę określonych ML podpoleceń polecenia:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Usuń rozszerzenie

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Poniższe polecenia pokazują, jak zarządzać zasoby używane przez usługi Azure Machine Learning przy użyciu interfejsu wiersza polecenia.

+ Jeśli nie masz już jeden, Utwórz grupę zasobów:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Utwórz obszar roboczy usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Dołącz Konfiguracja obszaru roboczego do folderu, aby włączyć rozpoznawanie kontekstowe interfejsu wiersza polecenia.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie umożliwia utworzenie `.azureml` podkatalogu zawierającego pliki przykładowe runconfig i conda środowiska. Zawiera ona także `config.json` pliku, który jest używany do komunikowania się z obszarem roboczym usługi Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [dołączyć az ml folderu](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Dołącz kontenera obiektów blob platformy Azure jako magazynu danych.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Aby uzyskać więcej informacji, zobacz [az ml datastore dołączanie — obiekt blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ Dołącz klaster AKS jako cel obliczenia.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget dołączanie usługi aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Utwórz nowy obiekt docelowy AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget tworzenie amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Uruchamianie eksperymentów

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, należy określić nazwę pliku runconfig (tekst przed \*.runconfig, jeśli patrzy systemu plików) dla parametru - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Polecenie tworzy `.azureml` podkatalogu, która zawiera dwa pliki runconfig przykład. 
    >
    > Jeśli masz skrypt w języku Python, która tworzy obiekt konfiguracji uruchamiania programowo, możesz użyć [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) go zapisać jako plik runconfig.
    >
    > Aby uzyskać więcej plików runconfig przykładu, zobacz [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Aby uzyskać więcej informacji, zobacz [az ml Uruchom Prześlij skrypt](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Wyświetlanie listy eksperymenty:

    ```azurecli-interactive
    az ml experiment list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml eksperymentować listy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Rejestracja modelu, profilowanie i wdrożenie

Poniższe polecenia pokazują, jak rejestrowanie uczonego modelu, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model przy użyciu usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Aby uzyskać więcej informacji, zobacz [rejestru modelu uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **OPCJONALNIE** profilu modelu w taki sposób, aby uzyskać optymalne wartości procesora CPU i pamięci dla wdrożenia.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Aby uzyskać więcej informacji, zobacz [profil modelu uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Wdrażanie modelu w usłudze AKS

    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
    ```

    Poniżej przedstawiono przykład `inferenceconfig.json` dokumentu:

    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```

    Aby uzyskać więcej informacji, zobacz [wdrożyć model uczenia maszynowego az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>Kolejne kroki

* [Polecenie Odwołanie rozszerzenia interfejsu wiersza polecenia Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Nauczanie i wdrażania modeli uczenia maszynowego przy użyciu potoków usługi Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)