---
title: Jak używać rozszerzenia interfejsu wiersza polecenia usługi Azure Machine Learning
description: Dowiedz się więcej o rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning dla wiersza polecenia platformy Azure. Wiersza polecenia platformy Azure jest narzędziem wiersza polecenia dla wielu platform, która umożliwia pracę z zasobami w chmurze platformy Azure. Rozszerzenie usługi Machine Learning umożliwia pracę z usługą Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 13d09471191deed670db97a9f18e15bc9577dd1a
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713422"
---
# <a name="use-the-azure-machine-learning-cli-extension"></a>Rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning

Interfejsu wiersza polecenia usługi Azure Machine Learning to rozszerzenie [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), interfejs wiersza polecenia dla wielu platform na platformie Azure. To rozszerzenie zawiera polecenia służące do pracy z usługą Azure Machine Learning z poziomu wiersza polecenia. Umożliwia tworzenia skryptów automatyzujących przepływy pracy uczenia maszynowego. Na przykład można utworzyć skrypty, które wykonują następujące czynności:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Zarejestruj modele uczenia maszynowego do użycia przez klientów

+ Pakowanie, wdrażanie i śledzenia w cyklu życia modeli usługi machine learning

Interfejs wiersza polecenia nie jest zamiennikiem dla zestawu SDK usługi Azure Machine Learning. To narzędzie służy do uzupełniające zoptymalizowane pod kątem obsługi wysoce sparametryzowanych zadań, takich jak:

* Tworzenie zasobów obliczeniowych

* przesyłanie eksperymentu sparametryzowane

* Rejestracja modelu

* Tworzenie obrazu

* Wdrażanie usługi

## <a name="prerequisites"></a>Wymagania wstępne

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!NOTE]
> Aby korzystać z interfejsu wiersza polecenia, musi mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie interfejsu wiersza polecenia Machine Learning, użyj następującego polecenia:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Po wyświetleniu monitu wybierz `y` można zainstalować rozszerzenia.

Aby sprawdzić, czy rozszerzenie zostało zainstalowane, użyj następującego polecenia, aby wyświetlić listę określonych ML podpoleceń polecenia:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Aby zaktualizować rozszerzenie, należy najpierw __Usuń__ , a następnie __zainstalować__ go. Spowoduje to zainstalowanie najnowszej wersji.

## <a name="remove-the-extension"></a>Usuń rozszerzenie

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Poniższe polecenia pokazują, jak zarządzać zasoby używane przez usługi Azure Machine Learning przy użyciu interfejsu wiersza polecenia.


+ Utwórz obszar roboczy usługi Azure Machine Learning:

   ```azurecli-interactive
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Ustaw domyślnego obszaru roboczego:

   ```azurecli-interactive
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Tworzenie maszyny wirtualnej DSVM (maszyny Wirtualnej analizy danych). Można również utworzyć klastrów BatchAI dla rozproszonego szkolenia lub klastry usługi AKS na potrzeby wdrożenia.


  ```azurecli-interactive
  az ml computetarget setup dsvm -n mydsvm
  ```

## <a name="experiments"></a>Eksperymenty

Poniższe polecenia pokazują, jak pracować z eksperymentów przy użyciu interfejsu wiersza polecenia:

* Dołączanie projektu (Uruchom konfigurację) przed przesłaniem eksperymentu:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, należy określić obliczeniowego elementu docelowego. W tym przykładzie `local` korzysta z komputera lokalnego do nauczenia modelu, przy użyciu `train.py` skryptu:

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

* Wyświetlanie listy przesłanych eksperymentów:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Rejestracja modelu, tworzenie obrazu i wdrożenie

Poniższe polecenia pokazują, jak rejestrowanie uczonego modelu, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model przy użyciu usługi Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Utwórz obraz, który zawiera Twoje modelu uczenia maszynowego i zależności: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Wdrażanie obrazu obliczeniowego elementu docelowego:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
